properties([
    pipelineTriggers([pollSCM('H/4 * * * *')]),
    pipelineTriggers([pollSCM('H/4 * * * *'), <object of type org.jenkinsci.plugins.fstrigger.triggers.FolderContentTrigger>])
])

pipeline {
    agent any
    stages {
        stage("SCM") {
            steps {
            //    git poll: true, url: 'git@github.com:willsbor/jenkinsfile-test.git'
            //    checkout SCM
            checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '4a1f8ef9-5a5f-4bfd-80e2-6ad127a6e855', url: 'https://github.com/willsbor/jenkinsfile-test']]])

            }
        }

        stage("Build") {
            steps {
                print "DEBUG ....."
            }
        }
    }
}
