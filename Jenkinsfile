pipeline {
    agent any
    
    environment {
        VERSION = '1.0'
    }

    stages {
        stage('Clone') {
            steps {
                // Get some code from a GitHub repository
                //cleanWs()
                echo "env $env.GIT_COMMIT"  
                echo 'Git cloning..'
                sh 'git clone https://github.com/tctomek198876/demoapp.git'
            }
        }
        stage('Build') {
            steps {
                dir("demoapp"){
                echo 'Build image'
                sh 'chmod 777 replace'
                sh './replace'
                sh 'mvn package' 
                echo "The build number is ${env.BUILD_NUMBER}"
                echo "VERSION is ${VERSION}"
                script{  SVC= sh(returnStdout: true, script: "docker service ls | grep demoapp | cut -d' ' -f4").trim() }   
                echo "SVC is ${SVC}"
                sh("docker build -f Dockerfile -t demoapp:${VERSION}-${env.BUILD_NUMBER} .")                
                script {
                    if (!SVC){
                    echo "SVC ${SVC} doesn not exist"
                    sh("docker service create --name demoapp --replicas 2 --publish 8090:8080 demoapp:${VERSION}-${env.BUILD_NUMBER}")
                    } 
                    else {
                    echo "SVC ${SVC} exists"   
                    sh("docker service update demoapp --replicas 2  --image=demoapp:${VERSION}-${env.BUILD_NUMBER}")
                    }
                }    

                }
                
            }
         }

        stage("Health Check") {
            steps {
                script {
                    sh 'curl -s http://localhost:8090/demo/'
                    }
                }
            }

       
    }

  post {
    always {
      echo "WsCleanup"
      step([$class: 'WsCleanup'])
    }
  }
    
}
