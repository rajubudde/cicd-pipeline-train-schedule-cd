pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        Stage('DeployToStaging') {
              when {
                  branch 'master'
              }
              steps
                   {
                  withCredentilas([usernamePassword(CredentialId:'weblogicserver_login', UsernameVaraiable:'USERNAME',PasswordVariable:'USERPASS')]){
                      sshPublisher(
                          failOnError: true,
                          continueOnError: false,
                          publishers:[
                              sshPublisherDesc(
                                  ConfigName:'Staging',
                                  sshCredenilas:[
                                      Username:"$USERNAME"
                                      encryptedPassphrase:"$USERPASS"
                                      ],
                                  transfers:[
                                      ssh transfers(
                                          sourcefiles:'dist/trainSchedule.zip',
                                          removePrefix:'dist/',
                                          remoteDirectory: '/tmp',
                                          execCommand:'sudo/usr/systemctl stop train_schedule && rm -rf /opt/train_schedule/ * && unzip /tmp/traiSchedule.zip -d/opt/train_schedule && sudo /usr/bin/systemctl start train_schedule'
                                          )
                                      ]
                                  )
                              ]
                          )
                  }
              }
              }
               Stage('DeployToProduction'){
              when {
                  branch 'master'
              }
               steps
                    {
                     input "Does the staging environment look ok?"
                     milestone(1)
                     withCredentilas([usernamePassword(CredentialId:'weblogicserver_login', UsernameVaraiable:'USERNAME',PasswordVariable:'USERPASS')]){
                       sshPublisher(
                                   failOnError: true,
                                   continueOnError:false,
                                   publishers:[
                              sshPublisherDesc(
                                  ConfigName:'Production',
                                  sshCredenilas:[
                                      Username:"$USERNAME"
                                      encryptedPassphrase:"$USERPASS"
                                      ],
                                  transfers:[
                                      ssh transfers(
                                          sourcefiles:'dist/trainSchedule.zip',
                                          removePrefix:'dist/',
                                          remoteDirectory: '/tmp',
                                          execCommand:'sudo/usr/systemctl stop train_schedule && rm -rf /opt/train_schedule/ * && unzip /tmp/traiSchedule.zip -d/opt/train_schedule && sudo /usr/bin/systemctl start train_schedule'
                                          )
                                      ]
                                  )
                              ]
                          )
                  }
              }
              }
              
    }
}
