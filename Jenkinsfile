pipeline {
    agent any

    parameters {
        string(name: 'repo_url', defaultValue: '', description: 'URL of the Git repository')
        string(name: 'branch', defaultValue: 'main', description: 'Branch to checkout')
        string(name: 'username', defaultValue: '', description: 'GitHub Username (owner)')
    }

    stages {
        stage('Checkout Source') {
            steps {
                script {
                    checkout([
                        $class: 'GitSCM', 
                        branches: [[name: params.branch]], 
                        userRemoteConfigs: [[url: params.repo_url, credentialsId: 'github-app-credentials']]
                    ])
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    if (fileExists('Dockerfile')) {
                        // Build Docker image from Dockerfile
                        sh "docker build -t ${params.username}/my-app ."
                    } else {
                        // Fallback to Buildpack
                        sh "pack build ${params.username}/my-app --builder gcr.io/buildpacks/builder:v1"
                    }
                }
            }
        }

        // stage('Push Docker Image') {
        //     steps {
        //         withAWS(region: 'us-west-2', credentials: 'ecr-credentials') {
        //             script {
        //                 // Authenticate Docker to the ECR registry
        //                 sh "eval \$(aws ecr get-login --no-include-email --region us-west-2)"

        //                 // Tag the Docker image
        //                 sh "docker tag ${params.username}/my-app:latest ${ECR_REPOSITORY}/${params.username}/my-app:latest"

        //                 // Push the Docker image
        //                 sh "docker push ${ECR_REPOSITORY}/${params.username}/my-app:latest"
        //             }
        //         }
        //     }
        // }
    }
}
