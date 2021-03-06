# Wichtiges zu Docker


<img src="/Bilder/Bild3.jpg" alt="Check"/>


### Dockerfile
#### Was ist ein Dockerfile?
In Dockerfile kann man Befehle definieren, anhand von diesen Befehlen werden dann entsprechend ein Image erstellt.

Hier befindet sich eine kleine Ansammlung von Docker Befehlen

| Vagrant Befehle        |Beschreibung                      |
| :--------------------  | :-----------                    |
| Docker Version          | Version Information anzeigen        |                        
| Docker pull ...        | Für die Verwaltung der Dockerumgebung        |
| Docker run image ...         | Starten von Docker image        |
| Docker stop container ...         | Docker stoppen        |
| Docker rm container ... / docker rmi image ...         | Docker File löschen        |
| Docker status         | Statusinformationen zu Dockerimage anzeigen        |


## Image Commands Benutzung

``` 

Commands:
  build       Build an image from a Dockerfile
  history     Show the history of an image
  import      Import the contents from a tarball to create a filesystem image
  inspect     Display detailed information on one or more images
  load        Load an image from a tar archive or STDIN
  ls          List images
  prune       Remove unused images
  pull        Pull an image or a repository from a registry
  push        Push an image or a repository to a registry
  rm          Remove one or more images
  save        Save one or more images to a tar archive (streamed to STDOUT by default)
  tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE

Run 'docker image COMMAND --help' for more information on a command.
``` 
docker pull IMAGENAME --> holt image
docker image --> zeigt heruntergeladene Images an
docker run -d -t --name ContainerName ImageName --> Container mit bestimmten Namen starten
docker container exec -it ContainerName /bin/bash --> geht in VM

Alles weitere zu commands hier. https://docs.docker.com/engine/reference/commandline/docker/

<img src="/Bilder/Bild4.jpg" alt="Check"/>