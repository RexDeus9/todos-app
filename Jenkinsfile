pipeline {
    agent any

    tools {
        nodejs "nodejs"
    }

    stages {
        stage('Save Environment') {
            steps {
                script {
                    sh 'env > ~/_environment.txt'
                }
            }
        }

        stage('Where are we') {
            steps {
                script {
                    sh 'pwd > ~/_files.txt'
                    sh 'ls -al >> ~/_files.txt'
                }
            }
        }

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

        stage('Pre Deploy') {
            steps {
                sh 'echo -e Here we would deploy!'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    cd /var/lib/jenkins/workspace
                    if [ ! -d "todos-app" ]; then
                        #git clone https://github.com/RexDeus9/todos-app todos-app
                        cd todos-app
                        echo 'git clone'
                        echo 'git clone' > /var/lib/jenkins/workspace/echo.txt
                    else
                        cd todos-app
                        #git pull
                        echo 'git pull'
                        echo 'git pull' > /var/lib/jenkins/workspace/echo.txt
                    fi
                '''
            }
        }

/*
        stage('Deploy') {
            steps {
                sh '''
                    //ssh -v -i $DEPLOY_SSH_KEY ubuntu@$IP_ADDRESS '
                    //ssh -v -i "/tmp/jenkins-key.pem" ubuntu@ec2-3-85-183-54.compute-1.amazonaws.com '
                    ssh -v ubuntu@ec2-3-85-183-54.compute-1.amazonaws.com '

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