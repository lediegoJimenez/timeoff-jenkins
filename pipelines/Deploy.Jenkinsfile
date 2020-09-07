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
                    sh "curl -X GET -u $NEXUS_USER:$NEXUS_PASS http://192.168.1.144:8081/repository/timeoff-raw/org/gorilla/${params.VERSION}/timeoff-managment-${params.VERSION}.tar.gz -O"
                    sh " ls -lat"
                }
            }
        }
        stage('Deploy') {
            steps {
                sh "ssh timeoff@${params.VIRTUALMACHINE} \"cd /home/timeoff && rm -rf timeoff-managment-*\""
                sh "scp timeoff-managment-${params.VERSION}.tar.gz timeoff@${params.VIRTUALMACHINE}:/home/timeoff"
                sh "ssh timeoff@${params.VIRTUALMACHINE} \"cd /home/timeoff && chmod -R 750 timeoff-managment-${params.VERSION}.tar.gz"
                sh "ssh timeoff@${params.VIRTUALMACHINE} \"tar -xzvf /home/timeoff/timeoff-managment-${params.VERSION}.tar.gz\""

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