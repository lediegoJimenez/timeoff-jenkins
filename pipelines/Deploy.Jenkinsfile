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
    }

    stages {

        stage('Download') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'nexus-admin', passwordVariable: 'NEXUS_PASS', usernameVariable: 'NEXUS_USER')]) {
                    sh "curl -X GET -u $NEXUS_USER:$NEXUS_PASS http://192.168.1.144:8081/repository/timeoff-raw/org/gorilla/${parms.VERSION}/timeoff-managment-${parms.VERSION}.tar.gz -O"
                    sh " ls -lat"
                }
            }
        }
        stage('Deploy') {
            steps {
                sh "scp timeoff-managment-${parms.VERSION}.tar.gz timeoff@192.168.1.122:/home/timeoff"
                sh "cd /home/timeoff && rm -rf timeoff-managment-*"
                sh "tar -xzvf /home/timeoff/timeoff-managment-jimenez-${parms.VERSION}.tar.gz && "

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