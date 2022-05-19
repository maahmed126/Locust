pipeline {
    agent any

    environment {
        RELEASE = "${env.BRANCH_NAME == "release/sprint/" || env.BRANCH_NAME == "main"}"
        DEPLOY_TO = "${env.BRANCH_NAME == "release/sprint/" ? "release" : env.BRANCH_NAME == "main" ? "production" : ""}"
    }
    
    stages {

        stage('Deploy') {
            
            when { expression { "${env.BRANCH_NAME == 'release/sprint/*'}" } }            
            
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
                            def INPUT_PARAMS = input message: 'Approval for Release Deployment', ok: 'Next', parameters: [choice(name: 'ENVIRON', choices: ['Release','Hotfix'].join('\n'),description: 'Please select the way of Deployment')]
                            println("Selected Environment: " + INPUT_PARAMS)
                            env.environ=INPUT_PARAMS
                            println("output: " + env.environ)
                        }
                        sh 'echo $env.environ'
                    }
                }

                stage("Started Deployment to QA") {
                    steps {
                        sh 'echo $environ'
                        sh 'echo Started QA release'
                        sh 'echo QA Release Skipped due to Hotfix'
                    }
                }

                stage('Approval to UAT') {
                    when { expression { env.environ == 'Release' } }
                    agent none
                    steps {
                        script {
                                def approver = input id: 'Deploy', message: 'Deploy to UAT?', submitter: 'pavan.prabhu,admin', submitterParameter: 'deploy_approver'
                                echo "This deployment was approved by ${approver}"
                        }
                    }
                }

                stage("Started Deployment to UAT") {
                    when { expression { env.environ == 'Release' } }
                    steps {
                            sh 'echo Started release'
                    }
                }

                stage('Approval to PROD') {
                    when { expression { env.environ == 'Release' } }
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
            deleteDir()

            /* clean up tmp directory */
            dir("${workspace}@tmp") {
                deleteDir()
            }
        }
    }
}
