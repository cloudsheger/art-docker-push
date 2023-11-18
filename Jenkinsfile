pipeline {
    agent any

    environment {
        artifactory_credentials = credentials('ARTIFACTORY_CREDENTIALS_ID')

        /* Define environment variables as needed */
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
                /* This builds the actual image; synonymous to docker build on the command line */
                script {
                    app = docker.build("cloudshegerlab.jfrog.io/cloudsheger-docker/nodetest")
                }
            }
        }

        stage('Test image') {
            steps {
                /* Ideally, we would run a test framework against our image.
                 * For this example, we're using a Volkswagen-type approach ;-) */
                script {
                    app.inside {
                        sh 'echo "Tests passed"'
                    }
                }
            }
        }

        stage('Push image') {
            steps {
                /* Finally, we'll push the image with two tags:
                 * First, the incremental build number from Jenkins
                 * Second, the 'latest' tag.
                 * Pushing multiple tags is cheap, as all the layers are reused. */
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
