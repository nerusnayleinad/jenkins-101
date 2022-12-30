#### jenkins-101

Build Jenkins pipeline in a docker container, with DinD stage from which it launches all stages within other docker containers (agent docker)

This example contains a base Jenkinsfile from which it is necessary to modify the stages that prints the versions of different software.

These are the steps to have a functioning Jenkins pipeline

+ Create jenkins custom docker image with the Dockerfile_jenkins.
```
docker build -t viejo/jenkins:docker (-f Dockerfile-jenkins) .

```

+ Create Docker in Docker image for jenkins with Dockerfile_dind_jenkins. This image contains AWS cli and GCP SDK. Remove any package that is not necessary.
```
docker build -t viejo/dind4jenkins:alpine_3_12_master (-f Dockerfile-dind_jenkins_master) .
```

Note: This image works well when there is one Jenkins instance (master) with no slaves as it shares the same UID as the master so it can share the same local directory on the host.

+ On the host machine, create /srv/jenkins directory and give it the right permissions.
```
sudo adduser --disabled-password -u 1000 --shell /bin/bash jenkins
sudo mkdir /srv/jenkins
sudo chown -R jenkins:jenkins /srv/jenkins
```

+ Run Jenkins
```
docker run -d -p 80:8080 -p 50000:50000 -v /var/run/docker.sock:/var/run/docker.sock -v /srv/jenkins/data/jenkins_home:/var/jenkins_home --name jenkins viejo/jenkins:docker
```

Note: the Docker in Docker image will be launched by the master instance.
Note: With the Jenkinsfile the master node needs to have the following:
+ labeled with built-in
+ number of executors at least 4

####