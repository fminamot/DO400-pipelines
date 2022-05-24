
hello-pipeline
```
pipeline {
    agent any
    stages {
        stage('Hello') {
            steps {
                echo 'Hello World'
            }
        }
    }
}
```
multi-pipeline
```
pipeline {
    agent any
    
    stages {
        stage('Hello') {
            steps {
                echo 'Hello World'
            }
        }
        stage('Log date') {
            steps {
                sh 'date'
            }
        }
    }
}
```

story-count-test
```
pipeline {
  agent any
	stages {
		stage('Checkout') {
    		steps {
    		    git branch: 'main', url: 'https://github.com/RedHatTraining/DO400-apps'
    		}
    		}
    		stage('Test Word Count') {
    		steps {
    		    sh './story-count/test-wc.sh ./story-count/frankenstein.txt 433'
    		}
		}
	}
}
```

hello-scripted
```
node {
    echo 'Hello workd'
}
```

webapp-tests
```
node('nodejs') {
    stage('Checkout') {
        git url: 'https://github.com/fminamot/DO400-apps', branch: 'scripted-pipelines'
    }

    stage('Test') {
        if (isChanged('simple-webapp/backend')) {
            echo 'Detected Backend changes'
            sh 'node ./simple-webapp/backend/test.js'
        }
        if (isChanged('simple-webapp/frontend')) {
            echo 'Detected Frontend changes'
            sh 'node ./simple-webapp/frontend/test.js'
        }
    }
}

def isChanged(dir) {
    changedFilepaths = sh(
        script: 'git diff-tree --no-commit-id --name-only -r HEAD',
        returnStdout: true
    ).trim().split('\n')
    
    for (filepath in changedFilepaths) {
        if (filepath.startsWith(dir)) {
            return true;
        }
    }        
    return false
}
```

webapp-api-test
```
pipeline {
    agent {
        node {
            label 'nodejs'
        }
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/fminamot/DO400-apps', branch: 'scripted-pipelines'
            }
        }
        stage('Test') {
            steps {
                script {
                    def verbs = ['GET', 'POST', 'PUT']
                    for (int i = 0; i < verbs.size(); ++i) {
                        sh "simple-webapp/backend/test_api.sh ${verbs[i]}"
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
            }
        }
    }
}
```

