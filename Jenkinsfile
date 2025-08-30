pipeline{
    agent{
        label "AGENT-1"
    }
    options{        
        // Timeout counter starts before agent is allocated
        timeout(time: 30, unit: "MINUTES")
        disableConcurrentBuilds()
        ansiColor('xterm')
    }
    parameters{
        booleanParam(name: 'deploy', defaultValue: false, description: 'Toggle this value')
    }
    environment{
        def appVersion = "" //variable declaration
        nexusUrl = 'nexus.daws80s.online:8081'
        region = "us-east-1"
        account_id = "196900110977"
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
                sh """
                    npm install
                    ls -ltr
                    echo "application version is $appVersion" 
                """
           }
        }
        stage("Build"){
            steps{
                sh """
                  zip -q -r backend-${appVersion}.zip * -x Jenkinsfile -x backend-${appVersion}.zip
                  ls -ltr
                """
            }
        }
        stage("Deploy"){
            steps{
            sh """
                aws ecr get-login-password --region ${region} | docker login --username AWS --password-stdin ${account_id}.dkr.ecr.${region}.amazonaws.com
                docker build -t ${account_id}.dkr.ecr.${region}.amazonaws.com/expense-backend:${appVersion} .
                docker push ${account_id}.dkr.ecr.${region}.amazonaws.com/expense-backend:${appVersion}
            """
            }
        }
        stage("helm install"){
            steps{
            sh """
                cd helm
                sed -i "s/Image_Version/${appVersion}" values.yaml
                helm install backend .
            """
            }
        }
        
            
        // }
        // stage('Sonar Scan'){
        //     environment {
        //         scannerHome = tool 'sonar-6.0' //referring scanner CLI
        //     }
        //     steps {
        //         script {
        //             withSonarQubeEnv('sonar-6.0') { //referring sonar server
        //                 sh "${scannerHome}/bin/sonar-scanner"
        //             }
        //         }
        //     }
        // }

        // stage("Quality Gate") {
        //     steps {
        //       timeout(time: 30, unit: 'MINUTES') {
        //         waitForQualityGate abortPipeline: true
        //       }
        //     }
        // } 
        // stage('Nexus Artifact Upload'){
        //     steps{
        //         script{
        //             nexusArtifactUploader(
        //                 nexusVersion: 'nexus3',
        //                 protocol: 'http',
        //                 nexusUrl: "${nexusUrl}",
        //                 groupId: 'com.expense',
        //                 version: "${appVersion}",
        //                 repository: "backend",
        //                 credentialsId: 'nexus-auth',
        //                 artifacts: [
        //                     [artifactId: "backend" ,
        //                     classifier: '',
        //                     file: "backend-" + "$appVersion" + '.zip',
        //                     type: 'zip']
        //                 ]
        //             )
        //         }
        //     }
        // }
        // stage('Deploy'){
        //     when{
        //         expression{
        //             params.deploy
        //         }
        //     }
        //     steps{
        //         script{
        //             def params = [
        //                 string(name: 'appVersion', value: "${appVersion}")
        //             ]
        //             build job: 'backend-deploy', parameters: params, wait: false
        //         }
        //     }
        // }

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


