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
                    }
                }

                stage("Started Deployment to QA") {
                    steps {
                       // script {
                       //     if(INPUT_PARAMS=="Release")
                       //     {
                       //         println("Selected release")
                       //     }
                       //     else
                       //     {
                       //         println("Hotfix Selected")
                       //     }
                       // }
                        sh 'echo $env.environ'
                        sh 'echo Started QA release'
                        sh 'echo QA Release Skipped due to Hotfix'
                    }
                }

                stage('Approval to UAT') {
                    // no agent is used, so executors are not used up when waiting for approvals
                    agent none
                    steps {
                        script {
                            //if (env.ENVIRONMENT == "Release") {
                                def approver = input id: 'Deploy', message: 'Deploy to UAT?', submitter: 'pavan.prabhu,admin', submitterParameter: 'deploy_approver'
                                echo "This deployment was approved by ${approver}"
                            //}
                            //else
                                sh 'echo "UAT Approval Skipped due to Hotfix" '
                        }
                    }
                }

                stage("Started Deployment to UAT") {
                    steps {
                        //if (env.ENVIRONMENT == 'Release')
                            sh 'echo Started release'
                        //else
                            sh 'echo UAT Deployment Skipped due to Hotfix'
                    }
                }

                stage('Approval to PROD') {
                    // no agent is used, so executors are not used up when waiting for approvals
                    agent none
                    steps {
                        script {
                            //if (env.ENVIRONMENT == 'Release') {
                                def approver = input id: 'Deploy', message: 'Deploy to PROD?', submitter: 'pavan.prabhu,admin', submitterParameter: 'deploy_approver'
                                echo "This deployment was approved by ${approver}"
                            //}
                            //else
                                echo "Approval for PROD Skipped due to Hotfix"
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
