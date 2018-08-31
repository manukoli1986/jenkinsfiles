node 
    {
        pollSCM('*/1 * * * *')

    stage('Git CheckOut') {
        git 'ssh://git@code.airtelworld.in:7999/air/retailer_springboot.git'
    }

    stage('Build') {
        input 'Proceed to build?'
        sh 'mvn  -f MitraTraining/pom.xml clean install -U'
    }

    stage('Backup'){
        sh '''ssh -q -tt testuser@10.56.110.172 << EOF 
        if [ -f /opt/SPRINGBOOT/mitratraining.jar ]; then  cp /opt/SPRINGBOOT/mitratraining.jar /opt/SPRINGBOOT/mitratraining.jar-`date +%F-%M`; else echo "File does not exist"; fi
        exit 0
        EOF'''
        }

    dir('/home/jenkins/.jenkins/workspace/Pipeline_MitraTraining/MitraTraining/target') {
    stage('Deploy and Run') {
        input 'Proceed to Deploy?'
        sh '''scp -r mitratraining.jar testuser@10.56.110.172:/opt/SPRINGBOOT/'''
        sh '''#!/bin/bash
        ssh -q -tt testuser@10.56.110.172 << EOF
        sudo kill -9 \\`pgrep -f mitratraining.jar\\`
        nohup java -jar /opt/SPRINGBOOT/mitratraining.jar &
        exit 0
        EOF
        '''
    }   
    }

//    emailext body: '"Please go to ${BUILD_URL} and verify the build"', subject: '"Job \'${JOB_NAME}\' (${BUILD_NUMBER}) is waiting for input"', to: 'a_TANWEER.ALAM@airtel.com'
    emailext body: '"Please go to ${BUILD_URL} and verify the build"', subject: '"Job \'${JOB_NAME}\' (${BUILD_NUMBER}) is waiting for input"', to: 'a_mayank.koli@airtel.com'

}    
    


    
