pipeline {
    agent any
    triggers {
        githubPush()
    }

     environment {
        TAR_FILE_NAME = "${env.BUILD_TAG}.tar.gz"
    }

    stages {
        stage("Install Dependencies") {
            steps {
                sh 'npm install'
            }
        }
        stage("Build") {
            steps {
                sh 'ng build'
            }
        }
        stage("Prepare zip file") {
            steps {
                sh "cd ${env.WORKSPACE}"
                // sh 'ls -lah'
                sh "tar cvzf ${TAR_FILE_NAME} dist/*"
            }
        }
        stage('Deploy') {
            steps {
                script {
                     def remote = [name: 'dev-server', host: env.DEV_SERVER_HOST, user: env.DEV_SERVER_USER, password: env.DEV_SERVER_PASSWORD, port: 6410, allowAnyHosts: true]
                     sshPut remote: remote, from: env.WORKSPACE + '/' + env.BUILD_TAG + '.tar.gz', into: env.DEV_USER_HOME + '/jenkins-starter/'
                     sshCommand remote: remote, command: "cd ${env.DEV_USER_HOME}/jenkins-starter && tar -xvzf ${TAR_FILE_NAME}", failOnError: true
                     sshCommand remote: remote, command: "cd ${env.DEV_USER_HOME}/jenkins-starter/dist && mv * /var/www/jenkins-test/"
                }
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