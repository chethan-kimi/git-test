pipeline {
    agent any
    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/chethan-kimi/git-test.git', credentialsId: 'github-credentials-id'
            }
        }
        stage('Build Application') {
            steps {
                echo 'Static HTML - No Build Required'
            }
        }
        stage('Upload to JFrog Artifactory') {
    steps {
        script {
            // Define the Artifactory server
            def server = Artifactory.server('Artifactory-Server')

            // Define upload spec
            def uploadSpec = '''{
                "files": [
                    {
                        "pattern": "**/*.html",
                        "target": "web-static-files/"
                    },
                    {
                        "pattern": "**/*.css",
                        "target": "web-static-files/"
                    },
                    {
                        "pattern": "**/*.js",
                        "target": "web-static-files/"
                    }
                ]
            }'''

            // Upload files to Artifactory
            server.upload(uploadSpec)
        }
    }
}
        stage('Deploy to AWS EC2') {
            steps {
                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: 'ec2-ssh-config',
                            transfers: [
                                sshTransfer(
                                    sourceFiles: '**',
                                    remoteDirectory: '/var/www/html/',
                                    execCommand: 'sudo systemctl restart httpd'
                                )
                            ]
                        )
                    ]
                )
            }
        }
    }
}
