pipeline {
    agent { 
        label { label 'built-in' }      // This is the master node that has been labeled with 'built-in'
    }                                   // Equivalent to agent none, as it doesn't have any slaves to pick
    
    stages {
        stage('Using DinD image') {
            agent { 
                docker {
                    alwaysPull false
                    image 'viejo/dind4jenkins:alpine_3_12_master'
                    args '-u 1000:998 -v /var/run/docker.sock:/var/run/docker.sock'
                    }
                }
            stages {
                stage ('version-maven') {
                    agent { 
                        docker { 
                            image 'maven:3.8.6-openjdk-11-slim'
                            } 
                        }
                    steps {
                        sh 'mvn --version'
                    }
                }
                stage('version-nodejs') {
                    agent { 
                        docker { 
                            image 'node:16.17.1-alpine'
                            }
                        }
                    steps {
                        sh 'node --version'
                    }
                }
                stage('version-ruby') {
                    agent {
                        docker {
                            image 'ruby:3.1.2-alpine'
                        }
                    }
                    steps {
                        sh 'ruby --version'
                    }
                }
                stage('version-python') {
                    agent {
                        docker {
                            image 'python:3.10.7-alpine'
                        }
                    }
                    steps {
                        sh 'python --version'
                    }
                }
                stage('version-php') {
                    agent {
                        docker {
                            image 'php:8.1.11-alpine'
                        }
                    }
                    steps {
                        sh 'php --version'
                    }
                }
                stage('version-golang') {
                    agent {
                        docker {
                            image 'golang:1.19.1-alpine'
                        }
                    }
                    steps {
                        sh 'go version'
                    }
                }
            }
        }
    }
}