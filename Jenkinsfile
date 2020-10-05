#!groovy

def buildVersion = "1.4.1.${BUILD_NUMBER}"
def projectName = "refdata"

stage('checkout') {
    node {
        git url: "https://github.com/sklintyg/refdata.git", branch: GIT_BRANCH
        util.run { checkout scm }
    }
}

stage('build') {
    node {
        shgradle "--refresh-dependencies clean build"
    }
}

stage('tag and upload') {
    def gitBranch = GIT_BRANCH
    def customBuildName = CUSTOM_BUILD_NAME
    node {
        if (gitBranch == "master") {
            shgradle "uploadArchives tagRelease -DbuildVersion=${buildVersion}"
        } else if (customBuildName?.trim()) {
            shgradle "uploadArchives tagRelease -DcustomBuildName=${projectName}-${customBuildName} -DbuildVersion=${BUILD_NUMBER} -Dintyg.tag.prefix=${customBuildName}-"
        } else {
            shgradle "uploadArchives tagRelease -DcustomBuildName=${projectName}-${gitBranch.replaceAll('/', '')} -DbuildVersion=${BUILD_NUMBER} -Dintyg.tag.prefix=${gitBranch}-"
        }
    }
}

stage('notify') {
    node {
        util.notifySuccess()
    }
}