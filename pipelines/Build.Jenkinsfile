// Build pipelines

String JENKINS_NODE = 'master'

pipeline {
    
    agent{
        label {
            label JENKINS_NODE
            customWorkspace "${env.JOB_NAME}/${env.BUILD_NUMBER}"
        }
    }

    stages {


        stage('Chekout Repo') {
            steps {

                git branch: 'master',
                credentialsId: 'gihub-creds',
                url: 'git@github.com:lediegoJimenez/timeoff-diego-jimenez.git'

                sh "ls -lat"
                //sh "mkdir timeoff-diego-jimenez && mv  ${env.WORKSPACE}/*  ./timeoff-diego-jimenez"

            }
        }
        stage('Build') {
            steps {
                sh "tar -czvf tmieoff-diego-jimenez.tar.gz ${env.WORKSPACE} --exclude='*.tar.gz'"
            }
        }
        stage('Deploy') {
            steps {

                withCredentials([usernamePassword(credentialsId: 'nexus-admin', passwordVariable: 'NEXUS_PASS', usernameVariable: 'NEXUS_USER')]) {
                    sh "curl -v -u $NEXUS_USER:$NEXUS_PASS --upload-file timeoff-diego-jimenez.tar.gz  http://192.168.1.144:8081/repository/timeoff/org/gorilla/1.0/timeoff-diego-jimenez-1.0.tar.gz"
                }
            }
        }
    }
    post{
        always {
            script {
                echo "Cleaning Workspace"
                cleanWs()
                sh "rm -rf ${env.WORKSPACE}/"
            }
        }
    }
}