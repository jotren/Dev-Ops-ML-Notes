# Search Bar

When creating a search bar we have options depending on scale. In both cases we would need to duplicate the database so that can be queried:

1) __Small - Meduim Scale SQLite (1000s - 100,000s rows)__: SQLite has built in Full Text Search (FTS). SQLite will duplicate the table in a __virtual table__ and use smart algorithmns to query this. 
2) __Large Scale Elastic Search (1,000,000s rows)__: ElasticSearch is a package you can download to perform search queries. Duplicate the database in a way that allows for smart querying, run the ElasticSearch API on a seperate port to database and use this to search items.

## Small Scale SQLite FTS

First we create a virtual table that SQLite can parse through:

```SQL
CREATE VIRTUAL TABLE search_index USING fts4( -- name the table search-index
    rowid INTEGER,            
    report_comments TEXT COLLATE NOCASE,    --NOCASE tells SQL this column is case insensitive
    report_date TEXT COLLATE NOCASE, 
    report_name TEXT COLLATE NOCASE,
    asset_name TEXT COLLATE NOCASE,
    system_name TEXT COLLATE NOCASE,
    tokenize=porter                         --Tells SQL that the tokenizer as porter for stemming, a popular stemming technique
);

```
Then we push all the relevant data into this table using JOINS etc.:

```SQL
-- Insert data from report_entity, including relevant data from asset_entity and system_entity
INSERT INTO search_index (rowid, report_comments, report_date, report_name, asset_name, system_name)
SELECT
    r.report_id,
    r.comments,
    r.date,
    r.name,
    a.name AS asset_name,
    s.name AS system_name
FROM report_entity r
LEFT JOIN asset_entity a ON r.assetAssetId = a.asset_id
LEFT JOIN system_entity s ON r.systemsSystemId = s.system_id
ORDER BY r.rowid; -- Order by the rowid for consistent ordering
```
Once we have the pushed the data into the search-index we can then query using this: 

```SQL
SELECT * FROM search_index WHERE search_index MATCH 'E7|blade|yaw';
```
If we are using a node application we can query the database using scripts like this: 

```Typescript
    async searchByQuery(searchQuery: string) {
        const splitStings = searchQuery.replace(/-/g, " OR ")
        const results = await this.reportRepository.query(
          `SELECT * FROM search_index WHERE search_index MATCH '${splitStings}';`,
        );
        return results;
    }
```
Now this approach is lightweight and easy to implement, but requires perfect spelling from the user if it is only one word.

## Small Scale PostGresSQL

PostgreSQL has better infrastructure for querying, things like fuzzy searching and levensctein, but has more overhead. To get PostgresSQL set up you need to download it and open up __pgAdmin__. Passwords for PG super user is:

```
username: postgres
password: Motorstorm!
```
When we are setting up the database we need to do this first in pdAdmin:

1) Right click Postgres16 and click on Create... -> Database
2) Create a password and username for the database
3) In Node application database factory set up this: 

```Typescript
case "postgres":
    return new DataSource({
        type: "postgres",
        host: "localhost",
        port: 5432, // Default PostgreSQL port
        username: process.env.dbusername,
        password: process.env.dbpassword,
        database: process.env.dbname,
        synchronize: true, // Set to false in production
        logging: false,
        entities: entities,
        migrations: [],
        subscribers: [],
                });
            
```
Set up enviroment file:

```Typescript
dbengine=postgres
dbname=oil-analysis
dbusername=postgres
dbpassword=motorstorm1!
```
4) Run application and check tables to see if database has been integrated.

Then in order to create a virtual searchable table run these commands

```SQL 
-- Create virtual table

CREATE TABLE report_fts (
    report_id integer PRIMARY KEY,
    report_tsvector tsvector
);

-- Insert relevant columns into table
INSERT INTO report_fts (report_id, report_tsvector)
SELECT re.report_id, 
       to_tsvector('english', 
           COALESCE(re.report_id::text, '') || ' ' ||
           COALESCE(re.name, '') || ' ' ||
           COALESCE(re.comments, '') || ' ' ||
           COALESCE(a.name, '') || ' ' ||  -- Left join asset_entity for asset name
           COALESCE(s.name, '')            -- Left join system_entity for system name
       )
FROM report_entity re
LEFT JOIN asset_entity a ON re."assetAssetId" = a."asset_id"
LEFT JOIN system_entity s ON re."systemsSystemId" = s."system_id";

-- Create searchable index in table

CREATE INDEX report_fts_text_search_idx ON report_fts USING gin(report_tsvector);

-- Query table

SELECT r.comments, r.name, r.date, a.name, s.name
FROM report_entity r
JOIN (
  SELECT report_id
  FROM report_fts
  WHERE report_tsvector @@ to_tsquery('english', 'fall')
  ORDER BY ts_rank(report_tsvector, to_tsquery('par')) DESC
  LIMIT 5
) subquery ON r.report_id = subquery.report_id
JOIN asset_entity a ON r."assetAssetId" = a."asset_id"
JOIN system_entity s ON r."systemsSystemId" = s."system_id";

```
Need to do more research on fuzzy searching.

## Large Scale Elasticsearch

This can require a few more steps to set up and implement. First we need to install java, as this runs on a java enviroment. You follow this link to download:

https://www.oracle.com/java/technologies/downloads/#jdk21-windows

Once Java is installed you download the ElasticSearch:

https://www.elastic.co/downloads/elasticsearch

This is a folder containing all the relevant set up for Elasticsearch. You unzip the file and save on local machine. We are mostly interested in a file called elasticsearch.yml which is in the config folder. THese are the elements we need to configure:

```YAML
# ---------------------------------- Cluster -----------------------------------
#
# Use a descriptive name for your cluster:
#
cluster.name: my-application
#
# ------------------------------------ Node ------------------------------------
#
# Use a descriptive name for the node:
#
node.name: node-1
#
# Add custom attributes to the node:
#
#node.attr.rack: r1
#
# ----------------------------------- Paths ------------------------------------
#
# Path to directory where to store the data (separate multiple locations by comma):
#
path.data: C:\projects\node\oil-analysis-backend\elasticsearch\data
#
# Path to log files:
#
path.logs: C:\projects\node\oil-analysis-backend\elasticsearch\logs
#
```
Which is where we configure all the information around data, logs and ports. And here, where we configure whether it is HTTP or HTTPS:

```YAML
#----------------------- BEGIN SECURITY AUTO CONFIGURATION -----------------------
#
# The following settings, TLS certificates, and keys have been automatically      
# generated to configure Elasticsearch security features on 30-11-2023 15:23:18
#
# --------------------------------------------------------------------------------

# Enable security features
xpack.security.enabled: false # false -> http / true -> https

xpack.security.enrollment.enabled: false # false -> http / true -> https

# Enable encryption for HTTP API client connections, such as Kibana, Logstash, and Agents
xpack.security.http.ssl:
  enabled: false # false -> http / true -> https
  keystore.path: certs/http.p12

# Enable encryption and mutual authentication between cluster nodes
xpack.security.transport.ssl:
  enabled: false # false -> http / true -> https
  verification_mode: certificate
  keystore.path: certs/transport.p12
  truststore.path: certs/transport.p12
# Create a new cluster with the current node only
# Additional nodes can still join the cluster later
cluster.initial_master_nodes: ["LAPTOP-0NU83HDB"]

# Allow HTTP API connections from anywhere
# Connections are encrypted and require user authentication
http.host: 0.0.0.0

# Allow other nodes to join the cluster from anywhere
# Connections are encrypted and mutually authenticated
#transport.host: 0.0.0.0

#----------------------- END SECURITY AUTO CONFIGURATION -------------------------
```
Once we have configured that we navigate to in our terminal to the location of the saved elastic unzipped files and run the command:

```Bash
bin\elasticsearch
```
This will run the elastic search program, there will be lots of scripts running, but essentially you will know if it is working because you can go to local host 9200 and you would see this returned.

```JSON
// http://localhost:9200/

{
  "name" : "node-1",
  "cluster_name" : "my-application",
  "cluster_uuid" : "_na_",
  "version" : {
    "number" : "8.11.1",
    "build_flavor" : "default",
    "build_type" : "zip",
    "build_hash" : "6f9ff581fbcde658e6f69d6ce03050f060d1fd0c",
    "build_date" : "2023-11-11T10:05:59.421038163Z",
    "build_snapshot" : false,
    "lucene_version" : "9.8.0",
    "minimum_wire_compatibility_version" : "7.17.0",
    "minimum_index_compatibility_version" : "7.0.0"
  },
  "tagline" : "You Know, for Search"
}

```
