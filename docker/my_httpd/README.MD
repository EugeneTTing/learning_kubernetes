## Image Description

[Docker Hub Link](https://hub.docker.com/r/eugeneting/example-httpd)

Based on the official Apache HTTP Server image. The `init.sh` script is copied into the image and made executable. The script is run which overwrites the default `index.html` file of the base image. The script is run in the container so the `$(hostname)` environmental variable correctly reflects the name of the running container. The `apachectl` process is run in the foreground to prevent the container from exiting immediately after the completion of the shell script. 
