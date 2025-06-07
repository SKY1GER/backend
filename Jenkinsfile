pipeline{
    agent{
        label "Agent-1"
    }
    options{        
        // Timeout counter starts before agent is allocated
        timeout(time: 30, unit: "MINUTES")
        disableConcurrentBuilds()
        ansiColor('xterm')
    }
    environment{
        def appVersion = ''
    }

    stages{
        stage("read json"){
            steps{
                script{ 
                    def packageJson = readJSON file: 'package.json'
                    appVersion = packageJson.version
                    echo "application version is $appVersion"
                }
            }
        }
        stage("install dependencies"){
            steps{
                sh  '''
                   npm install
                   ls -ltr
                   echo $appVersion
                '''
            
        }
        stage("Build"){
            steps{
                sh '''
                  zip -r backend-${appVersion} * -x Jenkinsfile -x backend-${appVersion}
                  ls -ltr
                '''
            }
            
        }

        }
    post{
        always{
            echo "this will run always"
            deleteDir()
        }
        success{
            echo "this will run only if build success"
        }
        failure{
            echo "this will run only if build fails"
        }
    }
    
}
}
