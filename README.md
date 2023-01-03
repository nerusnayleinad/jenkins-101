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

#### Master-Slave

For master-slave configuration, create a slave node as follows:
+ Create an SSH key pair.
  + $ ssh-keygen -C jenkins -f ~/.ssh/jenkins_slave
  + Create Credentials with the private key*
+ Launch the agent (slave node)
  + (1) ```$ docker run -d --name=jenkins-slave -v /srv/jenkins-slave/agent:/home/jenkins -p 22:22 -e "JENKINS_AGENT_SSH_PUBKEY=ssh-rsa ... jenkins" jenkins/ssh-agent:alpine```
  + (2) ```$ docker run -d --name=jenkins-slave -v /srv/jenkins-slave/agent:/home/jenkins -p 22:22 viejo/jenkins-agent:docker```
+ Manage Jenkins > Manage Nodes and Clouds
  + New Node > Name (e.g. jenkins-slave) > Permanent Agent (checked)
    + Number of executors: 2 (optional)
    + Remote root directory: /home/jenkins
    + Labels: jenkins-slave (optional)
    + Launch method: Launch agents via SSH
      + Host: a.b.c.d
      + Credentials (SSH credentials with Username "jenkins")*
      + Host Key Verification Strategy: Manually trusted key Verification Strategy

Note: The Credential for adding an agent must be with user "jenkins", even though the user when creating the key (with ssh-keygen), is differnet.

Registros relacionados:
  + viejo/jenkins:docker -> master
  + viejo/dind4jenkins:alpine_3_12_master -> para creater stages dentro de containers de docker.