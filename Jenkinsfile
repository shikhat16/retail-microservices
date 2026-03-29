pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: maven
    image: maven:3.9.6-eclipse-temurin-17
    command:
    - cat
    tty: true
  - name: docker
    image: docker:27.2.0-dind
    securityContext:
      privileged: true
    command:
    - cat
    tty: true
  - name: kubectl
    image: bitnami/kubectl:latest
    command:
    - cat
    tty: true
"""
        }
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                container('maven') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }
        stage('Test') {
            steps {
                container('maven') {
                    sh 'mvn test'
                }
            }
        }
        stage('Docker Build') {
            steps {
                container('docker') {
                    sh "docker build -t shikhat651/retail-service:${BRANCH_NAME}-${BUILD_NUMBER} ."
                }
            }
        }
        stage('Push Image') {
            steps {
                container('docker') {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                        sh "docker push shikhat651/retail-service:${BRANCH_NAME}-${BUILD_NUMBER}"
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            when {
                branch 'main'
            }
            steps {
                container('kubectl') {
                    sh 'kubectl apply -f k8s/deployment.yaml'
                }
            }
        }
    }
}

