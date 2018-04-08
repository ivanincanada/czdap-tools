pipeline {
    agent any
	 environment {
        czdaptools = 'czdap-tools'
        githubRepo    = 'https://github.com/wsad-io/czdap-tools.git'
		credentialsDir = 'czdap-tools/credentials-decrypt'
		credentialsScript = 'decrypt.py'
		gitBranch = 'wsadPipeline'
		configJsonData = '{   "base_url": "https://czdap.icann.org",   "token": "XmoPgREwsSrpSoCt9WUsSRptrrRXGeHA" }'
		zonedataDir = 'czdap-tools/zonedata-download'
		SQLHost = '35.196.142.233'
		SQLUser = 'dbadmin'
		SQLPass = '3212333222355321233322321'
		SQLDB = 'zonedata'
		DEPENDENCIES = 'curl nano git python3 mysql-client python-crypto python-requests'
    }
    stages 
        {
        stage('Clone Repo') 
            {
            steps {
		          sh '''
		            if [ -d "${czdaptools}" ]; then
		                echo "Removing old Folder"
                        rm "${czdaptools}" -r
                    fi
                    
                    git clone ${githubRepo}
                    
                    cd ${czdaptools}
                    
                    git branch ${gitBranch}
                    git pull
		          '''
                  }
            }            
        stage('Decrypt Credentials') 
            {
            steps {
		          sh '''
                    cd ${credentialsDir}
                    echo ${configJsonData} >> config.json
                    cp test/czdap.private.key ./czdap.private.key
		            python ${credentialsScript}
		            cd ..
		          '''
                  }
            }
        stage('Refresh ZoneData') 
            {
            steps 
                {
		        sh '''
                    cd ${zonedataDir}
                    echo ${configJsonData} >> config.json
		            python download.py
		            cd ..
		        '''
                }
            }
        stage('Check ZoneFiles') 
            {
            steps 
                {
                sh '''
                    cd ${zonedataDir}
                    ls zonefiles -l -s -S -h
                '''
                }
        }
    }
}
