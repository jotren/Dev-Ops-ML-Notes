# Virtual Machines

If you are unable to login you need to log into Azure portal and log your IP address:

```lnk
https://portal.azure.com/#@cognitive.business/resource/subscriptions/9725bebb-a690-468b-8c2f-a984f0eb46be/resourcegroups/DataScience/providers/Microsoft.Compute/virtualMachines/Vessel-tracker/overview

```
Then navigate to:

1) Networking Settings tab: Click pon Add inbound Port rule
2) Add information on:
    - Source IP Address -> IP address of working location
    - Service -> SSH
    - Port Range -> 22
    - Protocol -> TCP
    - Action -> Allow
    - Priority -> 370 (this is the prority of commands on the machine)
![Alt text](image.png)

3) Click save, note this can take a few minutes to implement.

## Navigating

There are a could of commands to bear in mind:

```
pm2 list = Seeing what is currently being exectuted
pm2 logs = Display in real time
pm2 restart {id} = Restart based in id
pm2 reload {id} = Reload based in id
pm2 stop {id} = Stop based in id
pm2 start {id} = Start based in id
```
## NGINX


## Setting up Python Enviroment

First we navigate to the folder we want to set up enviroment in. Then we make sure that virtualenv in install on machine:

```
pip3 install virtualenv
```
We then create the virtual enviroment

```
(sudo) /usr/bin/python3 -m virtualenv venv
```
Sometimes we have to insert the path if the system has two versions of python installed. Then we activate the venv by typing: 
```
(sudo) source venv/bin/activate
```
Sometimes this can still call issues with the enviroment. We can instead Dockerise the application.

## Docker

Sometimes you can login via the username/password combo:

```
docker login -u jotren
```
Then..
```
Password: ****************
```

Just to be clear about what Docker is, it enables deployment onto a virtual machine without having to wrestle enviroments on there. Instead, we can upload a container, which is seperate from the operating system, which contains our enviroment to run apis. 

When we are uploading something to a container, we use a Dockerfile to explain what information to include:

```Dockerfile
# Use an official Python runtime as a base image
FROM python:3.8

# Set the working directory in the container, this is NOT the directory related to local or virtual machine, but rather in container
WORKDIR /usr/src/app

# Copy only the files you need
COPY app.py .
COPY merge_info_pdf_processing.py .
COPY requirements.txt .

# Install any needed packages specified in requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# Make port 333 available to the world outside this container
EXPOSE 3333

# Define environment variable to enable Flask to run in production mode
ENV FLASK_ENV=development

# Run app.py when the container launches
CMD ["python", "app.py"]

```
Our requirements.txt file looks something like this:

```
flask
pdfplumber
pandas
pdfminer.six
```
And then we have out flask api, that starts and finishes like this: 

```python
app = Flask(__name__)

@app.route("/pdf-processing", methods=["POST"])
def upload_pdf():

    '''API logic'''

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=3333)

```
It is important that the host is '0.0.0.0' otherwise it cannot be accessed in the container. Then we build the image:

```bash
 docker buildx build -t { name of app i.e python_oil_report_api} .
```
You can check the image has been created with 
```Bash
docker images
```
If you want to test your container is working you can type (also remember to specify port):

```Bash
docker run --detach --publish 3333:3333 { image_name }

```
This will create a container to run the image. If we then want to edit the image and container we can:
```
docker images = see all images
docker ps -a = see all containers
docker rm {container name} = remove container
docker rmi {image name} = remove image
docker stop = stop container running
```

We then need to tag/rename our image so that it can be pushed to the remote repository:
```Bash
docker tag {image name} jotren/{repo name}:latest
docker push jotren/{repo name}:latest
```
Once we push onto the remote repository, we then need to pull down onto local machine:

```Bash
docker pull jotren/{repo name}:latest
```
Once we have on our local machine we then run the container. 

## Docker on Virtual Machine

If we want to install docker on Linux VM it is:

```Bash
sudo apt-get install docker 
```
If we want to check that our Docker Container is working we can use curl:
```
curl -X POST -F "file=@/var/www/aiforwind.com/dev.oil-analysis/test_files/test.pdf" http://127.0.0.1:3333/pdf-processing
```
The command will work like Postman and post a file at a specified path to the API endpoint.