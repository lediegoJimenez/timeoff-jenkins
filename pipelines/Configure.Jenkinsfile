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

                sh "cd inventories && rm -rf timeoff && echo \"[timeoff]\n ${params.VIRTUALMACHINE} ansible_user=timeoff \n\" >> timeoff && cat timeoff"
                sh "ansible-playbook -i ./inventories/timeoff playbook.yaml -vvv --tags timeoff --extra-vars \"ansible_sudo_pass=time123\""
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