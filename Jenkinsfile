pipeline {
    agent {
        docker {
            image 'ubuntu:noble'
            reuseNode false
        }
    }

    stages {
        stage('Install pre-requisite tools') {
            steps {
                sh """apt-get update
                apt-get -y install git build-essential libcppunit-dev libutfcpp-dev zlib1g-dev cmake
                """
            }
            
        }
        
        stage('Checkout') {
            steps {
                sh "ls -a"
                sh "pwd"
                // Cleanup for rebuild
                sh "ls -A1 | xargs rm -rf"

                sh """git clone https://github.com/johncave/taglib.git ."""
                sh "ls"
            }
        }
        
        stage('Configure') {
            steps {
                sh "cmake -B./taglib/build -DBUILD_SHARED_LIBS=ON -DVISIBILITY_HIDDEN=ON -DBUILD_TESTING=ON -DBUILD_EXAMPLES=ON -DBUILD_BINDINGS=ON -DCMAKE_BUILD_TYPE=Release"
            }
        }
        
        stage('Build') {
            steps {
                sh "cmake --build ./taglib/build/ --config Release"
            }
        }
        
        stage('Test') {
            steps {
                dir('./taglib/build/'){
                sh "ctest -C Release -V --no-tests=error"
                }
            }
        }
    }
    
    post {
        always {
            archiveArtifacts artifacts: 'taglib/build/taglib/', fingerprint: true
        }
    }
}