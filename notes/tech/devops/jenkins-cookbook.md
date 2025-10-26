---
title: Jenkins Cookbook
description: Some declarative pipelines I've used for Jenkins
date: 2018-11-01

tags:
  - jenkins
  - devops

---

## Basic Declarative Pipeline

```groovy
pipeline {
    agent any
    stages {
        stage('checkout') {
            steps {
                // Steps here
            }
        }
        stage('build') {
            steps {
                // Steps here
            }
        }
        stage('tests') {
            parallel {
                stage('AAA') {
                    steps {
                        // ...
                        }
                    }
                }
                stage('BBB') {
                    steps {
                        // ...
                    }
                }
            }
        }
    }
    post {
        always {
            echo 'One way or another, I have finished'
            deleteDir() /* clean up our workspace */
        }
        success {
            echo 'I succeeeded!'
        }
        unstable {
            echo 'I am unstable :/'
        }
        failure {
            echo 'I failed :('
        }
        changed {
            echo 'Things were different before...'
        }
    }
}
```

## Checkout from git with credentials (not needed for multi-branch pipelines)

```groovy
        stage('checkout') {
            steps {
                git url: '<git url>', branch: '<branch>', credentialsId: '<jenkins credId>'
                // ...
            }
        }
```

## Poll git for changes regularly (not needed for multi-branch pipelines)

```groovy
pipeline {
    agent any
    triggers { pollSCM('H 07 * * 1') }
    stages {
        stage('checkout') {
            steps {
                git url: '<git url>', branch: '<branch>', credentialsId: '<jenkins credId>', poll: true
                // ...
            }
        }
    }
```

## AnsiColor output

```groovy
        stage('AAA') {
            steps {
                ansiColor('xterm') {
                    // ...
                }
            }
    }
```

## Set environment variables (various ways)

```groovy
    stages {
        environment {
            GLOBALVAR = 'this is a global variable'
        }
        stage('AAA') {
            environment {
                 STAGEVAR = 'this is a stage variable'
            }
            steps {
                script {
                    env.MYVAR = "my variable"
                    env.VAR = "the value of MYVAR is ${env.MYVAR}"
                    env.VARFROMPROGRAM = sh(returnStdout:true, script: "<run a program and even use ${env.VAR} here>").trim()
                }
            }
        }
    }
```

## Inject Jenkins credentials into environment variable

```groovy
        stage('AAA') {
            steps {
                withCredentials([usernameColonPassword(credentialsId: '<jenkins credId', variable: 'USERPASS')]) {
                    sh "echo $USERPASS"
                }
            }
        }
```

## Select JDK and Maven version (defined in Jenkins)

```groovy
        stage('AAA') {
            steps {
                withEnv(["JAVA_HOME=${tool 'JDK 8'}","PATH+MAVEN=${tool 'Maven 3'}/bin:${env.JAVA_HOME}/bin"]) {
                    sh "mvn -Dmaven.test.skip=true clean package"
                    // ...
                }
            }
        }
```

## Ignore exit code while inside a shell step

A shell step invokes bash with -xe options. Setting +e will disable immediate exit from an earlier failed step. Note that the earlier exit code will always be 0. If your intent is to capture the exit code but not fail the step, then see the next section.

```groovy
                stage('AAA') {
                    steps {
                        sh """
                            set +e
                            // do something...
                            set -e
                            // this line will run regardless of previous exit code
                        """
                    }
                }
```

## Ignore exit code from a shell step (and capture it)

The shell step comes with a returnStatus option that will return the exit code and won't fail the step. This means you can capture the exit code and then defer the decision to fail the pipeline toward the end of the pipeline.

Note: for some reason, using environment variables will not work

```groovy
def pipeline_should_fail = false
pipeline {
    ....
        stage('AAA') {
            steps {
                script {
                    def step_result = sh (returnStatus: true, script: """
                        // do something
                    """)
            if (step_result != 0) { pipeline_should_fail = true }
                    sh """
                        echo 'this line will run'
                    """
                }
            }
        }
        // other stages

        post {
            always {
                // fail the pipeline if the earlier stage failed
                script {
                    if (pipeline_should_fail) {
                        currentBuild.result = "FAILURE"
                    }
                }
            }
        }
```

## Mark pipeline as failed when a stage is unstable

The post block is placed as the last step in a stage.

A stage is usually unstable when a Jenkins publisher determine so e.g. JUnit test has failures

You cannot mark a build as successful from unstable status.

```groovy
            post {
                unstable {
                    script {
                        echo "Problems encountered, marking build as FAILED"
                        currentBuild.result = 'FAILURE'
                    }
                }
            }
```

## Run SonarQube analysis

Requires SonarQube plugin

```groovy
               stage('sonar') {
                    steps {
                        withSonarQubeEnv('Sonar') {
                            script {
                                def sonar = tool name: 'Sonar Runner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
                                sh """
                                $sonar/bin/sonar-scanner -e -Dsonar.host.url=${SONAR_HOST_URL} -Dsonar.login=${SONAR_AUTH_TOKEN} \
                                    -Dsonar.projectName='<project name>' \
                                    -Dsonar.projectVersion=<version> \
                                    -Dsonar.projectKey=<project key> \
                                    -Dsonar.sources=<sources paths, no wildcards> \
                                    -Dsonar.inclusions=<inclusions paths, wildcards ok> \
                                    -Dsonar.exclusions=<exclusions paths, wildcards ok> \
                                    -Dsonar.java.binaries=<java binaries paths, no wildcards> \
                                    -Dsonar.java.libraries=<java libraries paths, wildcards ok> \
                                    -Dsonar.junit.reportPaths=<surefire report paths, wildcards ok>
                                """
                            }
                        }
                   }
                }
```

## Archive build/test artifacts

```groovy
    post {
        always {
            archiveArtifacts artifacts: 'build/libs/**/*.jar', fingerprint: true
            junit 'build/reports/**/*.xml'
        }
    }
```

## Sending notification to Email / Slack

Requires Email-ext and Slack plugins

```groovy
    post {
        always {
            script {
                if (currentBuild.currentResult == 'SUCCESS') {
                    env.SLACKCOLOR = "good"
                } else {
                    env.SLACKCOLOR = "danger"
                }
                env.GITCOMMIT = sh(returnStdout:true, script: 'git rev-parse HEAD | cut -c 1-6').trim()
                env.GITCOMMITUSER = sh(returnStdout:true, script: "git --no-pager show -s --format='%an' ${env.GITCOMMIT}").trim()
                env.GITCOMMITMSG = sh(returnStdout: true, script: 'git log -1 --pretty=%B').trim()

                env.SLACKMSG = """
                    *${currentBuild.currentResult}: <${env.BUILD_URL}|${env.JOB_NAME} #${env.BUILD_NUMBER}>*
                    commit ${env.GITCOMMIT} ${env.GITCOMMITMSG}
                    by ${env.GITCOMMITUSER} on branch ${env.BRANCH_NAME}
                    """

                env.EMAILMSG = """
                    ${currentBuild.currentResult}: ${env.JOB_NAME} #${env.BUILD_NUMBER}:
                    Commit ${env.GITCOMMIT} ${env.GITCOMMITMSG}
                            by ${env.GITCOMMITUSER} on branch ${env.BRANCH_NAME}

                            Check console output at ${env.BUILD_URL} to view the results
                    """
            }

            slackSend (
                message: "${env.SLACKMSG}",
                color: "${env.SLACKCOLOR}",
                baseUrl: "<jenkins integration url from slack>",
                channel: "#<channel>",
                tokenCredentialId: "<jenkins credId>"
                )
        }
        failure {
            emailext (
                to: "<email addresses>",
                subject: "FAIL: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "${env.EMAILMSG}"
                )
        }
    }
```

## Invoke OWASP Dependency Check scan

```groovy
        stage('dependency-check') {
            steps {
                dependencyCheckAnalyzer scanpath: '',
                    outdir: '.',
                    datadir: '',
                    suppressionFile: '',
                    hintsFile: '',
                    zipExtensions: '',
                    isAutoupdateDisabled: false,
                    includeHtmlReports: true,
                    includeVulnReports: false,
                    includeJsonReports: false,
                    includeCsvReports: false,
                    skipOnScmChange: false,
                    skipOnUpstreamChange: false

                dependencyCheckPublisher canComputeNew: false, defaultEncoding: '', healthy: '', pattern: '', unHealthy: ''
            }
        }
```
