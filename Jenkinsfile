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
      }
    } 
