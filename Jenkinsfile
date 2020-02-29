node('Noeud_Jenkins'){

    def registry = "mounabal/image_test"
    def registryCredential = "dockerhub"
    def image="${registry}:version-${env.BUILD_ID}"
    def registry_prod = "mounabal/image_prod"
    def image_prod="${registry_prod}:version-${env.BUILD_ID}"
    
   stage('CloningGit'){
          
        git url: 'https://github.com/aaugrain/devopsapps.git',
            branch: 'develop'
    }
    
    stage('Build_with_Maven'){
          
        sh 'mvn clean package'
        
    }
     stage('Publish test results') {
        junit 'target/surefire-reports/*.xml'
    }

    
    stage ('Clonegit_Dockerfile'){
        
        git url: 'https://github.com/aaugrain/Projet_CD.git',
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
            playbook: 'playbook.yml',
            inventory: 'ansible/inventory',
            extras: "--extra-vars 'image=$image'"
            
        )
    
    }
    
    stage('CloningGit'){
          
        git url: 'https://github.com/aaugrain/devopsapps.git',
            branch: 'master'
    }
    
    stage('Build_with_Maven'){
          
        sh 'mvn clean package'
        
    }
    
    stage ('Clonegit_Dockerfile'){
        
        git url: 'https://github.com/aaugrain/Projet_CD.git',
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
            playbook: 'playbook_prod.yml',
            inventory: 'ansible/inventory',
            extras: "--extra-vars 'image_prod=$image_prod'"
            
        )    
        
    }
        
}
