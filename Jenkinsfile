pipeline {
   agent { dockerfile true }
   environment {
      CI = 'true'
   }
   stages {
         stage('Install Packages') {
            steps {
               sh 'npm install'
            }
         }

         stage('Test') {
            steps{
               sh 'npm run test'
            }
         }
         stage('Create Build Artifacts') {
            steps {
               sh 'npm run build'
            }
         }


         stage('Validate CF syntax') {
            steps {
               withAWS(region:'ap-southeast-2', credentials:'aws-credentials') {
                  sh 'aws cloudformation validate-template --template-body file://s3.yml'
                   }
               }
            }
            stage('S3 Bucket') {
               steps {
                  withAWS(region:'ap-southeast-2', credentials:'aws-credentials') {
                           sh 'aws cloudformation deploy \
                        --template-file s3.yml \
                        --stack-name pipeline-practice-stack \
                        --parameter-overrides BucketName=tobiasdevops.link'
                  }
               }
            }
         stage('Deploy to Production') {
               steps {
                  withAWS(region:'ap-southeast-2', credentials:'aws-credentials') {
                        s3Delete(bucket: 'tobiasdevops.link', path:'**/*')
                        s3Upload(bucket: 'tobiasdevops.link', workingDir:'build', includePathPattern:'**/*')


                     }
                  }
               }
         }
}
