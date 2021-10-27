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
        stage('Deploy') {
            steps {
                script {
                     def remote = [name: 'dev-server', host: env.DEV_SERVER_HOST, user: env.DEV_SERVER_USER, password: env.DEV_SERVER_PASSWORD, port: 6410, allowAnyHosts: true]
                     // sshCommand remote: remote, command: "for i in {1..5}; do echo -n \"Loop \$i \"; date ; sleep 1; done"
                     sshPut remote: remote, from: env.WORKSPACE + '/dist/.', into: '/home/shashanth/jenkins-starter/'
                     sshCommand remote: remote, command: "cd /home/shashanth/jenkins-starter/"
                    //  sshCommand remote: remote, command: "ls -lah"
                     sshCommand remote: remote, command: "mv /home/shashanth/jenkins-starter/dist/* .", failOnError: false
                     sshCommand remote: remote, command: "rm -rf /home/shashanth/jenkins-starter/dist"
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