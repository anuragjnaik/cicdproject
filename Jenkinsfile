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
      }
    } 
