pipeline {
    agent any
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
