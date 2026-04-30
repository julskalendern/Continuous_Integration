pipeline {
    agent any

    stages {
     
        stage('docker') {
            steps {
                
                sh '''
				sleep 10
				docker run -d --name ngx -p 9889:80 -v /var/jenkins_home/workspace/helvete_git:/usr/share/nginx/html/ nginx:latest
				sleep 10
				'''
            }
        }
        stage('tost') {
            steps {
               
                script {
                    def resp = sh(script: "curl -s -o /dev/null -w '%{http_code}' http://localhost:9889", returnStatus: true, returnStdout: true)
                    if (resp != '200') {
                        error "AHTUNG code ${resp}"
                    }
                }
       
                script {
                    def loc = sh(script: "cat index.html | tr -d '\\n\\r ' | md5sum | awk '{print \$1}'", returnStdout: true).trim()
                    def rem = sh(script: "curl -s http://localhost:9889 | tr -d '\\n\\r' | md5sum | awk '{print \$1}'", returnStdout: true).trim()
                    if (loc != rem) {
                        error "AHTUNG md5"
                    }
                }
            }
        }
    }
    post { 
		always {
            sh "docker rm -f ngx || true"
        }
        failure {
            
            sh '''
            echo "AHTUNG"
            '''
        }
       
    }
}
