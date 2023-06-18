def selectStage = ['Add developer']
def orgList = ['APIGEE_ORGANISATION_NAME']

// Parameters Separated with Separator
properties([
    parameters([
        [
            $class: 'ChoiceParameter',
            choiceType: 'PT_SINGLE_SELECT',
            description: 'Select the Organisation',
            name: 'ORGANISATION',
            script: [
                $class: 'GroovyScript',
                script: [classpath: [], sandbox: true, script: "return ${orgList.inspect()}"]
            ]
        ],
        // Separator
        separator(name: "CREATE_PROXY", sectionHeader: "Create Proxy",
          separatorStyle: "border-width: 0",
          sectionHeaderStyle: """
            background-color: #7ea6d3;
            text-align: left;
            padding: 4px;
            color: #343434;
            font-size: 22px;
            font-weight: normal;
            text-transform: uppercase;
            font-family: 'Orienta', sans-serif;
            letter-spacing: 1px;
            font-style: italic;
          """
        ),
        [
          $class: 'DynamicReferenceParameter', 
          choiceType: 'ET_FORMATTED_HTML', 
          description: 'Enter the Proxy Bundle Name to be created',
          name: 'PROXY_BUNDLE_NAME', 
          omitValueField: true,
          referencedParameters: 'ORGANISATION',
          script: [
              $class: 'GroovyScript', 
              fallbackScript: [
                  classpath: [],
                  sandbox: true,
                  script: 
                      'return [\'Error message\']'
              ], 
              script: [
                  classpath: [], 
                  sandbox: true,
                  script: 
                      """ 
                          html=""
                          if (ORGANISATION.contains('')){
                              html="<input name='value' value='' class='setting-input' type='text'>"
                          }
                          else {
                            
                              html="Enter value in PROXY_BUNDLE_NAME to enter the value"
                          }
                          return html
                      """
              ]
          ]
        ]
    ])
])

def selectedProxies = params.PROXY_BUNDLE_NAME.split(',')

pipeline {
    agent any
    environment {
          GCLOUD_DIR = "$JENKINS_HOME/google-cloud-sdk/bin"
          APIGEE_CLI_DIR = "$HOME/.apigeecli/bin"
    }
    stages {
        stage('Installing Dependencies') {
      steps {
          sh '''#!/bin/bash
                echo "Checking for pre-installed dependencies..."
                echo ""
                if [ ! -d "$GCLOUD_DIR" ]; then
                    echo "Installing GCloud CLI..."
                    echo ""
                    cd $JENKINS_HOME
                    curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-cli-412.0.0-linux-x86_64.tar.gz
                    tar -xf google-cloud-cli-412.0.0-linux-*.tar.gz
                    ./google-cloud-sdk/install.sh -q
                    source $JENKINS_HOME/google-cloud-sdk/completion.bash.inc
                    source $JENKINS_HOME/google-cloud-sdk/path.bash.inc
                else--
                    echo "GCloud CLI is already Installed!"
                    echo ""
                fi

                if [ ! -d "$APIGEE_CLI_DIR" ]; then
                    echo "Installing Apigee CLI..."
                    echo ""
                    curl -L https://raw.githubusercontent.com/apigee/apigeecli/main/downloadLatest.sh | sh -
                    
                else
                    echo "Apigee CLI is already Installed!"
                    echo ""
                fi
             '''
      }
    }
        // Logging into GCloud
        stage('Logging into Google Cloud and Get Access Token') {
          steps {
            script {
                withCredentials([file(credentialsId: 'GAccountKey', variable: 'GOOGLE_SERVICE_ACCOUNT_KEY')]) {
                sh '${GCLOUD_DIR}/gcloud auth activate-service-account --key-file ${GOOGLE_SERVICE_ACCOUNT_KEY}'
                env.TOKEN = sh([script: "${GCLOUD_DIR}/gcloud auth print-access-token", returnStdout: true ]).trim()
              }
            }
          }
        }
         
         stage('Create Proxies') {

          steps {
            script {
          // Clone the repository
          checkout([$class: 'GitSCM', branches: [[name: "*/master"]], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'gi-apigee-data']], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'github', url: 'https://github.com/knoldus/Automation-to-create-proxies-in-Apigee.git']]])

              for (proxy in selectedProxies) {
                sh "$APIGEE_CLI_DIR/apigeecli apis create bundle -n ${proxy} -f ${WORKSPACE}/Automation-to-create-proxies-in-Apigee/proxies/${proxy}/apiproxy -o ${params.ORGANISATION} -t ${env.TOKEN}"
               }
             }
           }
        }
      }
    }
  
