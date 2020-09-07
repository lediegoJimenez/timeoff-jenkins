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

        
                    //sh "echo \"ssh timeoff@${params.VIRTUALMACHINE} \"cd /home/timeoff && curl -X GET -u $NEXUS_USER:$NEXUS_PASS \"http://192.168.1.144:8081/service/rest/v1/search/assets?repository=timeoff-raw&name=org%2Fgorilla%2F${params.VERSION}%2Ftimeoff-management-${params.VERSION}.zip\" -H \"accept: application/json\" | grep -Po \'\"downloadUrl\" : \"\\K.+(?=\",)\' | xargs curl -u $NEXUS_USER:$NEXUS_PASS -fsSL -o timeoff-management-${params.VERSION}.zip\"\""
                    //sh "ssh timeoff@${params.VIRTUALMACHINE} \"cd /home/timeoff && curl -X GET -u $NEXUS_USER:$NEXUS_PASS \"http://192.168.1.144:8081/service/rest/v1/search/assets?repository=timeoff-raw&name=org%2Fgorilla%2F${params.VERSION}%2Ftimeoff-management-${params.VERSION}.zip\" -H \"accept: application/json\" | grep -Po \'\"downloadUrl\" : \"\\K.+(?=\",)\' | xargs curl -u $NEXUS_USER:$NEXUS_PASS -fsSL -o timeoff-management-${params.VERSION}.zip\""

                    //sh "ssh timeoff@${params.VIRTUALMACHINE} curl -X GET -u $NEXUS_USER:$NEXUS_PASS \"http://192.168.1.144:8081/service/rest/v1/search/assets?repository=timeoff-raw&name=org%2Fgorilla%2F${params.VERSION}%2Ftimeoff-management-${params.VERSION}.zip\" | grep -Po '\"downloadUrl\" : \"\\K.+(?=\",)' | xargs curl -u $NEXUS_USER:$NEXUS_PASS -fsSL -o timeoff-management-${params.VERSION}.zip"

                    sh "echo \"curl -X GET -u $NEXUS_USER:$NEXUS_PASS \"http://192.168.1.144:8081/service/rest/v1/search/assets?repository=timeoff-raw&name=org%2Fgorilla%2F${params.VERSION}%2Ftimeoff-management-${params.VERSION}.zip\" | grep -Po '\"downloadUrl\" : \"\\K.+(?=\",)' | xargs curl -u $NEXUS_USER:$NEXUS_PASS -fsSL -o timeoff-management-${params.VERSION}.zip\" >> bash.sh && cat bash.sh"

                    sh "scp bash.sh timeoff@${params.VIRTUALMACHINE}:/home/timeoff"

                }
            }
        }
        stage('Deploy') {
            steps {
                //sh "scp timeoff-managment-${params.VERSION}.tar.gz timeoff@${params.VIRTUALMACHINE}:/home/timeoff"
                sh "ssh timeoff@${params.VIRTUALMACHINE} \"cd /home/timeoff && chmod -R 750 timeoff-managment-${params.VERSION}.zip\""
                sh "ssh timeoff@${params.VIRTUALMACHINE} \"unzip /home/timeoff/timeoff-managment-${params.VERSION}.zip\""
                sh "ssh timeoff@${params.VIRTUALMACHINE} \"cd /home/timeoff/timeoff-managment-${params.VERSION} && npm install && npm start > /dev/null 2>&1 & \""
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