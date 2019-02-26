properties([
    pipelineTriggers([pollSCM('H/4 * * * *')])
])

pipeline {
    agent any
    stages {
        stage("SCM") {
            steps {
            //    git poll: true, url: 'https://github.com/willsbor/jenkinsfile-test'
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '4a1f8ef9-5a5f-4bfd-80e2-6ad127a6e855', url: 'https://github.com/willsbor/jenkinsfile-test']]])
            }
        }

        stage("prepare file from mailbox") {
            steps {
                when {
                    expression {
                        return isStartByUppersteamBuild()
                    }
                }

                script {
                    def mailBox = "/Users/jenkins/Documents/SDKServerMailBox"
                    def testingBox = "/Users/jenkins/Documents/SDKServerTesting"
                    def mailedAppName = "app.zip"
                    def tempAppName = "receive.app.zip"
                    def testAppName = "current.app.zip"
                    try {
                        sh "test -f \"${mailBox}/${mailedAppName}\""
                        echo "file exist"
                        removeFile("${testingBox}/${tempAppName}")
                        sh "mv \"${mailBox}/${mailedAppName}\" \"${testingBox}/${tempAppName}\""
                        removeFile("${testingBox}/${testAppName}")
                        sh "mv \"${testingBox}/${tempAppName}\" \"${testingBox}/${testAppName}\""
                    }
                    catch (exc) {
                        error("file not found! \"${exc}\"")
                    }
                }
            }
        }
        
        stage("cooked by Macaron") {
            steps {
                script {
                    def testingBox = "/Users/jenkins/Documents/SDKServerTesting"
                    def testAppName = "current.app.zip"

                    try {
                        sh "cat \"${testingBox}/${testAppName}\""
                    }
                    catch (exc) {
                        echo "test failed: ${exc}"
                    }
                }
            }
        }
    }
}

@NonCPS
def removeFile(filename) {
    sh "[ -f \"${filename}\" ] && rm \"${filename}\" || echo \"file(${filename}) not found\""
}

@NonCPS
def isStartByUppersteamBuild() {
    def buildCauses = currentBuild.rawBuild.getCauses()
    //echo buildCauses

    boolean isByPollingSCM = false
    for (buildCause in buildCauses) {
        echo "${buildCause}"

        if ("${buildCause}" ==~ /job\/.*\[hudson\.model\.Cause\$UserIdCause.*\]/) {
        // if ("${buildCause}".contains("hudson.triggers.SCMTrigger\$SCMTrigger")) {
        /// job/test-pipe/51[hudson.model.Cause$UserIdCause@ba8d3b63]
        /// job/.*\[hudson\.model\.Cause\$UserIdCause.*\]
            isByPollingSCM = true
        }
    }

    return isByPollingSCM
}

