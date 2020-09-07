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

                sh "cd inventories && rm -rf timeoff && echo \"[timeoff]\n${params.VIRTUALMACHINE} ansible_user=timeoff\n\" >> timeoff && cat timeoff"
                sh "ssh timeoff@${params.VIRTUALMACHINE} \"cd /home/timeoff && rm -rf timeoff-managment-*\""

                //withCredentials([usernamePassword(credentialsId: 'ansible-admin', passwordVariable: 'ANSIBLE_PASS', usernameVariable: 'ANSIBLE_USER')]) {               
                sh "ansible-playbook -i ./inventories/timeoff playbook.yaml -vvv --tags timeoff --extra-var \"ansible_sudo_pass=time123 url_repo=http://192.168.1.144:8081/repository/timeoff-raw/org/gorilla/${params.VERSION}/timeoff-managment-${params.VERSION}.zip\""                 
                //}
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
                //sh "rm -rf ${env.WORKSPACE}/"
            }
        }
    }
}