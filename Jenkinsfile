pipeline {
    agent any

    environment {
        IMAGE_NAME = "mriganka11/myapp:${BUILD_NUMBER}"
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

        stage('Debug Workspace') {
            steps {
                bat "dir"
                bat "dir /s"
            }
        }


        stage('Update Deployment Image') {
            steps {
                bat """
                powershell -Command "(Get-Content k8s/deployment.yaml) -replace 'image:.*', 'image: %IMAGE_NAME%' | Set-Content k8s/deployment.yaml"
                """
            }
        }

        sstage('Deploy to Kubernetes') {
            steps {
                bat "kubectl apply -f k8s/deployment.yaml"
                bat "kubectl rollout restart deployment myapp"
            }
        }

    }
}
