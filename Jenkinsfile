pipeline {
    agent any
    environment {
        APP_NAME = "test-pipeline"
    }

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/mrdevops12/gitops-register-app-test.git'
            }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                   echo '--- Before Update ---'
                   cat deployment.yaml

                   # Correct sed pattern to update the image tag
                   sed -i "s|rajuvarma/register-app-pipeline:.*|rajuvarma/register-app-pipeline:${IMAGE_TAG}|g" deployment.yaml

                   echo '--- After Update ---'
                   cat deployment.yaml
                """
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                sh """
                   git config --global user.name "mrdevops12"
                   git config --global user.email "mrdevops1@gmail.com"
                   git add deployment.yaml || true  # Continue even if no changes
                   git commit -m "Updated Deployment Manifest" || echo 'No changes to commit'
                """
                withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                    sh "git push https://github.com/mrdevops12/gitops-register-app-test.git main"
                }
            }
        }
    }
}
