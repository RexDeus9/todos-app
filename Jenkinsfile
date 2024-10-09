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

        stage('Check IP_ADDRESS') {
            steps {
                script {
                    //sh 'export IP_ADDRESS=$(hostname -i)'
                    sh 'IP_ADDRESS=172.31.35.186'
                    sh 'echo "IP_ADDRESS=$IP_ADDRESS"'
                    sh 'echo IP_ADDRESS=172.31.35.186 > /var/lib/jenkins/IP_ADDRESS.txt'
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
                    sh 'echo "Hello Stop"'
                    //sh 'pm2 stop todos-app'
                }
            }
        }

        stage('Add Host to known_hosts') {
            steps {
                script {
                    sh 'sleep 2'
                    sh 'mkdir -p /var/lib/jenkins/.ssh'
                    //sh 'ssh-keyscan -H $IP_ADDRESS > /var/lib/jenkins/.ssh/known_hosts'
                    sh 'ssh-keyscan -H 172.31.35.186 > /var/lib/jenkins/.ssh/known_hosts'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh 'pwd > ~/_files.txt'
                    sh 'echo '________' >> ~/_files.txt'
                    sh 'ls -al >> ~/_files.txt'
                }
            }
        }
/*
        stage('Deploy') {
                environment {
                    //DEPLOY_SSH_KEY = credentials('AWS_INSTANCE_SSH')
                    DEPLOY_SSH_KEY = 'AWS_INSTANCE_SSH'
                }
                steps {
                    sh '''
                        //ssh -v -i $DEPLOY_SSH_KEY ubuntu@$IP_ADDRESS '
                        ssh -v -i "~/.aws/jenkins-key.pem" ubuntu@ec2-3-85-183-54.compute-1.amazonaws.com '

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
*/
    }
}