pipeline {
    agent any
    
    stages {
        
        stage('Build') { 
            steps {
                def mvn_version = 'M3'
                withEnv( ["PATH+MAVEN=${tool mvn_version}/bin"] ) {
                  //sh "mvn clean package"
                }    
                sh "mvn -f pom.xml package" 
            }
        }
        stage('Create docker image') { 
            steps {
                script {
                    def scmVars = checkout([
                        $class: 'GitSCM',
                        doGenerateSubmoduleConfigurations: false,
                        userRemoteConfigs: [[
                            url: 'https://github.com/hoshikawa2/runHTMLJenkins.git'
                          ]],
                        branches: [ [name: '*/master'] ]
                      ])
                sh "docker build -f Dockerfile -t runhtml:${scmVars.GIT_COMMIT} ." 
                }
            }
        }
        stage('Push image to OCIR') { 
            steps {
                script {
                    def scmVars = checkout([
                        $class: 'GitSCM',
                        doGenerateSubmoduleConfigurations: false,
                        userRemoteConfigs: [[
                            url: 'https://github.com/hoshikawa2/runHTMLJenkins.git'
                          ]],
                        branches: [ [name: '*/master'] ]
                      ])
                sh "docker login -u ${params.REGISTRY_USERNAME} -p ${params.REGISTRY_TOKEN} iad.ocir.io"
                sh "docker tag runhtml:${scmVars.GIT_COMMIT} ${params.DOCKER_REPO}:${scmVars.GIT_COMMIT}"
                sh "docker push ${params.DOCKER_REPO}:${scmVars.GIT_COMMIT}" 
                env.GIT_COMMIT = scmVars.GIT_COMMIT
                sh "export GIT_COMMIT=${env.GIT_COMMIT}"
                }
               }
            }
        }
}
