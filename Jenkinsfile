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

        stage('Deploy to Ubuntu') {
            steps {
                sh '''
                COMMAND_ID=$(aws ssm send-command \
                  --region eu-west-1 \
                  --instance-ids i-0ff4e56bee030a8ae \
                  --document-name "AWS-RunShellScript" \
                  --parameters 'commands=[
                    "aws s3 cp s3://jenkins-nginx-deploy-162331/webapp.tar.gz /tmp/webapp.tar.gz",
                    "tar -xzf /tmp/webapp.tar.gz -C /tmp",
                    "sudo cp /tmp/webserver1.html /var/www/html/webserver1.html",
                    "sudo systemctl restart nginx"
                  ]' \
                  --query 'Command.CommandId' \
                  --output text)

                echo "Ubuntu SSM Command ID: $COMMAND_ID"

                aws ssm wait command-executed \
                  --region eu-west-1 \
                  --command-id "$COMMAND_ID" \
                  --instance-id i-0ff4e56bee030a8ae

                echo "Ubuntu deployment completed successfully."
                '''
            }
        }

        stage('Deploy to Amazon Linux') {
            steps {
                sh '''
                COMMAND_ID=$(aws ssm send-command \
                  --region eu-west-1 \
                  --instance-ids i-0c1fe4c21ee5d6553 \
                  --document-name "AWS-RunShellScript" \
                  --parameters 'commands=[
                    "aws s3 cp s3://jenkins-nginx-deploy-162331/webapp.tar.gz /tmp/webapp.tar.gz",
                    "tar -xzf /tmp/webapp.tar.gz -C /tmp",
                    "sudo cp /tmp/webserver2.html /usr/share/nginx/html/webserver2.html",
                    "sudo systemctl restart nginx"
                  ]' \
                  --query 'Command.CommandId' \
                  --output text)

                echo "Amazon Linux SSM Command ID: $COMMAND_ID"

                aws ssm wait command-executed \
                  --region eu-west-1 \
                  --command-id "$COMMAND_ID" \
                  --instance-id i-0c1fe4c21ee5d6553

                echo "Amazon Linux deployment completed successfully."
                '''
            }
        }
    }
}
