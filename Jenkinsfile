pipeline {
    agent any

    stages {

        stage('Clone Repository') {
            steps {
               git branch: 'main', url: 'https://github.com/anuragjnaik/cicdproject.git'
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

 stage('Install sonarqube cli') {
steps {
// Step to install SonarQube CLI
sh 'sudo wget -O sonar-scanner.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.0.1.3006-linux.zip'
sh 'sudo apt install unzip -y'
sh 'sudo unzip -o -q sonar-scanner.zip'
sh 'sudo rm -rf /opt/sonar-scanner'
sh 'sudo mv --force sonar-scanner-5.0.1.3006-linux /opt/sonar-scanner'
sh 'sudo sh -c \'echo "#/bin/bash \nexport PATH=\\\"$PATH:/opt/sonar-scanner/bin\\\"" >/etc/profile.d/sonar-scanner.sh\''
sh 'sudo chmod +x /opt/sonar-scanner/bin/sonar-scanner'
sh '. /etc/profile.d/sonar-scanner.sh'
}
}
stage('Analyzing Code Quality') {
steps {
// Step to analyze code quality with SonarQube
sh '/opt/sonar-scanner/bin/sonar-scanner -Dsonar.projectKey=anuragow_myproject -Dsonar.organization=anuragow -Dsonar.qualitygate.wait=false -Dsonar.qualitygate.timeout=300 -Dsonar.sources=src/main/java/ -Dsonar.java.binaries=target/classes -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=f02c29b68363fa5a3d16a4d05add7b8974731123'
}
}
        
      }
    } 
