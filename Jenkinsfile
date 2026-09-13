pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Package') {
            steps {
                sh 'tar -czf webapp.tar.gz webserver1.html webserver2.html'
            }
        }

        stage('Verify Package') {
            steps {
                sh 'ls -lh webapp.tar.gz'
            }
        }
    }
}
