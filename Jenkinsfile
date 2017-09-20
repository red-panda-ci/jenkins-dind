#!groovy

@Library('github.com/red-panda-ci/jenkins-pipeline-library') _

// Initialize global config
cfg = jplConfig('jenkins-dind','backend','', [hipchat: '', slack: '', email:'redpandaci+jenkinsdind@gmail.com'])

pipeline {
    agent { label 'docker' }

    stages {
        stage ('Build') {
            steps  {
                jplCheckoutSCM(cfg)
            }
        }
        stage ('Test') {
            steps  {
                timestamps {
                    ansiColor('xterm') {
                        sh 'bin/test.sh'
                    }
                }
            }
        }
        stage('Sonarqube Analysis') {
            steps {
                //jplSonarScanner(cfg)
                echo "ToDo: Sonar"
            }
        }
        stage ('Release confirm') {
            when { branch 'release/*' }
            steps {
                jplPromoteBuild(cfg)
            }
        }
        stage ('Release finish') {
            agent { label 'docker' }
            when { branch 'release/*' }
            steps {
                jplCloseRelease(cfg)
            }
        }
        stage ('PR Clean') {
            agent { label 'docker' }
            when { branch 'PR-*' }
            steps {
                deleteDir()
            }
        }
    }

    post {
        always {
            jplPostBuild(cfg)
        }
    }

    options {
        buildDiscarder(logRotator(artifactNumToKeepStr: '20',artifactDaysToKeepStr: '30'))
        disableConcurrentBuilds()
        skipDefaultCheckout()
        timeout(time: 1, unit: 'DAYS')
    }
}
