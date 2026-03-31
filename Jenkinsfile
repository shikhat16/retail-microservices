pipeline {
    agent {
        kubernetes {
            inheritFrom 'maven-agent'
            defaultContainer 'maven'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
    }
}
