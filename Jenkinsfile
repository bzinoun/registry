
node {
    stage('checkout') {
        checkout scm
    }

     def nodeHome = tool name: 'node-7.4.0-gulp', type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
     env.PATH = "${nodeHome}/bin:${env.PATH}"
    
         def mvnHome = tool 'maven-3.3.3'
     env.PATH = "${mvnHome}/bin:${env.PATH}"	

     def javaHome = tool'java8'		
     env.PATH = "${javaHome }/bin:${env.PATH}"

   stage('check tools') {
        sh "node -v"
        sh "npm -v"
        sh "bower -v"
        sh "gulp -v"
        sh "java -version"	
        sh "mvn -version"
    }

    stage('npm install') {
        sh "npm install"
    }

    stage('clean install') {
        sh "mvn clean"
    }


    stage('docker') {
        
    //docker.withRegistry('http://localhost:5000') {
   
 docker.image('maven:3.3.3-jdk-8').inside {
	git url: "https://github.com/bzinoun/registry.git"
	sh 'mvn  clean install'
}
        
        git url: "https://github.com/bzinoun/registry.git"
        sh "git rev-parse HEAD > .git/commit-id"
        def commit_id = readFile('.git/commit-id').trim()
        println commit_id
    //rm file commit-id
        stage "docker build"
       
   def image = docker.build("registry:${commit_id}", '.')
   def container = image.run('-P')
    }
    }

