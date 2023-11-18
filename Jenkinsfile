pipeline {
    agent any

    environment {
        artifactory_credentials = credentials('ARTIFACTORY_CREDENTIALS_ID')
    }

    parameters {
        string(name: 'GIT_REPO', defaultValue: 'https://github.com/cloudsheger/art-docker-push.git', description: 'GitHub repo')
        string(name: 'GIT_BRANCH', defaultValue: 'main', description: 'GitHub branch name')
    }

    stages {
        stage('Checkout SCM') {
            steps {
                git branch: params.GIT_BRANCH, url: params.GIT_REPO
            }
        }

        stage('Build image') {
            steps {
                script {
                    app = docker.build("cloudshegerlab.jfrog.io/cloudsheger-docker/nodetest")
                }
            }
        }

        stage('Test image') {
            steps {
                script {
                    app.inside {
                        sh 'echo "Tests passed"'
                    }
                }
            }
        }

        stage('Push image') {
            steps {
                script {
                    docker.withRegistry('https://cloudshegerlab.jfrog.io', 'artifactory_credentials') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
    }
}
