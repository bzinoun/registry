
node {
    stage('checkout') {
        checkout scm
    }

    // uncomment these 2 lines and edit the name 'node-4.6.0' according to what you choose in configuration
     def nodeHome = tool name: 'node-7.4.0-gulp', type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
     env.PATH = "${nodeHome}/bin:${env.PATH}"
    
         def mvnHome = tool 'maven-3.3.3'
     env.PATH = "${mvnHome}/bin:${env.PATH}"	

     def javaHome = tool'java8'		
     env.PATH = "${javaHome }/bin:${env.PATH}"

     def DOCKER_HOST="tcp://192.168.1.225:2375"
        env.PATH = "${DOCKER_HOST}:${env.PATH}"

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

    stage('clean') {
        sh "mvn clean"
    }

    /*stage('backend tests') {
        try {
            sh "mvn test"
        } catch(err) {
            throw err
        } finally {
            step([$class: 'JUnitResultArchiver', testResults: '(*)(*)/target/surefire-reports/TEST-*.xml'])// replace (*) ->*
        }
    }*/


    stage('packaging') {
        sh "mvn package -Pprod -DskipTests"
    }
        stage('docker') {
        
 node("docker") {
    docker.withRegistry('localhost:5000') {
    
        git url: "https://github.com/bzinoun/registry.git"
        sh "git rev-parse HEAD > .git/commit-id"
        def commit_id = readFile('.git/commit-id').trim()
        println commit_id
    //rm file commit-id
        stage "docker build"
        def app = docker.build("registry:${commit_id}", '.')
        
    
        stage "docker publish"
        app.push 'master'
        app.push "${commit_id}"
    }
}
    }
}
