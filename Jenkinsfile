#!groovy

def buildVersion = "1.0.0.${BUILD_NUMBER}"
def projectName = "refdata"

stage('checkout') {
    //println 'checkout'
    node {
        /*
        git url: "https://github.com/sklintyg/refdata.git", branch: GIT_BRANCH
        util.run { checkout scm }
        */
    }
}

stage('build') {
    //println 'build'
    node {
        /*
        try {
            shgradle "--refresh-dependencies clean build"
        } finally {
            publishHTML allowMissing: true, alwaysLinkToLastBuild: true, keepAll: true, reportDir: 'build/reports/allTests', \
                reportFiles: 'index.html', reportName: 'JUnit results'
        }
        */
    }
}

stage('tag and upload') {
    //println 'tag and upload'
    def gitBranch = GIT_BRANCH
    //println "gitBranch: ${gitBranch}"

    node {
        // om GIT_BRANCH är skilt från develop/master så
        // 1. bygg upp artifactId med refdata-GIT_BRANCH-version
        // 2. tagga koden med GIT_BRANCH-version
        // annars gör som idag

        if (gitBranch == "develop" || gitBranch == "master") {
            def shgradleCmd = "uploadArchives tagRelease "
                + "-DbuildVersion=${buildVersion} "
            //println "shgradleCmd 1: " + shgradleCmd
            //shgradle "uploadArchives tagRelease -DbuildVersion=${buildVersion}"
        } else {
            def shgradleCmd = "uploadArchives tagRelease "
                + "-DcustomBuildName=${projectName}-${gitBranch.replaceAll('/', '')} "
                + "-DbuildVersion=${BUILD_NUMBER} "
                + "-Dintyg.tag.prefix=${gitBranch}-"

            //println "shgradleCmd 2: " + shgradleCmd

            //shgradle "uploadArchives tagRelease "
            //        + "-DcustomBuildName=${projectName}-${gitBranch.replaceAll('/', '')} "
            //        + "-DbuildVersion=${BUILD_NUMBER} -Dintyg.tag.prefix=${gitBranch}-"

        }
    }
}

stage('notify') {
    //println 'notify'
    node {
        //util.notifySuccess()
    }
}
