// Build Docker pipelines

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

        stage('Chekout Repo') {
            steps {

                git branch: 'master',
                credentialsId: 'gihub-creds',
                url: 'git@github.com:lediegoJimenez/timeoff-diego-jimenez.git'

                sh "ls -lat"

            }
        }

        stage('Upload Dockerfile') {
            steps {
                sh "ssh docker@192.168.1.144 \"cd /home/docker && rm -rf timeoff && mkdir timeoff\""
                sh "scp Dockerfile docker@192.168.1.144:/home/docker/timeoff"
            }
        }

        stage('Build Image') {
            steps {
                sh "ssh docker@192.168.1.144 \"cd /home/docker/timeoff && docker build --tag timeoff:${params.VERSION} . \""      
            }
        }
        
        stage('Push Image') {
            steps {
                sh "ssh docker@192.168.1.144 \"docker tag timeoff:${params.VERSION} 192.168.1.144:8085/timeoff:${params.VERSION} \""
                sh "ssh docker@192.168.1.144 \"docker push 192.168.1.144:8085/timeoff:${params.VERSION} \""
            }
        }



    }
    post{
        success {
             build job: 'Deploy_Docker', parameters: [
                string(name: 'VERSION', value: "${params.VERSION}")
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