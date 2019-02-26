properties([
    pipelineTriggers([pollSCM('H/4 * * * *')])
])

pipeline {
    agent any
    stages {
        stage("SCM") {
            steps {
                checkout scm
            }
        }

        stage("prepare file from mailbox") {
            when {
                expression {
                    return isStartByUppersteamBuild()
                }
            }
            steps {
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

        def matcher = ("${buildCause}" ==~ /^job.*\[hudson\.model\.Cause.*\]$/)
        if (matcher) {
        // if ("${buildCause}".contains("hudson.triggers.SCMTrigger\$SCMTrigger")) {
        /// job/test-pipe/51[hudson.model.Cause$UserIdCause@ba8d3b63]
        /// job/.*\[hudson\.model\.Cause\$UserIdCause.*\]
        /// \/.*\[hudson\.model\.Cause\\$UserIdCause.*\]
            isByPollingSCM = true
        }
    }

    return isByPollingSCM
}

