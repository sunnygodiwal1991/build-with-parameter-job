pipeline {
    agent any

    environment {
        APP_NAME     = "my-app"
        DEV_SERVER   = "dev.example.com"
        STAGE_SERVER = "stage.example.com"
        PROD_SERVER  = "prod.example.com"
    }

    stages {

        stage('Checkout') {
            steps {

                // Multibranch pipeline SCM checkout
                checkout scm

            }
        }

        stage('Detect Branch') {
            steps {
                script {

                    echo "Current Branch: ${env.BRANCH_NAME}"

                    if (env.BRANCH_NAME == "main") {

                        env.DEPLOY_ENV = "prod"

                    }
                    else if (
                        env.BRANCH_NAME == "stage" ||
                        env.BRANCH_NAME == "staging"
                    ) {

                        env.DEPLOY_ENV = "stage"

                    }
                    else if (
                        env.BRANCH_NAME == "dev" ||
                        env.BRANCH_NAME == "develop"
                    ) {

                        env.DEPLOY_ENV = "dev"

                    }
                    else {

                        env.DEPLOY_ENV = "none"
                    }

                    echo "Deployment Environment: ${env.DEPLOY_ENV}"
                }
            }
        }

        stage('Build') {
            when {
                expression {
                    env.DEPLOY_ENV != "none"
                }
            }

            steps {

                echo "Building application..."

                sh '''
                    echo "Running build..."
                    # npm install
                    # npm run build
                '''
            }
        }

        stage('Deploy DEV') {
            when {
                expression {
                    env.DEPLOY_ENV == "dev"
                }
            }

            steps {

                echo "Deploying to DEV Server: ${DEV_SERVER}"

                sh '''
                    echo "DEV deployment started..."
                '''
            }
        }

        stage('Deploy STAGE') {
            when {
                expression {
                    env.DEPLOY_ENV == "stage"
                }
            }

            steps {

                echo "Deploying to STAGE Server: ${STAGE_SERVER}"

                sh '''
                    echo "STAGE deployment started..."
                '''
            }
        }

        stage('Deploy PROD') {
            when {
                expression {
                    env.DEPLOY_ENV == "prod"
                }
            }

            steps {

                input message: 'Deploy to Production?', ok: 'Deploy'

                echo "Deploying to PROD Server: ${PROD_SERVER}"

                sh '''
                    echo "PROD deployment started..."
                '''
            }
        }
    }

    post {

        success {
            echo "Pipeline completed successfully"
        }

        failure {
            echo "Pipeline failed"
        }
    }
}
