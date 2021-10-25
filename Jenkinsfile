pipeline {
    agent any
    stages {
        stage("Install Dependencies") {
            steps {
                sh 'npm install'
            }
        }
        stage("Build") {
            steps {
                sh 'ng build --prod'
            }
        }
    }
    post {
        success{
            echo "Build Success"
        }
        failure{
            echo "Build Failed"
        }
    }
}