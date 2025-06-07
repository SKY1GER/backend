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

    stages{
        stage("install dependencies"){
            steps{
                sh  '''
                   npm install
                   ls -ltr
                '''
            }
            
        }
        // stage("plan"){
        //     when{
        //         expression{
        //             params.action == 'apply'
        //         }
        //     }
        //     steps{
        //         sh '''
        //           cd 01-vpc
        //           terraform plan
        //         '''
        //     }
            
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
    

