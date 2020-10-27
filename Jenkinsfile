pipeline {
    agent any

    tools {
/**      Uncomment if want to have specific java versions installed, otherwise maven tool will use jenkins default embedded java 8
 */
//        jdk 'jdk8'
        maven 'maven3'
    }

    stages {
        stage('install and sonar parallel') {
            steps {
                parallel(
                        install: {
                            sh "mvn -U clean test cobertura:cobertura -Dcobertura.report.format=xml"
                        },
                        sonar: {
                                try {
                                    sh "mvn sonar:sonar"
                                } catch(error){
                                    echo "The sonar server could not be reached ${error}"
                                }
                        }
                )
            }
            post {
                always {
                    junit '**/target/*-reports/TEST-*.xml'
                    step([$class: 'CoberturaPublisher', coberturaReportFile: 'target/site/cobertura/coverage.xml'])
                }
            }
        }
        stage ('deploy'){
            steps{
                configFileProvider([configFile(fileId: 'our_settings', variable: 'SETTINGS')]) {
                    sh "mvn -s $SETTINGS deploy -DskipTests"
                }
            }
        }
    }
}
