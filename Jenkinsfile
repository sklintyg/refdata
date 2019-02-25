#!groovy

def buildVersion = "1.0.0.${BUILD_NUMBER}"
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
        def gitBranch = GIT_BRANCH
        if (gitBranch == "devtest" || gitBranch == "master") {
            shgradle "uploadArchives tagRelease -DbuildVersion=${buildVersion}"
        } else {
            def customBuildName = CUSTOM_BUILD_NAME
            if (customBuildName?.trim()) {
                shgradle "uploadArchives tagRelease -DcustomBuildName=${projectName}-${customBuildName} -DbuildVersion=${BUILD_NUMBER} -Dintyg.tag.prefix=${customBuildName}-"
            } else {
                shgradle "uploadArchives tagRelease -DcustomBuildName=${projectName}-${gitBranch.replaceAll('/', '')} -DbuildVersion=${BUILD_NUMBER} -Dintyg.tag.prefix=${gitBranch}-"
            }
        }
    }
}

stage('notify') {
    node {
        util.notifySuccess()
    }
}
