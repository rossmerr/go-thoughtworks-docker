# go-thoughtworks-docker

A docker file with the go-agent setup with vnext ready to do builds, change the GO_SERVER=10.0.2.14 variable to point to your go server

docker run -d -p 5000:5000 -e GO_SERVER=10.0.2.14 --restart=always --privileged=true --name go-agent travcoltd/go-thoughtworks-docker:latest


Some Notes fomr myself
-v /var/run/docker.sock:/var/run/docker.sock
