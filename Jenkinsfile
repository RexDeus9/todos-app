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
                    sh '''
                        ssh-keyscan -H ${PRODUCTION_IP_ADDRESS} >> /var/lib/jenkins/.ssh/known_hosts || true
                    '''
                }
            }
        }

      stage('Deploy') {
    environment {
        DEPLOY_SSH_KEY = credentials('AWS_INSTANCE_SSH')
        PRODUCTION_IP_ADDRESS = 'ec2-13-235-91-89.ap-south-1.compute.amazonaws.com' // Set your actual IP address here or ensure it’s defined elsewhere
    }

    steps {
        sh '''
            echo "Connecting to: $PRODUCTION_IP_ADDRESS"  # Debugging output

            ssh -i "test.pem" ubuntu@ec2-13-235-91-89.ap-south-1.compute.amazonaws.com'
                if [ ! -d "todos-app" ]; then
                    git clone https://github.com/AhmadMazaal/todos-app.git todos-app
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
