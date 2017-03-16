node() {
	   
    stage 'Checkout'
        git url: 'https://github.com/emcconsulting/Devops-MicroService.git', branch: 'int-test'
        def mvnHome = tool 'M3'
    stage 'Maven Build'
		//sh "sudo rm -rf target"
		sh "sudo ${mvnHome}/bin/mvn -q clean package "
	stage 'Sonar Validation'	
    	sh "sudo ${mvnHome}/bin/mvn sonar:sonar -Dsonar.host.url=http://192.168.33.80:9000"
	stage 'Docker Build'
		sh "sudo docker build -t emcdevops/tnt-utilities ."
	stage 'integration testing'
		sh "cd tnt-utilities.it"
		sh "sudo mvn clean install -DskipTests"
		sh "sudo docker build -t emcdevops/tnt-utilities-it ."
		sh "cd ../src/main/resources/integration"
		sh "sudo docker-compose up --abort-on-container-exit"
		sh "sudo docker cp intg-test-cont:test-output ../../../../target/test-output"
		sh "sudo docker-compose rm -f –v    --cleanup residuals"
		publishHTML([allowMissing: true, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'target/test-output/citrus-reports', reportFiles: 'citrus-test-results.html', reportName: 'citrus integration test report '])
		
	stage 'Docker Run'
	    sh"sudo docker stop \$(sudo docker ps -a -q)"
		sh "sudo docker rm \$(sudo docker ps -a -q)"
		sh "sudo docker run -p 9091:9091  emcdevops/tnt-utilities -d &"
}
