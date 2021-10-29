pipeline {
    agent any
    triggers {
        githubPush()
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
                sh 'cd ' + env.WORKSPACE
                sh 'ls -lah'
                sh 'tar cvzf ' + env.BUILD_TAG + '.tar.gz dist/*'
            }
        }
        stage('Deploy') {
            steps {
                script {
                     def remote = [name: 'dev-server', host: env.DEV_SERVER_HOST, user: env.DEV_SERVER_USER, password: env.DEV_SERVER_PASSWORD, port: 6410, allowAnyHosts: true]
                     sshPut remote: remote, from: env.WORKSPACE + '/' + env.BUILD_TAG + '.tar.gz', into: '/home/shashanth/jenkins-starter/'
                     sshCommand remote: remote, command: 'cd ~/jenkins-starter && tar -xvzf ' + env.BUILD_TAG + '.tar.gz', failOnError: true
                     sshCommand remote: remote, command: 'cd ~/jenkins-starter/dist && mv -r * /var/www/jenkins-test/'
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