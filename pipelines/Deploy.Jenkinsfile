// Build pipelines

String JENKINS_NODE = 'master'

pipeline {
    
    agent{
        label {
            label JENKINS_NODE
            customWorkspace "${env.JOB_NAME}/${env.BUILD_NUMBER}"
        }
    }

    parameters{
        string(name: 'VERSION', defaultValue: '', description: 'Build version')
        string(name: 'VIRTUALMACHINE', defaultValue: '', description: 'Virtual Machine')
    }

    stages {

        stage('Download') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'nexus-admin', passwordVariable: 'NEXUS_PASS', usernameVariable: 'NEXUS_USER')]) {
                    sh "ssh timeoff@${params.VIRTUALMACHINE} \"cd /home/timeoff && rm -rf timeoff-managment-*\""
                    //sh "ssh timeoff@${params.VIRTUALMACHINE} \"cd /home/timeoff && curl -X GET -u $NEXUS_USER:$NEXUS_PASS http://192.168.1.144:8081/repository/timeoff-raw/org/gorilla/${params.VERSION}/timeoff-managment-${params.VERSION}.zip -O\""

                    sh "ssh timeoff@${params.VIRTUALMACHINE} \"cd /home/timeoff && curl -v -u $NEXUS_USER:$NEXUS_PASS -o ~/timeoff-managment-${params.VERSION}.zip http://192.168.1.144:8081/repository/timeoff-raw/org/gorilla/${params.VERSION}/timeoff-managment-${params.VERSION}.zip"
                }
            }
        }
        stage('Deploy') {
            steps {
                //sh "scp timeoff-managment-${params.VERSION}.tar.gz timeoff@${params.VIRTUALMACHINE}:/home/timeoff"
                sh "ssh timeoff@${params.VIRTUALMACHINE} \"cd /home/timeoff && chmod -R 750 timeoff-managment-${params.VERSION}.zip\""
                sh "ssh timeoff@${params.VIRTUALMACHINE} \"unzip /home/timeoff/timeoff-managment-${params.VERSION}.zip\""
            }
        }
    }
    post{
        always {
            script {
                echo "Cleaning Workspace"
                cleanWs()
            }
        }
    }
}