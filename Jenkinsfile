node 
    {
    pollSCM('*/1 * * * *')
    stage('Git CheckOut') {
//    git 'ssh://git@code.airtelworld.in:7999/air/cms-2.0.git'
    git branch: 'feature/sit-cms2.0', url: 'ssh://git@code.airtelworld.in:7999/air/cms-2.0.git'
    }
    
    stage('Build') {
    sh 'mvn clean install -U'
    }

    
    stage('Backup'){
    sh '''ssh -q -tt testuser@10.56.110.235 << EOF 
    if [ -f /home/testuser/CMS_PROD/cms-0.0.1-SNAPSHOT.jar ]; then  cp /home/testuser/CMS_PROD/cms-0.0.1-SNAPSHOT.jar /home/testuser/CMS_PROD/cms-0.0.1-SNAPSHOT.jar-`date +%F-%M`; else echo "File does not exist"; fi
    exit 0
    EOF'''
    }

    dir('/home/jenkins/.jenkins/workspace/cms-sit-deploy/target/') {
    stage('Deploy and Run') {
    sh '''scp -r cms-0.0.1-SNAPSHOT.jar testuser@10.56.110.235:/home/testuser/CMS_PROD/
    ssh -q -tt testuser@10.56.110.235 << EOF
    sudo kill -9 `pgrep -f cms-0.0.1-SNAPSHOT.jar`
    nohup java -jar -Djavax.net.ssl.trustStore=/usr/java/jdk1.8.0_111/jre/lib/security/cacerts -Djavax.net.ssl.trustAnchors=/usr/java/jdk1.8.0_111/jre/lib/security/cacerts /home/testuser/CMS_PROD/cms-0.0.1-SNAPSHOT.jar --spring.config.location=/home/testuser/CMS_PROD/application.properties  &
    exit 0
    EOF'''
    }   
    }
    emailext body: '"Please go to ${BUILD_URL} and verify the build"', subject: '"Job \'${JOB_NAME}\' (${BUILD_NUMBER}) is waiting for input"', to: 'a_mayank.koli@airtel.com'


}    
    


    
