pipeline {

    agent any

    options {

        buildDiscarder(
            logRotator(
                numToKeepStr: '5'
            )
        )

        disableConcurrentBuilds()

        timeout(time: 15, unit: 'MINUTES')

        timestamps()

        ansiColor('xterm')

        skipDefaultCheckout(true)
    }

    environment {

        APP_NAME               = "my-app"

        DEV_SERVER             = "dev.example.com"
        DEV_SERVER_ENDPOINT    = "https://dev.example.com/health"

        STAGE_SERVER           = "stage.example.com"
        STAGE_SERVER_ENDPOINT  = "https://stage.example.com/health"

        PROD_SERVER            = "prod.example.com"
        PROD_SERVER_ENDPOINT   = "https://prod.example.com/health"

        APPROVER_NAME          = "Sunny"
    }

    stages {

        stage('Cleanup Workspace') {

            steps {

                cleanWs()
            }
        }

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

                    if (
                        env.BRANCH_NAME == "main" ||
                        env.BRANCH_NAME == "master" ||
                        env.BRANCH_NAME == "prod"
                    ) {

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

                    currentBuild.displayName =
                        "#${BUILD_NUMBER}"
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

                retry(3) {

                    sh '''
                        echo "Running build..."

                        # npm install
                        # npm run build
                    '''
                }
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
                echo "App Endpoint: ${DEV_SERVER_ENDPOINT}"

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
                echo "App Endpoint: ${STAGE_SERVER_ENDPOINT}"

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

                script {

                    timeout(time: 10, unit: 'MINUTES') {

                        def approver = input(
                            message: "Production deployment approval required from ${env.APPROVER_NAME}",
                            ok: 'Deploy',
                            submitterParameter: 'APPROVER_NAME'
                        )

                        env.PROD_APPROVER = approver
                    }

                    echo "Approved By: ${env.PROD_APPROVER}"

                    echo "Deploying to PROD Server: ${PROD_SERVER}"
                    echo "App Endpoint: ${PROD_SERVER_ENDPOINT}"

                    sh '''
                        echo "PROD deployment started..."
                    '''
                }
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
