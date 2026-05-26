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

                withCredentials([string(credentialsId: 'sonartoken', variable: 'sonar_token')]) {

                sh '''
                /opt/sonar-scanner/bin/sonar-scanner \
                -Dsonar.projectKey=anuragow_myproject1 \
                -Dsonar.organization=anuragow \
                -Dsonar.sources=. \
                -Dsonar.host.url=https://sonarcloud.io \
                -Dsonar.login=$sonar_token
                '''
                }
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

             stage('Install Trivy') {
            steps {
                sh '''
                sudo apt-get install wget gnupg -y

                wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | \
                gpg --dearmor | \
                sudo tee /usr/share/keyrings/trivy.gpg > /dev/null

                echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb generic main" | \
                sudo tee -a /etc/apt/sources.list.d/trivy.list

                sudo apt-get update

                sudo apt-get install trivy -y
                '''
            }
        }

                stage('Trivy Scan') {
            steps {
                sh 'trivy image --format json --output result.json --severity HIGH,CRITICAL anuragjnaik/cicdproject:latest'
                    archiveArtifacts artifacts: 'result.json', followSymlinks: false
            
            }
        }
    }
}
