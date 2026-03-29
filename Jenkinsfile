pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            agent {
                docker {
                    image 'maven:3.9.6-eclipse-temurin-17'
                    args '-v /root/.m2:/root/.m2'
                }
            }
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'maven:3.9.6-eclipse-temurin-17'
                    args '-v /root/.m2:/root/.m2'
                }
            }
            steps {
                sh 'mvn test'
            }
        }
        stage('Docker Build') {
            agent {
                docker {
                    image 'docker:27.2.0'
                    args '--privileged -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                sh "docker build -t shikhat651/retail-service:${BRANCH_NAME}-${BUILD_NUMBER} ."
            }
        }
        stage('Push Image') {
            agent {
                docker {
                    image 'docker:27.2.0'
                    args '--privileged -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh "docker push shikhat651/retail-service:${BRANCH_NAME}-${BUILD_NUMBER}"
                }
            }
        }
        stage('Deploy to Kubernetes') {
            when {
                branch 'main'
            }
            agent {
                docker {
                    image 'bitnami/kubectl:latest'
                    args '-v /root/.kube:/root/.kube'
                }
            }
            steps {
                sh 'kubectl apply -f k8s/deployment.yaml'
            }
        }
    }
}

