def IMAGE_NAME = 'my-node-app'
pipeline {
    agent any
    stages {

        stage('Test') {
            steps {
                sh 'echo "Testing..."'
                script {
                    def imageName = "ttl.sh/${IMAGE_NAME}:10m"
                    def dockerBuildCommand = "docker build -t ${imageName} -f Dockerfile.test .";
                    def dockerRunCommand = "docker run -p 4444:4444 --name my_container ${imageName}";
                    def container = "my_container"
                    def stopContainerCommand = """docker stop ${container} && docker rm ${container}"""

                    sh stopContainerCommand
                    sh dockerBuildCommand
                    sh dockerRunCommand
                    sh stopContainerCommand
                }
            }
        }

        
        stage('Build and Push:STAGING') {
            when {
                branch 'origin/staging'
            }
            steps {
                sh 'echo "Deploying..."'

                withCredentials([sshUserPrivateKey(credentialsId: 'mykey2',
                                                   keyFileVariable: 'mykey',
                                                   usernameVariable: 'myuser')]) {
                    script {
                        def imageName = "ttl.sh/${IMAGE_NAME}:10m"
                        def dockerBuildCommand = "docker build -t ${imageName} .";
                        def dockerPullCommand = "docker push ${imageName};"
                        def ret = sh dockerBuildCommand
                        print(ret)
                        def ret1 = sh(dockerPullCommand)
                        print(ret1)
                    }    
                    
                }
            }
        }
        stage('Pull and Run as a service:STAGING') {
            when {
                branch 'origin/staging'
            }
            steps {
                sh 'echo "Running as a service..."'
                withCredentials([sshUserPrivateKey(credentialsId: 'mykey2',
                                                   keyFileVariable: 'mykey',
                                                   usernameVariable: 'myuser')]) {
                    script {
                        def remoteHost = "192.168.56.3 "
                        def imageName = "ttl.sh/${IMAGE_NAME}:10m"
                        def defaultPort = "4444"
                        def container = "my_container_master"

                        def stopContainerCommand = """docker stop ${container} && docker rm ${container}"""
                        def runContainerCommandDefault = "docker run -d -p ${defaultPort}:${defaultPort} --name ${container} ${imageName}"
                        def sshCommand = """ssh -o StrictHostKeyChecking=no -i ${mykey} ${myuser}@${remoteHost} \"${stopContainerCommand} && ${runContainerCommandDefault}\" """
                        sh(sshCommand)
                    }
                }
            }
        }
        stage('Build and Push:MASTER') {
            when {
                branch 'origin/main'
            }
            steps {
                sh 'echo "Deploying..."'

                withCredentials([sshUserPrivateKey(credentialsId: 'mykey2',
                                                   keyFileVariable: 'mykey',
                                                   usernameVariable: 'myuser')]) {
                    script {
                        def imageName = "ttl.sh/${IMAGE_NAME}_master:10m"
                        def dockerBuildCommand = "docker build -t ${imageName} -f Dockerfile.prod .";
                        def dockerPullCommand = "docker push ${imageName};"
                        def ret = sh dockerBuildCommand
                        print(ret)
                        def ret1 = sh(dockerPullCommand)
                        print(ret1)
                    }    
                    
                }
            }
        }
        stage('Pull and Run as a service:MASTER') {
            when {
                branch 'origin/main'
            }
            steps {
                sh 'echo "Running as a service..."'
                withCredentials([sshUserPrivateKey(credentialsId: 'mykey2',
                                                   keyFileVariable: 'mykey',
                                                   usernameVariable: 'myuser')]) {
                    script {
                        def remoteHost = "192.168.56.3 "
                        def imageName = "ttl.sh/${IMAGE_NAME}_master:10m"
                        def defaultPort = "4444"
                        def container = "my_container_master"

                        def stopContainerCommand = """docker stop ${container} && docker rm ${container}"""
                        def runContainerCommandDefault = "docker run -d -p ${defaultPort}:${defaultPort} --name ${container} ${imageName}"
                        def sshCommand = """ssh -o StrictHostKeyChecking=no -i ${mykey} ${myuser}@${remoteHost} \"${stopContainerCommand} && ${runContainerCommandDefault}\" """
                        sh(sshCommand)
                    }
                }
            }
        }
    }
}
