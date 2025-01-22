pipeline {
    agent any
    environment {
        NVM_DIR = "/home/alite-148/.nvm"
        BUILD_DIR = "/var/lib/jenkins/workspace/reactDeployment/build"
        DEPLOY_DIR = "/var/lib/jenkins/workspace/reactDeployment/deploy"
        WORKDIR = "/var/lib/jenkins/workspace/ReactPipeline/react-demo"
        NODE_SERVER_DIR="/var/lib/jenkins/workspace/reactDeployment/nodeServer/server"
    }
    stages {        
        stage ("Clone") {
            when {
                expression {return !env.RERUN_STAGE || env.RERUN_STAGE == "Clone" || env.PREV_STAGE == "Clone"}
            }
            steps {
                script {
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: '*/main']],
                        doGenerateSubmoduleConfigurations: false,
                        extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: "."]],
                        submoduleCfg: [],
                        userRemoteConfigs: [[
                            credentialsId: "githubCreds",
                            url: "https://github.com/Ketan34790/react-demo.git"
                        ]]
                    ])
                    env.PREV_STAGE = "Clone"
                }  
            }
        }

        stage ("Build") {
            when {
                expression {return !env.RERUN_STAGE || env.RERUN_STAGE == "Build" || env.PREV_STAGE == "Clone" || env.PREV_STAGE == "Build"}
            }
            steps {
                script {
                sh """
                    #!/bin/bash
                    cd $WORKDIR
                    ls -la 
                    . $NVM_DIR/nvm.sh > /dev/null 2>&1        
                    nvm install 20 > /dev/null 2>&1
                    nvm use 20 
                    npm install
                    npm run build
                    ls
                    zip -r reactBuild.zip build -x "./build/node_modules/*"
                    ls
                    cp reactBuild.zip $BUILD_DIR
                """
                env.PREV_STAGE = "Build"
            }
        }

        stage ("ServerCopy") {
            when {
                expression {return !env.RERUN_STAGE || env.RERUN_STAGE == "ServerCopy" || env.PREV_STAGE == "Build" || env.PREV_STAGE == "ServerCopy"}
            }
            steps {
                script {
                sh '''
                    #!/bin/bash
                    #Moving the build to the server

                    set -e && set -x
                    cd $DEPLOY_PATH
                    ls -la
    
                    unzip -o reactBuild.zip 

                    cd build || { echo "Error: build directory not found"; exit 1; }

                    cp -r "$NODE_SERVER_PATH" .

                    cd server || { echo "Error: server directory not found"; exit 1; }

                    
                ''' 
                
                }
            }
        }

        stage ("Deploy") {
            when {
                expression {return !env.RERUN_STAGE || env.RERUN_STAGE == "Deploy" || env.PREV_STAGE == "ServerCopy" || env.PREV_STAGE == "Deploy"}
            }
            steps {
                script {
                    sh '''
                    #!/bin/bash
                    cd $DEPLOY_DIR 
                    ./reactDeploy.sh
                    '''
                }
            }
        }        
    }
    post {
        always {
            echo 'Pipeline finished.'
        }
        failure {
            echo 'Pipeline failed!'
        }
        success {
            echo 'Pipeline succeeded!'
        }
    }
}
}