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
        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: '**/*', fingerprint: true
            }
        }
        stage('Upload to JFrog Artifactory') {
            steps {
                rtServer (
                    id: 'Artifactory-Server',
                    url: 'https://trial116ruw.jfrog.io',
                    credentialsId: 'artifactory-credentials-id'
                )
                rtUpload (
                    serverId: 'Artifactory-Server',
                    spec: '''{
                        "files": [
                            {
                                "pattern": "**/*.html",
                                "target": "web-static-files/"
                            }
                        ]
                    }'''
                )
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
