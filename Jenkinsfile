pipeline {
    agent any

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/anuragjnaik/cicdproject.git'
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

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t anuragjnaik/cicdproject:latest -f Dockerfile .'
            }
        }

        stage('Push') {
            steps {
              withCredentials([usernamePassword(credentialsId: 'docker login', passwordVariable: 'password', usernameVariable: 'username')]) {
    

                sh '''
                echo $password | docker login -u $username --password-stdin

                docker push anuragjnaik/cicdproject:latest
                '''
            }
        }
}
    }
}
