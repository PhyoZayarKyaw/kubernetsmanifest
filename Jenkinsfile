pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
metadata:
  name: alpine-pod
spec:
  containers:
  - name: alpine-container
    image: alpine:latest
    imagePullPolicy: Always
"""
    }

    environment {
        DOCKERTAG = "latest" // Or set this dynamically if needed
    }

    stages {
        stage('Clone repository') {
            steps {
                checkout scm
            }
        }

        stage('Update GIT') {
            steps {
                script {
                    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                        withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                            // Configure Git with username and email
                            sh "git config user.email phyozayarkyaw2018@gmail.com"
                            sh "git config user.name PhyoZayarKyaw"
                            
                            // Display the current deployment.yaml content
                            sh "cat deployment.yaml"
                            
                            // Update the Docker image tag in the deployment.yaml file
                            sh "sed -i'' 's+phyozayarkyaw/test.*+phyozayarkyaw/test:${DOCKERTAG}+g' deployment.yaml"
                            
                            // Display the updated deployment.yaml content
                            sh "cat deployment.yaml"
                            
                            // Add, commit, and push changes
                            sh "git add ."
                            sh "git commit -m 'Done by Jenkins Job changemanifest: ${env.BUILD_NUMBER}'"
                            sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/kubernetesmanifest.git HEAD:master"
                        }
                    }
                }
            }
        }
    }
}

