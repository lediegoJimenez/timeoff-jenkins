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


        stage('Chekout Repo') {
            steps {

                git branch: 'master',
                credentialsId: 'gihub-creds',
                url: 'git@github.com:lediegoJimenez/timeoff-diego-jimenez.git'

                sh "ls -lat"

            }
        }
        stage('Build') {
            steps {
                //sh "zip -r timeoff-management.zip ${env.WORKSPACE} && ls -lat"
                sh "cd ${env.WORKSPACE} && zip -r timeoff-management.zip ./* && ls -lat "
            }
        }
        stage('Deploy') {
            steps {

                withCredentials([usernamePassword(credentialsId: 'nexus-admin', passwordVariable: 'NEXUS_PASS', usernameVariable: 'NEXUS_USER')]) {
                    sh "curl -v -u $NEXUS_USER:$NEXUS_PASS --upload-file timeoff-management.zip  http://192.168.1.144:8081/repository/timeoff-raw/org/gorilla/${params.VERSION}/timeoff-management-${params.VERSION}.zip"
                }
            }
        }
    }
    post{
        success {
             build job: 'Configure_VM', parameters: [
                string(name: 'VERSION', value: "${params.VERSION}"),
                string(name: 'VIRTUALMACHINE', value: "${params.VIRTUALMACHINE}")
             ]
        }
        always {
            script {
                echo "Cleaning Workspace"
                cleanWs()
            }
        }
    }
}