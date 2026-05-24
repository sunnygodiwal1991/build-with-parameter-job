pipeline {
    agent any

    environment {
        APP_NAME = "my-app"
        DEV_SERVER = "dev.example.com"
        PROD_SERVER = "prod.example.com"
    }

    triggers {
        githubPush()
    }

    stages {

        stage('Checkout') {
            steps {
        
                git(
                    branch: 'dev',
                    credentialsId: 'parameter-token',
                    url: 'https://github.com/sunnygodiwal1991/build-with-parameter-job.git'
                )
        
            }
        }

        stage('Detect Branch') {
            steps {
                script {

                    // branch name detect
                    def branch = env.BRANCH_NAME

                    echo "Current Branch: ${branch}"

                    // ENV decide based on branch
                    if (branch == "main") {
                        env.DEPLOY_ENV = "prod"
                    }
                    else if (branch == "develop") {
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
                expression { env.DEPLOY_ENV != "none" }
            }

            steps {
                echo "Building application..."

                sh '''
                    echo "Running build..."
                    # Example:
                    # npm install
                    # npm run build
                '''
            }
        }

        stage('Deploy to DEV') {
            when {
                expression { env.DEPLOY_ENV == "dev" }
            }

            steps {
                echo "Deploying to DEV Server"

                sh """
                    echo "Deploying ${APP_NAME} to ${DEV_SERVER}"

                    # Example deployment commands

                    # scp -r build/* user@${DEV_SERVER}:/var/www/app/

                    # ssh user@${DEV_SERVER} '
                    #   cd /var/www/app &&
                    #   docker compose restart
                    # '
                """
            }
        }

        stage('Deploy to PROD') {
            when {
                expression { env.DEPLOY_ENV == "prod" }
            }

            steps {

                // optional manual approval
                input message: 'Deploy to Production?', ok: 'Deploy'

                echo "Deploying to PROD Server"

                sh """
                    echo "Deploying ${APP_NAME} to ${PROD_SERVER}"

                    # Example deployment commands

                    # scp -r build/* user@${PROD_SERVER}:/var/www/app/

                    # ssh user@${PROD_SERVER} '
                    #   cd /var/www/app &&
                    #   docker compose restart
                    # '
                """
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
