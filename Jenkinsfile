node{
     
    stage('SCM Checkout'){
        git url: 'https://github.com/munusudanaecommerceapp/java-web-app-docker.git',branch: 'master'
    }
    
    stage(" Maven Clean Package"){
      def mavenHome =  tool name: "Maven", type: "maven"
      def mavenCMD = "${mavenHome}/bin/mvn"
      sh "${mavenCMD} clean package"
      
    } 
    
    
    stage('Build Docker Image'){
        sh 'docker build -t munudockers/java-web-app-docker .'
    }
    
    stage('Push Docker Image'){
        withCredentials([string(credentialsId: 'dockerpwd', variable: 'dockerhubpasswd')]) {
          sh "docker login -u munudocker -p ${dockerhubpasswd}"
        }
        sh 'docker push munudockers/java-web-app-docker'
     }
     
      stage('Run Docker Image In Dev Server'){
        
        def dockerRun = ' docker run  -d -p 8080:8080 --name java-web-app munudockers/java-web-app-docker'
         
         sshagent(['DOCKER_SERVER']) {
          sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.10.205 docker stop java-web-app || true'
          sh 'ssh  ubuntu@172.31.10.205 docker rm java-web-app || true'
          sh 'ssh  ubuntu@172.31.10.205 docker rmi -f  $(docker images -q) || true'
          sh "ssh  ubuntu@172.31.10.205 ${dockerRun}"
       }
       
    }
     
     
}
