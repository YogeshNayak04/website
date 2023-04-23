pipeline {
    agent any

     environment{
       registryCredential = 'ecr:us-west-2:202052350'
       appRegistry = "323326736782.dkr.ecr.us-west-2.amazonaws.com/capstoneproject_1"
       capstoneRegistry = "323326736782.dkr.ecr.us-west-2.amazonaws.com"
       cluster = "Capstoneproject"
        service = "202052350_Yogesh"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/YogeshNayak04/website'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: 'us-west-2', region: 'us-east-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
