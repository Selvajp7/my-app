node{
   stage('SCM Checkout'){
     git 'https://github.com/selva7290/my-app.git'
   }
   stage('Maven Build'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
      stage('Build Docker Imager'){
   sh 'docker build -t selva7290/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u selva7290 -p ${dockerPassword}"
    }
   sh 'docker push selva7290/myweb:0.0.2'
   }
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 13.232.200.253:7290"
   sh "docker tag selva7290/myweb:0.0.2 13.232.200.253:7290/selva:1.0.0"
   sh 'docker push 13.232.200.253:7290/selva:1.0.0'
   }
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest selva7290/myweb:0.0.2' 
   }
}
}
