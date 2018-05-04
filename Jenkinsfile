node {


    /* Requires the Docker Pipeline plugin to be installed */
    docker.image('maven:3-alpine').inside('-v $HOME/.m2:/root/.m2') 
    {
        
        stage('Build') 
        {
            checkout(
                [$class: 'GitSCM', 
                branches: [[name: '*/master']], 
                doGenerateSubmoduleConfigurations: false, 
                extensions: [], 
                submoduleCfg: [], 
                userRemoteConfigs: [[url: 'https://github.com/lbrandis/simple-java-maven-app']]]
                )

            try{
            sh 'mvn -B -DskipTests clean install'
            }
            catch(e){
                currentBuild.result = "FAILED"
                notifyFailed()
                throw e
            }
        }

        stage("Checkstyle") 
        {
            sh "mvn checkstyle:checkstyle"
                
            step([$class: 'CheckStylePublisher',
                canRunOnFailed: true,
                defaultEncoding: '',
                healthy: '100',
                pattern: '**/target/checkstyle-result.xml',
                unHealthy: '90',
                useStableBuildAsReference: true
            ])
        }


        stage('Test')
        {
            sh 'mvn test'
            junit 'target/surefire-reports/*.xml'
        }

        stage('Deliver') { 
                sh './jenkins/scripts/deliver.sh' 
        }
    }
}

def notifyStarted() { /* .. */ }
 
def notifySuccessful() { /* .. */ }
 
def notifyFailed() {
    emailext body: 'A Test EMail', 
        recipientProviders: [[$class: 'DevelopersRecipientProvider'], 
                             [$class: 'RequesterRecipientProvider']], 
        subject: 'Test', 
        to: 'leonid.brandis@mac.com'
}
