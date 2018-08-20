node 
    {
    pollSCM('*/1 * * * *')
    stage('Git CheckOut') {
    git 'ssh://git@code.airtelworld.in:7999/air/retailerbatchprocesses.git'
    }

    stage('Build') {
    sh 'mvn  -f RetailersBatch/pom.xml clean install -U'
    }

    stage('Backup'){
    sh '''ssh -q -tt testuser@10.56.110.172 << EOF 
    if [ -f /home/testuser/RetailersBatch.jar ]; then  cp /home/testuser/RetailersBatch.jar /home/testuser/RetailersBatch.jar-`date +%F-%M`; else echo "File does not exist"; fi
    exit 0
    EOF'''
    }

    dir('/home/jenkins/.jenkins/workspace/retailer_sit_deploy/RetailersBatch/target') {
    stage('Deploy and Run') {
    sh '''scp -r RetailersBatch.jar testuser@10.56.110.172:/home/testuser/'''
    sh '''#!/bin/bash
    ssh -q -tt testuser@10.56.110.172 << EOF
    sudo kill -9 \\`pgrep -f RetailersBatch.jar\\`
    nohup java -jar /home/testuser/RetailersBatch.jar &
    exit 0
    EOF
    '''
    }   
    }

    emailext body: '"Please go to ${BUILD_URL} and verify the build"', subject: '"Job \'${JOB_NAME}\' (${BUILD_NUMBER}) is waiting for input"', to: 'a_TANWEER.ALAM@airtel.com'
//    emailext body: '"Please go to ${BUILD_URL} and verify the build"', subject: '"Job \'${JOB_NAME}\' (${BUILD_NUMBER}) is waiting for input"', to: 'a_mayank.koli@airtel.com'

}    
    


    
