# jenkins-tf

## required pluggins
````
aws steps
````
````
aws credentials
````
````
terraform
````
````
stage view
````

## configure tools
- manage jenkins -> tools -> terraform
- install from binary.com

## credentials
- select kind as aws credentials
- add access key and secret key

## create pipeline

- generate pipeline syntax for git and aws credentials
````
pipeline {
    agent any 
 
    stages{
        stage('code-pull'){
            steps{
                git branch: 'main', url: 'https://github.com/abhipraydhoble/jenkins-tf.git'
            }
        }
        
        stage('eks-cluster'){
            steps{
                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws-cred', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                 sh 'terraform init'
                 sh 'terraform validate'
                 sh 'terraform plan'
                 sh 'terraform apply -auto-approve'
               }
            }
        }
    }
}
````

## after eks cluster creation add destroy stage
````
stage('tf-destroy'){
            steps{
                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws-cred', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                 sh 'terraform destroy -auto-approve'
                }
            }
````
