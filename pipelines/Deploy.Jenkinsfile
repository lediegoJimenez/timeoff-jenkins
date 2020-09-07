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
                    //DOWNLOAD
                }
            }
        }
        stage('Deploy') {
            steps {
                sh "ssh timeoff@${params.VIRTUALMACHINE} \"cd /home/timeoff && rm -rf timeoff-management-*\""
                sh "ssh timeoff@${params.VIRTUALMACHINE} \"cd /home/timeoff && ./bash.sh\""
                sh "ssh timeoff@${params.VIRTUALMACHINE} \"cd /home/timeoff && chmod -R 750 timeoff-management-${params.VERSION}.zip\""
                sh "ssh timeoff@${params.VIRTUALMACHINE} \"mkdir timeoff-management-${params.VERSION} && unzip -d /home/timeoff/timeoff-management-${params.VERSION} /home/timeoff/timeoff-management-${params.VERSION}.zip\""
                sh "ssh timeoff@${params.VIRTUALMACHINE} \"cd /home/timeoff/timeoff-management-${params.VERSION} && npm install\""
                sh "ssh timeoff@${params.VIRTUALMACHINE} \"cd /home/timeoff/timeoff-management-${params.VERSION} && npm start > /dev/null 2>&1 & \""
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