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

        stage('Upload Artifact') {
            steps {
                sh 'aws s3 cp webapp.tar.gz s3://jenkins-nginx-deploy-162331/webapp.tar.gz'
            }
        }
    }
}
