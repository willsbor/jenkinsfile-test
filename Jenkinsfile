
/// polling 週期請參考 cron job
properties([
    pipelineTriggers([pollSCM('H/4 * * * *')])
])

pipeline {
    agent any
    stages {
        stage("SCM") {
            steps {
                /// checkout source by scm setting
                checkout scm
            }
        }

        stage("prepare file from mailbox") {
            when {
                expression {
                    /// 只有當由上游 Job 觸發此 Job 時會執行
                    return isStartByUppersteamBuild()
                }
            }
            steps {
                /// 因為要將信箱內的 app.zip 轉到定義好的工作目錄下
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
                /// 執行要實行的動作
                script {
                    def testingBox = "/Users/jenkins/Documents/SDKServerTesting"
                    def testAppName = "current.app.zip"

                    try {
                        /// 換成 執行 Macaron 的指令
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
    
    boolean isByPollingSCM = false
    for (buildCause in buildCauses) {
        echo "${buildCause}"

        def matcher = ("${buildCause}" ==~ /^job.*\[hudson\.model\.Cause.*\]$/)
        if (matcher) {
            isByPollingSCM = true
        }
    }

    return isByPollingSCM
}

