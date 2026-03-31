pipeline {
    agent {
        kubernetes {
            label 'maven'
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
