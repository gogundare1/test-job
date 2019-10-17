pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                dd if=/dev/zero of=file.txt count=1024 bs=1024
            }
        }
        stage('DeployToStaging') {
            when {
                branch 'master'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'staging',
                                sshCredentials: [
                                    username: "$USERNAME",
                                    encryptedPassphrase: "$USERPASS"
                                ], 
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: '/home/gxo/file.txt'
                                        remoteDirectory: '/tmp',
                                        execCommand: 'sudo yum install -y ntp && sudo /usr/bin/systemctl start ntpd'
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
        stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                input 'Does the staging environment look OK? if Yes, "click Proceed", else CLick "Cancel"'
                milestone(1)
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'production',
                                sshCredentials: [
                                    username: "$USERNAME",
                                    encryptedPassphrase: "$USERPASS"
                                ], 
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: '/home/gxo/file.txt'
                                        remoteDirectory: '/tmp',
                                        execCommand: 'sudo yum install -y ntp && sudo /usr/bin/systemctl start ntpd'
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
    }
}
