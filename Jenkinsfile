pipeline {
    agent any

    environment {
        RELEASE = "${env.BRANCH_NAME == "release/sprint/" || env.BRANCH_NAME == "main"}"
        DEPLOY_TO = "${env.BRANCH_NAME == "release/sprint/" ? "release" : env.BRANCH_NAME == "main" ? "production" : ""}"
    }
    
    stages {

        stage('Deploy') {
            // when { branch pattern: "main", comparator: "REGEXP"}
            // when { expression { params.BRANCH_NAME == 'main' } }
            // when { expression { env.DEPLOY_TO == "release" } }
            
            when {
                expression { "${env.BRANCH_NAME == 'release/sprint/*'}" }
            }            
            
            stages('Deployment Flow') {

                stage("Started Deployment to DEV") {
                    steps {
                        sh 'echo Started DEV release'
                    }
                }

                stage('Approval to QA') {
                    // no agent is used, so executors are not used up when waiting for approvals
                    agent none
                    steps {
                        script {
                            def approver = input id: 'Deploy', message: 'Deploy to QA?', submitter: 'pavan.prabhu,admin', submitterParameter: 'deploy_approver'
                            echo "This deployment was approved by ${approver}"
                        }
                    }
                }

                stage("Started Deployment to QA") {
                    steps {
                        sh 'echo Started QA release'
                    }
                }

                stage('Approval to UAT') {
                    // no agent is used, so executors are not used up when waiting for approvals
                    agent none
                    steps {
                        script {
                            def approver = input id: 'Deploy', message: 'Deploy to UAT?', submitter: 'pavan.prabhu,admin', submitterParameter: 'deploy_approver'
                            echo "This deployment was approved by ${approver}"
                        }
                    }
                }

                stage("Started Deployment to UAT") {
                    steps {
                        sh 'echo Started release'
                    }
                }

                stage('Approval to PROD') {
                    // no agent is used, so executors are not used up when waiting for approvals
                    agent none
                    steps {
                        script {
                            def approver = input id: 'Deploy', message: 'Deploy to PROD?', submitter: 'pavan.prabhu,admin', submitterParameter: 'deploy_approver'
                            echo "This deployment was approved by ${approver}"
                        }
                    }
                }

                stage("Started Deployment to PROD") {
                    steps {
                        sh 'echo Started PROD release'
                    }
                }
            }
        }
    }
    post {
        always {
            /* clean up our workspace */
            deleteDir()

            /* clean up tmp directory */
            dir("${workspace}@tmp") {
                deleteDir()
            }
        }
    }
}
