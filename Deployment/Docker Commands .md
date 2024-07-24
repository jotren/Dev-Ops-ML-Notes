#  Docker #
Cognitive didn't end up using Docker, but it still could be a powerfull tool

Visualise current Docker images
```
docker images
```
In order to build a docker container you need the program, the Docker file and the requirements.txt. The program is what you want to run in Docker, the Docker file is number of commands to tell Docker:

- The version of python
- The direcory
- The where to find requirements

If these files are all in order you can got to the directory in Powershell and run the command
```
docker build -t "app_test.py" . 
```
__Tips__ 

- Docker does not like capitals in the files names)
- Make sure the Docker file does not have the extension .txt, the programme will not run

If you would like to rename any files quickly, you can use the bash command
```
Rename-Item [file name] [new name]
```

When we push the container into the Docker Hub need to make sure the name of the container matches the registry in the hub i.e jotren/app

To change the name you can use the below command
```
docker tag a0b jotren/app:latest
```
Push the container to the hub using
```
docker push jotren/app
```

We then move onto visual studio code and look at the registries for docker. Will be an option to deploy omage to container instances

The programme will then be available to view on the individual containers

When you push a programme your docker interface will then move into that container. In order to move out of the container use:
```
docker context use default
```
IF you want to remove an image from the hub use the command
```
docker rmi e4c    (where the e4c is the first three letters of the image ID)
```
In order to get the print function working we need to add a "-u" to the CMD line in the DockerFile
```
CMD ["python", "-u", "./vessel_data_pull.py"]
```
Good tip if you want to remove all docker conatiners, images, volumes etc:
```
docker system prune
```


