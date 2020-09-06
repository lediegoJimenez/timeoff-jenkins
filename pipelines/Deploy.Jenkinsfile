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

        stage('Download') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'nexus-admin', passwordVariable: 'NEXUS_PASS', usernameVariable: 'NEXUS_USER')]) {
                    sh "curl -X GET -u $NEXUS_USER:$NEXUS_PASS http://192.168.1.144:8081/repository/timeoff-raw/org/gorilla/1.0/timeoff-diego-jimenez-1.0.tar.gz -O"
                    sh " ls -lat"

                    //sh "tar -xzvf timeoff-diego-jimenez-1.0.tar.gz && ls -lat"
                }
            }
        }
        stage('Deploy') {
            steps {
                sh "scp timeoff-diego-jimenez-1.0.tar.gz timeoff@192.168.1.122:/home/timeoff"
                sh "tar -xzvf /home/timeoff/timeoff-diego-jimenez-1.0.tar.gz"

            }
        }
    }
    post{
        always {
            script {
                echo "Cleaning Workspace"
                cleanWs()
                //sh "rm -rf ${env.WORKSPACE}/"
            }
        }
    }
}