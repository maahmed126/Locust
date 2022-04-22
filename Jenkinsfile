pipeline {
        agent any
        stages {
            stage('Start') {
             steps {
                 sh 'mkdir -p locust_results' 
                    sh 'python3 -m venv venv/'
                    sh 'pip3 install -r requirements.txt'
                    sh 'ls'
                    sh '''locust -f locustfile.py --headless --only-summary -u 100 -t 14s -r 10 --html index.html'''
                    sh 'cp -r /var/jenkins_home/workspace/Locust/*.html  locust_results' 
                    buildDiscarder(logRotator(numToKeepStr: '30', artifactNumToKeepStr: '30'))
             }
                    
        post {
        always {

                publishHTML([allowMissing: false,
                      alwaysLinkToLastBuild: true,
                      keepAll: true,
                      reportDir: 'locust_results',
                      reportFiles: 'index.html',
                      reportName: 'Locust-Report',
                      includes: 'index.html'
     ])        
            }
        }
 }
        }
}
