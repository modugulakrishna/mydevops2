pipeline {
    agent any
    stages {
        stage ('Code Checkout') {
            steps {
				checkout([$class: 'GitSCM',
				branches: [[name: '*/master']],
				userRemoteConfigs: [[url: 'mkmohan@devops-tomcat:addressbook.git']]])
			}
        }
        stage ('Dev Compile') {
            steps {
                withMaven {
                    sh 'mvn compile'
                }
            }
        }
        stage ('PMD Analysis') {
            steps {
                withMaven {
                    sh 'mvn -P metrics pmd:pmd'
                }
            }
            post {
				always {
					pmd canComputeNew: false, defaultEncoding: '', healthy: '', pattern: '**/pmd.xml', unHealthy: ''
				}
			}
        }
        stage ('QA Unit Test') {
            steps {
                withMaven {
                    sh 'mvn test'
                }
            }
        }
        stage ('QA Metrics') {
            steps {
                withMaven {
                    sh 'mvn cobertura:cobertura -Dcobertura.report.format=xml'
                }
            }
            post {
                always {
                    cobertura autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: '**/target/site/cobertura/coverage.xml', conditionalCoverageTargets: '70, 0, 0', failUnhealthy: false, failUnstable: false, lineCoverageTargets: '80, 0, 0', maxNumberOfBuilds: 0, methodCoverageTargets: '80, 0, 0', onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false
                }
            }
        }
        stage ('QA Package') {
            steps {
                withMaven {
                    sh 'mvn package'
                }
            }
            post {
                always {
                    sh 'scp /var/lib/jenkins/workspace/QA-PACKAGE/target/addressbook.war mkmohan@devops-tomcat:/var/lib/tomcat8/webapps'
                }
            }
        }
    }
}
