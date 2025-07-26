# jenkins-tf


**AWS CLI Installation:**

````
sudo apt install unzip -y
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
````
````
aws --version
````
**Terraform Installation:Ubuntu**
````
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform
````


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
