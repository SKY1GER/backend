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
        def appVersion = "" //variable declaration
        nexusUrl = 'nexus.daws80s.online:8081'
        // region = "us-east-1"
        // account_id = "315069654700"
    }

    stages{
        stage("read json"){
            steps{
                script{ 
                    def packageJson = readJSON file: 'package.json'
                    def appVersion = packageJson.version
                    echo "application version is $appVersion"
                }
            }
        }
        stage("install dependencies"){
            steps{
                script{
                    sh  '''
                    npm install
                    ls -ltr
                    echo "application version : ${env.appVersion}"   '''
               }
           }
        }
        stage("Build"){
            steps{
                sh '''
                  zip -q -r backend-1.0.0.zip * -x Jenkinsfile -x backend-1.0.0.zip
                  ls -ltr
                '''
            }
            
        }
        stage('Nexus Artifact Upload'){
            steps{
                script{
                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: "${nexusUrl}",
                        groupId: 'com.expense',
                        version: "${appVersion}",
                        repository: "backend",
                        credentialsId: 'nexus-auth',
                        artifacts: [
                            [artifactId: "backend" ,
                            classifier: '',
                            file: "backend-" + "${appVersion}" + '.zip',
                            type: 'zip']
                        ]
                    )
                }
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


