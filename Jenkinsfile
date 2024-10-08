pipeline {
    agent any

    tools {
        nodejs "nodejs"
    }

    stages {
        stage('Install Packages') {
            steps {
                script {
                    sh 'yarn install'
                }
            }
        }

        stage('Install PM2') {
            steps {
                script {
                    sh 'yarn global add pm2'
                }
            }
        }

        stage('Check PRODUCTION_IP_ADRESSS') {
            steps {
                script {
                    sh echo "PRODUCTION_IP_ADRESS= $PRODUCTION_IP_ADRESS" >> /var/lib/jenkins/PRODUCTION_IP_ADRESS.txt
                }
            }
        }

        stage('Run the App') {
            steps {
                script {
                    sh 'yarn start:pm2'
                    sleep 5
                }
            }
        }

        stage('Test the app') {
            steps {
                script {
                    sh 'curl http://localhost:3000/health'
                }
            }
        }

        stage('Stop the App') {
            steps {
                script {
                    sh 'pm2 stop todos-app'
                }
            }
        }

        stage('Add Host to known_hosts') {
            steps {
                script {
                    sh ssh-keyscan -H $PRODUCTION_IP_ADRESS >> /var/lib/jenkins/.ssh/known_hosts
                }
            }
        }

        stage('Deploy') {
                environment {
                    DEPLOY_SSH_KEY = credentials('AWS_INSTANCE_SSH')
                }

                steps {
                    sh '''
                        ssh -v -i $DEPLOY_SSH_KEY ubuntu@$PRODUCTION_IP_ADRESSS '

                            if [ ! -d "todos-app" ]; then
                                git clone https://github.com/RexDeus9/todos-app todos-app
                                cd todos-app
                            else
                                cd todos-app
                                git pull
                            fi

                            yarn install

                            if pm2 describe todos-app > /dev/null ; then
                                pm2 restart todos-app
                            else
                                yarn start:pm2
                            fi
                        '
                    '''
                }
            }
    }
}