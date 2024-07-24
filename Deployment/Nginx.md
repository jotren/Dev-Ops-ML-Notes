# NGINX #

In the virtual machine there is a NGINX config file that informs the reverse proxy what data to present when called upon. This is not crucial learning but have recorded incase we ever need to change the files that are pushed to the API

To open to Nginx configuration file
```
sudo nano /etc/nginx/sites-available/aiforwind.com
```
This will open up the config file which will look like this:
```
server {
    server_name vessel-data-1.aiforwind.com;

    root /var/www/aiforwind.com/vessel-data-1;

    location /files/ {
        autoindex on;
    }

}

```
The root indicates the location of the folder we need to push, location /files/ is the folder that is actually pushed. Autoindex allows the user to parse through the directories

Now if we type the directory
```
http://vessel-data-1.aiforwind.com/files/
```
The user will be taken to the file position specfied. If we ever needed to change the output of the reverse proxy to the port 5000 (where the flask app is operating) can change as below
```
    location / {
        proxy_pass http://localhost:5000;
    }
````     