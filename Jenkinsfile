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
                            requirements.txt \
                            azureuser@20.114.40.192:~/my-cicd-app/

                        ssh -o StrictHostKeyChecking=no azureuser@20.114.40.192 '
                            cd ~/my-cicd-app &&
                            rm -rf venv &&
                            python3 -m venv venv &&
                            ./venv/bin/python -m pip install -r requirements.txt &&
                            pkill -f "venv/bin/python app.py" || true &&
                            nohup ./venv/bin/python app.py > app.log 2>&1 </dev/null &
                        '
                    '''
                }
            }
        }
    }
}