pipeline {
    agent any
    parameters {
        choice(
            choices: ['AA', 'BB'],
            description: '',
            name: 'REQUESTED_ACTION'
        )
    }
    stages {
        stage('Debug Docker') {
            steps {
                sh '''
                    echo "Current PATH: $PATH"
                    echo "Current USER: $(whoami)"
                    echo "Docker location: $(which docker)"
                    docker --version
                    ls -l /Users/romaindehaese/.docker/run/docker.sock
                    docker run --rm alpine echo "Docker is running inside Jenkins."
                '''
            }
        }
        stage('conditional') {
            steps {
                script {
                    if (fileExists('/tmp/myfile')) {
                        echo "file exists"
                        docker.image('alpine').inside {
                            sh 'echo "This is running inside a Docker container because the file exists."'
                        }
                    } else {
                        echo "file does not exist"
                        docker.image('alpine').inside {
                            sh 'echo "This is running inside a Docker container because the file does not exist."'
                        }
                    }
                }
            }
        }
        stage('Speak') {
            when { expression { params.REQUESTED_ACTION == 'AA' } }
            steps {
                script {
                    echo "Hello, AA!"
                    docker.image('alpine').inside {
                        sh 'echo "This is running inside a Docker container because AA was requested."'
                    }
                }
            }
        }
        stage('Trivy Scan') {
            steps {
                script {
                    echo "Running Trivy vulnerability scan"
                    sh '''
                        # Install Trivy if not already installed
                        if ! command -v trivy &> /dev/null
                        then
                            echo "Trivy not found, installing..."
                            brew install trivy
                        fi
                        # Run Trivy scan on the Alpine image
                        trivy image alpine
                    '''
                }
            }
        }
    }
}
