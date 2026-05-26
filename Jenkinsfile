pipeline {

    agent any

    environment {
        PACKAGE_NAME = "my_package"
        PYTHON       = "python3"
        VENV_DIR     = "venv"
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Install Python Venv') {
            steps {
                sh '''
                sudo apt-get update
                sudo apt-get install python3-venv -y
                '''
            }
        }

        stage('Setup Python Environment') {
            steps {

                sh """
                    ${PYTHON} -m venv ${VENV_DIR}

                    . ${VENV_DIR}/bin/activate

                    pip install --upgrade pip

                    pip install -r requirements.txt
                """
            }
        }

        stage('Install Build Package') {
            steps {

                sh """
                    . ${VENV_DIR}/bin/activate

                    pip install build
                """
            }
        }

        stage('Build WHL Package') {
            steps {

                sh """
                    . ${VENV_DIR}/bin/activate

                    python -m build
                """
            }
        }

        stage('Archive Artifacts') {
            steps {

                archiveArtifacts artifacts: 'dist/*.whl, dist/*.tar.gz',
                fingerprint: true
            }
        }

        stage('Build Docker Image') {
            steps {

                sh '''
                docker build -t anuragjnaik/cicdproject:latest .
                '''
            }
        }

        stage('Push Docker Image') {
            steps {

                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'USERNAME',
                    passwordVariable: 'PASSWORD'
                )]) {

                    sh '''
                    echo $PASSWORD | docker login -u $USERNAME --password-stdin

                    docker push anuragjnaik/cicdproject:latest
                    '''
                }
            }
        }

        stage('Install SonarScanner') {
            steps {

                sh '''
                sudo wget -O sonar-scanner.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.0.1.3006-linux.zip

                sudo apt install unzip -y

                sudo unzip -o -q sonar-scanner.zip

                sudo rm -rf /opt/sonar-scanner

                sudo mv --force sonar-scanner-5.0.1.3006-linux /opt/sonar-scanner

                sudo chmod +x /opt/sonar-scanner/bin/sonar-scanner
                '''
            }
        }

        stage('Analyzing Code Quality') {
            steps {

                sh '''
                /opt/sonar-scanner/bin/sonar-scanner \
                -Dsonar.projectKey=anuragow_myproject1 \
                -Dsonar.organization=anuragow \
                -Dsonar.sources=. \
                -Dsonar.host.url=https://sonarcloud.io \
                -Dsonar.login=2768594d881140b713e584eb57b4f2f8478135cd
                '''
            }
        }
    }
}
