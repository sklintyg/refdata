#!groovy

def gitBranch = GIT_BRANCH
def customBuildName = CUSTOM_BUILD_NAME
def buildVersion = gitBranch == "devtest" ? "0-SNAPSHOT" : "1.0.0.${BUILD_NUMBER}"
def projectName = "refdata"

stage('checkout') {
    node {
        git url: "https://github.com/sklintyg/refdata.git", branch: GIT_BRANCH
        util.run { checkout scm }
    }
}

stage('build') {
    node {
        try {
            shgradle "--refresh-dependencies clean build"
        } finally {
            publishHTML allowMissing: true, alwaysLinkToLastBuild: true, keepAll: true, reportDir: 'build/reports/allTests', \
                reportFiles: 'index.html', reportName: 'JUnit results'
        }
    }
}

stage('tag and upload') {
    node {
        if (gitBranch == "devtest") {
            shgradle "uploadArchives -DbuildVersion=${buildVersion}"
        } else if (gitBranch == "master") {
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
