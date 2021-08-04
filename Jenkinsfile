pipeline {
    agent any

    tools {
        maven "Maven"
    }

    stages {
        stage('Build') {
            steps {
                // Get some code from a GitHub repository
                git 'https://github.com/Sourabh-The-Creator/hello-world.git'

                sh "mvn clean install package"

            }

        }
        
        stage('Build Docker Image') {
            steps {
                sshPublisher(publishers: [
                    
                    sshPublisherDesc(
                            configName: 'Ansible server', 
                            transfers: [sshTransfer( 
                                                    execTimeout: 120000, 
                                                    remoteDirectory: '//opt//docker', 
                                                    removePrefix: 'webapp/target',
                                                    sourceFiles: 'webapp/target/*.war')
                                        ],
                                    ),
                                                     
                    sshPublisherDesc(
                            configName: 'Ansible server', 
                            transfers: [sshTransfer( 
                                                    execCommand: '''cd /opt/docker
                                                                    sudo docker build -t $JOB_NAME .
                                                                    sudo docker tag $JOB_NAME sourabhmiraje/$JOB_NAME:$BUILD_ID
                                                                    sudo docker tag $JOB_NAME sourabhmiraje/$JOB_NAME:latest
                                                                    sudo docker push sourabhmiraje/$JOB_NAME:$BUILD_ID
                                                                    sudo docker push sourabhmiraje/$JOB_NAME:latest
                                                                    sudo docker rmi -f sourabhmiraje/$JOB_NAME:latest
                                                                    sudo docker rmi -f sourabhmiraje/$JOB_NAME:$BUILD_ID
                                                                    sudo docker rmi -f $JOB_NAME
                                                                    ansible-playbook /opt/docker/playbook.yaml''',
                                                    execTimeout: 120000, 
                                                    remoteDirectory: '//opt//docker', 
                                                    sourceFiles: 'Dockerfile')
                                        ],
                                    )
                                ]
                            )
            }

        }
    }
}
