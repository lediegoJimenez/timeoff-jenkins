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
                url: 'git@github.com:lediegoJimenez/timeoff-ansible.git'

                sh "ls -lat"

            }
        }
        stage('Configure') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'ansible-admin', passwordVariable: 'ANSIBLE_PASS')]) {

                sh "cd inventories && rm -rf timeoff && echo \"[timeoff]\n${params.VIRTUALMACHINE} ansible_user=timeoff\n\" >> timeoff && cat timeoff"
                sh "ansible-playbook -i ./inventories/timeoff playbook.yaml -vvv --tags timeoff --extra-var \"ansible_sudo_pass=$ANSIBLE_PASS url_repo=http://192.168.1.144:8081/repository/timeoff-raw/org/gorilla/${params.VERSION}/timeoff-managment-${params.VERSION}.zip\"" 
                
                }
            }
        }

    }
    post{
        /*success {
             build job: 'Deploy_Project', parameters: [
                string(name: 'VERSION', value: "${params.VERSION}"),
                string(name: 'VIRTUALMACHINE', value: "${params.VIRTUALMACHINE}")
             ]
        }*/
        always {
            script {
                echo "Cleaning Workspace"
                cleanWs()
                //sh "rm -rf ${env.WORKSPACE}/"
            }
        }
    }
}