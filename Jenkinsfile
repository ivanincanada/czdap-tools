pipeline {
    agent 
        {
        node 
            {
            label 'GoogleWorker'
            }
        }
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
        stage('Check/Install Dependencies') 
            {
            steps 
                {
                sh '''
                    sudo apt-get update
                    sudo apt-get install ${DEPENDENCIES} -y
                '''
                }
        }
        stage('CheckDB Access') 
            {
            steps 
                {
                sh '''
                    mysql -u ${SQLUser} -h ${SQLHost} -p${SQLPass} -e "exit"
                '''
                }
        }
        stage('Clone Repo') 
            {
            steps {
		          sh '''
		            if [ -d "${czdaptools}" ]; then
						cd ${czdaptools}
						# git branch ${gitBranch}
						git pull		                						
						
					else		
						
						git clone ${githubRepo}		
						cd ${czdap-tools}
						# git branch ${gitBranch}
						
                    fi                                                            
                  git branch ${gitBranch}
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
		                python download.py &
		                sleep 1
		                python download2.py 
		                cd ..
		                '''
                  
                }
            }
        stage('Import to MySQL') 
            {
            steps 
                {
                sh '''
                    cd ${zonedataDir}
					cd zonefiles
                    for f in *.gz; do
                    do
                    zcat ${f} >> file.tmp
                    mysql -u ${SQLUser} -h $SQLHost -p${SQLPass} -e "use ${SQLDB}" -e "
                    LOAD DATA LOCAL INFILE 'file.tmp'
                    INTO TABLE zonedata
                    FIELDS TERMINATED BY '\t'
                    LINES TERMINATED BY '\n'
                    IGNORE 0 LINES
                    (HOST, TTL, RecordType, RecordClass, RecordData, Data1, Data2, Data3, Data4, Data5, Data6, Data7, Data8, Data9, Data10)"
                    rm file.tmp
                    done
                    
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
