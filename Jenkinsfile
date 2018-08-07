/*
   Name:   Jenkinsfile for user_data_service microservice
   Author: Blue Cedar Inc.
           Eugene Chupkin
           May 24, 2018
*/

import hudson.model.*

// Start of JOB, setup variables used throughout the pipeline
// Mark build as in-progress, force disableInprogressNotification to false

println 'Start JOB'

def helm_deploy     = "bc-baas10"
def helm_chart      = "user-service"
def dockerImageName  = "bluecedarnetworks/user_service"
def buildVersion    = "env.BRANCH_NAME"
def buildType       = "Release"
def buildState      = "SUCCESS"
currentBuild.result = "SUCCESS"
def bStageBuildGradle   = false
def bStageTestGradle    = false
def bStageLiquibase     = false


if (env.BRANCH_NAME == 'dev') {
    buildVersion    ='dev'
    buildType       ='Release'
} else if (env.BRANCH_NAME == ("stg")) {
    buildVersion    = 'stg'
    buildType       = 'Release'
} else if (env.BRANCH_NAME == ("master")) {
    buildVersion    = 'production'
    buildType       = 'Release'
} else {
    println "Branch not suitable for build & deploy: " + env.BRANCH_NAME
    currentBuild.result = 'ABORTED'
    return
}

def buildParameters = "\n" \
       + 'Application  : ' + helm_chart + '\n' \
       + 'Build Branch : ' + env.BRANCH_NAME + '\n' \
       + 'Build URL    : ' + env.BUILD_URL + '\n'

println "Build Parameters : " + buildParameters


pipeline {

    options {  
        buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '10'))
        disableConcurrentBuilds()
    }

    triggers {
         pollSCM('H/5 * * * *')
    }

    agent { label 'swarm' }

    stages{
        stage('Init'){
            steps {
                echo "Start CI pipeline for branch: $GIT_BRANCH"
            }
        }

        stage('Sonarqube') {

            steps {
                echo "Execute sonarqube commands"

            }
        }

        stage('Build'){

            steps {

                echo "Building..."

                sh '/usr/local/src/apache-maven/bin/mvn clean package || exit 1'

            }
        }

        stage ('Deploy'){
            parallel{
                stage ('Deploy to Development'){
                    steps {
                        //input message: 'Do you want to deploy to Development? (Click "Proceed" to continue)'

                        echo "Code deploy on Dev environment process started"

                    }
                }

                stage ("Deploy to Test"){
                    steps {
                        echo "Code deploy on Test environment process started"
                    }
                }
            }
        }
    }
}
