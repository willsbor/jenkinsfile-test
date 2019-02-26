properties([pipelineTriggers([pollSCM('H/1 * * * *')])])

pipeline {
    agent any
    stages {
        stage("SCM") {
            steps {
            //    git poll: true, url: 'git@github.com:willsbor/jenkinsfile-test.git'
                checkout SCM
            }
        }

        stage("Build") {
            steps {
                print "DEBUG ....."
            }
        }
    }
}
