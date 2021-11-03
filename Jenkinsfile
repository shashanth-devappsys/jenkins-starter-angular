pipeline {
    agent any
    triggers {
        githubPush()
    }

     environment {
        TAR_FILE_NAME = "${env.BUILD_TAG}.tar.gz"       // file to be created after successful build
    }

    stages {

       /* stage("Clean workspace") {
            steps {
                cleanWs()   // clean current workspace before build
                checkout scm
            }
        } */
        
        stage("Install Dependencies") {
            steps {
                sh 'npm install'    // install dependencies
            }
        }

        stage("Build") {
            steps {
                sh 'ng build'       // run Angular build
            }
        }

        stage("Prepare zip file") {
            steps {
                sh "cd ${env.WORKSPACE}"    // change directory to current work space
                // sh 'ls -lah'
                sh "tar cvzf ${TAR_FILE_NAME} dist/*"   // create tar file 
            }
        }

        stage('Deploy') {
            steps {
                script {
                    
                    def remote = [
                        name: 'dev-server', 
                        host: env.DEV_SERVER_HOST, 
                        user: env.DEV_SERVER_USER, 
                        password: env.DEV_SERVER_PASSWORD, 
                        port: 6410, 
                        allowAnyHosts: true
                    ]

                     sshPut remote: remote, from: "${env.WORKSPACE}/${TAR_FILE_NAME}", into: "${env.DEV_USER_HOME}/jenkins-starter/"
                     sshCommand remote: remote, command: "cd ${env.DEV_USER_HOME}/jenkins-starter && tar -xvzf ${TAR_FILE_NAME}"
                     sshCommand remote: remote, command: "cd ${env.DEV_USER_HOME}/jenkins-starter/dist && mv * /var/www/jenkins-test/"
                }
            }
        }
    }
    post {
        success{
            echo "Build Success"
            emailext body: "Build ${env.BUILD_DISPLAY_NAME} Successful", 
            to: env.DEV_TEAM_MAIL, 
            subject: 'Test'
        }
        failure{
            echo "Build Failed"
            emailext body: "Build ${env.BUILD_DISPLAY_NAME} Failed", 
            to: env.DEV_TEAM_MAIL, 
            subject: 'Test'
        }
    }
}