#jenkins job



#prepare
sh 'mkdir - p $BUILD_ID/job'
sh 'cp $JENKINS_HOME/*.xml $BUILD_ID/'

#Keys and Secrets 
sh 'cp $JENKINS_HOME/*.key $BUILD_ID/'
sh 'cp -r "JENKINS_HOME/secrets $BUILD_ID/'

$JENKINS_HOME/jobs/ BUILD_ID/jobs 

