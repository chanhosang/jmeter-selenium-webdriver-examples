/*
 ============================================================================
 This Jenkins declarative pipeline (using docker pipeline plugin) demonstrate how to run
 load test (Integration of Selenium with JMeter) with Taurus Tool inside a container build agent.
 The image is based on the official blzemeter/taurus image from Docker Hub.
 ============================================================================

 Pre-requisite:
- Installed the following jenkins plugins:
	* https://wiki.jenkins.io/display/JENKINS/Git+Plugin
    * https://wiki.jenkins.io/display/JENKINS/Docker+Plugin
	* http://wiki.jenkins-ci.org/display/JENKINS/HTML+Publisher+Plugin
*/

def jenkins = [:]

String sectionHeaderStyle = '''
    background: LightGreen;
    text-align: center;
'''

String separatorStyle = '''
    border: none;
'''

properties(
  [
    buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10')),
    parameters([
        [
            $class: 'ParameterSeparatorDefinition',
            sectionHeader: 'Load Setting',
            separatorStyle: separatorStyle,
            sectionHeaderStyle: sectionHeaderStyle
        ],
        string(name: "concurrency", defaultValue: "1", description: 'How many concurrent connections to execute the test?'),
        string(name: "iterations", defaultValue: "1", description: 'Specify the number of times (iteration) to execute the test.'),
        string(name: "ramp_up", defaultValue: "1s", description: 'If you are running more than 1 thread, please increase the ramp-up period accordingly.'),
        [
            $class: 'ParameterSeparatorDefinition',
            sectionHeader: 'Test Environment Setting',
            separatorStyle: separatorStyle,
            sectionHeaderStyle: sectionHeaderStyle
        ],
        string(name: "selenium_grid_host", defaultValue: "172.50.2.1", description: 'Specify the IP address or hostname to Selenium Grid.'),
        [
            $class: 'ParameterSeparatorDefinition',
            sectionHeader: 'Flags',
            separatorStyle: separatorStyle,
            sectionHeaderStyle: sectionHeaderStyle
        ],
        booleanParam(
            description: "Enable to send interim and final test result to BlazeMeter Service (https://a.blazemeter.com/).",
            name: "run_blazemeter_reporter",
            defaultValue: false
        )
      ]
    )
  ]
)

pipeline {
    agent {
        label 'master'
    }

    options {
        // disableConcurrentBuilds()
        timestamps()
        skipDefaultCheckout()
    }


    stages {

        stage('Preparation') {
            steps {
                // Enabled skipDefaultCheckout()
                script {
                    // sh 'printenv'
                    echo "git url: ${scm.userRemoteConfigs[0].url}"
                    echo "git branch: ${scm.branches[0].name}"

                    // checkout from version control configured in pipeline job
                    checkout scm // git branch: 'main', credentialsId: '<credentialsId>', url: '<repositoryUrl>'
                }

                stash name: 'bzt-source', includes: 'scripts/bzt/**,jmeter/**'
            }
		}


        stage('Run Load Testing') {
            agent {
                docker {
                    image 'blazemeter/taurus:latest'
                    label 'master'
                    args '--entrypoint=' //args '-u root --entrypoint=\'/bin/sh\''
                }
            }
            steps {

                unstash "bzt-source"

                echo 'Run load test with BlazeMeter Taurus, generate and publish reports'

                script {
                    def extra_args = ""

                    // If true, uploads tests stats into BlazeMeter.com service
                    if (params.run_blazemeter_reporter) {
                        extra_args = "-report"
                    }

                    // -o modules.jmeter.properties=\"{'jmeter.reportgenerator.overall_granularity':1000}\"
                    sh """
                    mkdir -p results
                    bzt scripts/bzt/theinternet.yml \
                    scripts/bzt/theinternet_reporting.yml \
                    -o settings.env.SELENIUM_GRID_HOST=${params.selenium_grid_host} \
                    -o execution.0.concurrency=${params.concurrency} \
                    -o execution.0.ramp-up=${params.ramp_up} \
                    -o execution.0.iterations=${params.iterations} \
                    -o modules.jmeter.properties=\"{'jmeter.reportgenerator.overall_granularity':10000, \
                    'jmeter.reportgenerator.report_title':Integration of Selenium with JMeter}\"  \
                    ${extra_args}
                    """

                    // Move the reports from taurus artifacts dir location to workspace dir
                    sh """
                    mv /tmp/artifacts .
                    tar -zcvf logs.tar.gz **/*.log
                    tar -zcvf taurus-artifacts-${currentBuild.number}.tar.gz artifacts
                    """
                }
            }

			post {
                always {
                    archiveArtifacts artifacts: "**/*.tar.gz", fingerprint: true

                    perfReport errorFailedThreshold: 50,
                    errorUnstableThreshold: 10,
                    filterRegex: '',
                    sourceDataFiles: '**/results/results.xml'

                    publishHTML([allowMissing: false,
                        alwaysLinkToLastBuild: false,
                        keepAll: false,
                        reportDir: 'artifacts/reports',
                        reportFiles: '**/index.html',
                        reportName: 'HTML Report',
                        reportTitles: ''])
                    cleanWs()
                }
            }
        }

    }

    post {
        always {
            cleanWs()
        }
    }
}
