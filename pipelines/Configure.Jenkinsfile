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
                url: 'git@github.com:lediegoJimenez/timeoff-ansible.git'

                sh "ls -lat"

            }
        }
        stage('Configure') {
            steps {
                sh "ansible-playbook -i ./inventories/timeoff playbook.yaml -vvv --tags timeoff --extra-vars \"ansible_sudo_pass=admin123\""
            }
        }
        stage('Deploy') {
            steps {

                withCredentials([usernamePassword(credentialsId: 'nexus-admin', passwordVariable: 'NEXUS_PASS', usernameVariable: 'NEXUS_USER')]) {
                    sh "curl -v -u $NEXUS_USER:$NEXUS_PASS --upload-file timeoff-management.tar.gz  http://192.168.1.144:8081/repository/timeoff-raw/org/gorilla/${params.VERSION}/timeoff-management-${params.VERSION}.tar.gz"
                }
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