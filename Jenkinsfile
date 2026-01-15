pipeline {
    agent any

    environment {
        DOCKER_HOST_IP = "3.106.246.219"
        SSH_USER = "ec2-user"
        IMAGE_NAME = "myapp"
    }

    stages {

        stage('Clone Repo') {
            steps {
                git 'https://github.com/Shubham-3177/jenkins-docker-setup.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $IMAGE_NAME .
                '''
            }
        }

        stage('Send Image to Docker Server') {
            steps {
                sh '''
                docker save $IMAGE_NAME | ssh $SSH_USER@$DOCKER_HOST_IP docker load
                '''
            }
        }

        stage('Install Docker on Target (if needed)') {
            steps {
                sh '''
                ssh $SSH_USER@$DOCKER_HOST_IP "
                sudo yum install docker -y || true
                sudo systemctl start docker
                sudo systemctl enable docker
                sudo usermod -aG docker ec2-user
                "
                '''
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                ssh $SSH_USER@$DOCKER_HOST_IP "
                docker rm -f myapp || true
                docker run -d -p 80:80 --name myapp $IMAGE_NAME
                "
                '''
            }
        }
    }
}

