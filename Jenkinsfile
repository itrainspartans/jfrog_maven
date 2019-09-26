node {
    def server = Artifactory.server('satyasai.jfrog.io')
    def buildInfo = Artifactory.newBuildInfo()
    def rtMaven = Artifactory.newMavenBuild()
    
    
    stage ('Checkout & Build') {
        git url: 'https://github.com/itrainspartans/jfrog_maven.git'
    }
 
    stage ('Unit Test') {
        rtMaven.tool = 'Maven-3.6.1' // Tool name from Jenkins configuration
        rtMaven.run pom: 'pom.xml', goals: 'clean compile test'
    }
    
    stage('SonarQube Analysis') {
        //rtMaven.run pom: 'maven-example/pom.xml', goals: 'clean org.jacoco:jacoco-maven-plugin:prepare-agent package sonar:sonar -Dsonar.host.url=https://sonarcloud.io -Dsonar.organization=pattabhi -Dsonar.login=df5bb81bae9ba310d6a38135b957227ba6ecd32c '
     
         
      }
    
    stage ('Artifactory configuration') {
        // Obtain an Artifactory server instance, defined in Jenkins --> Manage..:
         
        rtMaven.tool = 'Maven-3.6.1' // Tool name from Jenkins configuration
        rtMaven.deployer releaseRepo: 'ajitkatta-artifactory-primary-0-releases-local', snapshotRepo: 'ajitkatta-artifactory-primary-0-snapshots-local', server: server
        rtMaven.resolver releaseRepo: 'ajitkatta-artifactory-primary-0-releases', snapshotRepo: 'ajitkatta-artifactory-primary-0-snapshots', server: server
        rtMaven.deployer.deployArtifacts = false // Disable artifacts deployment during Maven run
     }
            
    stage ('Install') {
        rtMaven.run pom: 'pom.xml', goals: 'install', buildInfo: buildInfo
     }
 
    stage ('Deploy') {
        rtMaven.deployer.deployArtifacts buildInfo
    }
        
    stage ('Publish build info') {
        server.publishBuildInfo buildInfo
    }
    
    stage('Status Notification'){
        def mailRecipients = "ramesh.thadivada@gmail.com"
        def subject = "${env.JOB_NAME} - Build #${env.BUILD_NUMBER}- ${currentBuild.result}"
       
        mail bcc: '',
             from: 'manee2k6@gmail.com',
             to: 'manee2k6@gmail.com, ramesh.thadivada@gmail.com',
             subject: subject,
             body: "Build Number: #${env.BUILD_NUMBER}  Status:${currentBuild.result} Build URL: ${env.BUILD_URL}"
          
   }
   
}
