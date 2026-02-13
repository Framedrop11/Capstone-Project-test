pipeline {
    agent any

    environment {
        IMAGE_NAME = "yourdockerhub/myapp:${BUILD_NUMBER}"
    }

    stages {

        stage('Build Image') {
            steps {
                bat "docker build -t %IMAGE_NAME% ."
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    bat """
                        docker login -u %USER% -p %PASS%
                        docker push %IMAGE_NAME%
                    """
                }
            }
        }

        stage('Update Deployment Image') {
            steps {
                bat """
                powershell -Command "(Get-Content k8s/deployment.yaml) -replace 'image:.*', 'image: %IMAGE_NAME%' | Set-Content k8s/deployment.yaml"
                """
            }
        }

        stage('Deploy via Ansible (WSL)') {
            steps {
                bat "wsl ansible-playbook ansible/deploy.yml"
            }
        }
    }
}
