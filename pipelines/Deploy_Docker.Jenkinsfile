// Deploy Docker pipelines

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

        stage('Stop and Delete Container') {
            steps {
                sh "ssh docker@192.168.1.144 \"docker container stop timeoff\""
                sh "ssh docker@192.168.1.144 \"docker container rm timeoff\""
            }
        }

        stage('Pull Image') {
            steps {
                sh "ssh docker@192.168.1.144 \"docker pull 192.168.1.144:8085/timeoff:${params.VERSION} \""
            }
        }
        
        stage('Run Container') {
            steps {
                sh "ssh docker@192.168.1.144 \"docker run -d -p 3000:3000 --name timeoff timeoff:${params.VERSION} \""
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