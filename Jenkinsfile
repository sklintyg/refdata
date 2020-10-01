#!groovy

def buildVersion = "1.0-SNAPSHOT"

stage('checkout') {
    node {
        git url: 'https://github.com/sklintyg/refdata.git', branch: GIT_BRANCH
        util.run { checkout scm }
    }
}

stage('build') {
    node {
        shgradle11 "clean build"
    }
}

stage('tag and upload') {
    node {
        shgradle11 "uploadArchives -DbuildVersion=${buildVersion}"
    }
}

stage('notify') {
    node {
        util.notifySuccess()
    }
}
