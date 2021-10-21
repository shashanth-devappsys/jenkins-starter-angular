pipeline {
    agent {
        label "node"
    }
    stages {
        stage("Git Pull") {
            steps {
                sh 'git pull origin dev'
            }
        }
        stage("Build") {
            steps {
                sh 'npm install'
            }
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