pipeline {
    agent any

    environment {
        DOCKER_USER = "bromaaascripts"
        IMAGE = "${DOCKER_USER}/insurance-app"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Cloud-savvy/InsureMe'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build and Push') {
            steps {
                script {
                    def tag = "${env.BUILD_NUMBER}"
                    sh "docker build -t $IMAGE:$tag ."

                    withCredentials([string(credentialsId: 'docker-cred', variable: 'dockerhubpasswd')]) {
                        sh "echo ${dockerhubpasswd} | docker login -u ${DOCKER_USER} --password-stdin"
                        sh "docker push $IMAGE:$tag"
                        sh "docker tag $IMAGE:$tag $IMAGE:latest"
                        sh "docker push $IMAGE:latest"
                    }
                }
            }
        }

        stage('Deploy to ansible-worker') {
            steps {
                sh 'ansible-playbook -i hosts.ini ansible-playbook.yml'
            }
        }
    }
}
