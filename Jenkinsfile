pipeline {
    agent GoogleWorker

    stages {
        stage('Decrypt Credentials') {
            steps {
		sh '''
                cd credentials-decrypt
		py decrypt.py
		cd ..
		'''
            }
        }
        stage('Refresh ZoneData') {
            steps {
		sh '''
                cd zonedata-download
		py download.py
		'''
            }
        }
        stage('Check ZoneFiles') {
            steps {
                ls zonefiles -l -s -S -h
            }
        }
    }
}
