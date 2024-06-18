pipeline {
    agent any
    stages{
        stage("zip the file"){
            steps{
                sh '''
                    mkdir -p ansible-dev
                    rm -rf ansible-dev/*
                    shopt -s extglob
                    mv !(ansible-dev) ansible-dev/
                    '''
                dir ('/var/lib/jenkins/workspace/ansible-playbook-class/ansible-dev'){
                    sh 'rm -rf *.zip || echo ""'
                    sh 'zip -r ansible-${BUILD_ID}.zip * --exclude Jenkinsfile'
                }
                
            }
        }

        stage("upload artifact to jfrog"){
            steps{
                sh 'curl -uadmin:AP8gcgmmset5jeYChTJYDN6XmDd \
                -T ansible-dev/ansible-${BUILD_ID}.zip \
                "http://100.25.48.130:8081/artifactory/ansible-${BUILD_ID}.zip"'
            }
        }

        stage("publish to ansible server"){
            steps{
                sshPublisher(publishers: [sshPublisherDesc(configName: 'Ansible', \
                transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'cd ansible-dev; unzip -o ansible-${BUILD_ID}.zip; rm -rf ansible-${BUILD_ID}.zip', \
                execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, \
                patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, \
                removePrefix: '', sourceFiles: 'ansible-dev/ansible-${BUILD_ID}.zip')], usePromotionTimestamp: false, \
                useWorkspaceInPromotion: false, verbose: false)])
            }
        }
    }
}