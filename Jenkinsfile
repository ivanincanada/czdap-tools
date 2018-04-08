pipeline {
    agent any

    stages {
        stage('Decrypt Credentials') {
            steps {
                cd credentials-decrypt
		py decrypt.py
		cd ..
            }
        }
        stage('Refresh ZoneData') {
            steps {
                cd zonedata-download
		py download.py
            }
        }
        stage('Check ZoneFiles') {
            steps {
                ls zonefiles -l -s -S -h
            }
        }
    }
}
