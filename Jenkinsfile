pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Deploy to Azure VM') {
            steps {
                sshagent(['AZURE_VM_SSH']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no azureuser@20.114.40.192 '
                            mkdir -p ~/my-cicd-app
                        '

                        scp -o StrictHostKeyChecking=no app.py \
                            azureuser@20.114.40.192:~/my-cicd-app/app.py

                        ssh -o StrictHostKeyChecking=no azureuser@20.114.40.192 '
                            cd ~/my-cicd-app &&
                            python3 -m venv venv &&
                            ./venv/bin/pip install flask &&
                            pkill -f "python.*app.py" || true &&
                            nohup ./venv/bin/python app.py > app.log 2>&1 &
                        '
                    '''
                }
            }
        }
    }
}
