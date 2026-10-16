pipeline {
    agent any 
    stages {
        stage('checkout') {
            steps {
                echo 'clone the repo'
                git url: 'https://github.com/vaibhavk2000/Student-registration-webpage-project.git', branch: 'main'
            }
        }
        stage('build') {
            steps {
                echo 'build docker images'
                sh 'cd frontend && docker build -t fronted:latest .'
            }
        }
        stage('run') {
            steps {
                echo 'run the container'
                sh 'docker run -d -P fronted:latest'
            }
        }
    }
}
