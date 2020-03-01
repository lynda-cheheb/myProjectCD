node('slave_node'){

    def registry = "berberose/image_test"
    def registryCredential = "dockerhub"
    def image="${registry}:version-${env.BUILD_ID}"
    def registry_prod = "berberose/image_prod"
    def image_prod="${registry_prod}:version-${env.BUILD_ID}"
    
   stage('CloningGit'){
          
        git url: 'https://github.com/lynda-cheheb/devopsapp.git',
            branch: 'develop'
    }
    
    stage('Build_with_Maven'){
          
        sh 'mvn clean package'
        
    }
     stage('Publish test results') {
        junit 'target/surefire-reports/*.xml'
    }

    
    stage ('Clonegit_Dockerfile'){
        
        git url: 'https://github.com/lynda-cheheb/myProjectCD.git',
            branch: 'master'
    }
    
    def Image_test = stage('BuildImage'){
        
        docker.build("$image",  '.')
        
    }
    
    stage ('Push_DockerHub'){
        
        docker.withRegistry('',registryCredential){
            Image_test.push()
        }
    }
    
    stage('Deploiement Ansible') {
        ansiblePlaybook (
            colorized: true, 
            become: true,
            playbook: 'playbook_Test.yml',
            inventory: 'inventory',
            extras: "--extra-vars 'image=$image'"
            
        )
    
    }
    
    stage('CloningGit'){
          
        git url: 'https://github.com/lynda-cheheb/devopsapp.git',
            branch: 'master'
    }
    
    stage('Build_with_Maven'){
          
        sh 'mvn clean package'
        
    }
    
    stage ('Clonegit_Dockerfile'){
        
        git url: 'https://github.com/lynda-cheheb/myProjectCD.git',
            branch: 'master'
    }
    
    def Image_prod = stage('BuildImage'){
        
        docker.build("$image_prod",  '.')
        
    }
    
    stage ('Push_DockerHub'){
        
        docker.withRegistry('',registryCredential){
            Image_prod.push()
        }
    }
    
    stage('Deploiement Ansible') {
        ansiblePlaybook (
            colorized: true, 
            become: true,
            playbook: 'playbook_Prod.yml',
            inventory: 'inventory',
            extras: "--extra-vars 'image_prod=$image_prod'"
            
        )    
        
    }
        
}
