class Globals
{
  static String uploadOutput
}

pipeline
{
    agent any
    options
    {
        buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '10'))
        disableConcurrentBuilds()
        timeout(time: 120, unit: 'MINUTES')
    }


    environment
    {
        dotnettoolsfolder = 'C:\\dotnet-tools\\'
    }

    stages
    {
        stage('Install Package Creation')
        {
            steps
            {
                bat "dotnet tool update \"Skyline.DataMiner.CICD.Tools.Packager\" --local"
            }
        }

        stage('Install DataMiner Deploy')
        {
            steps
            {
                bat "dotnet tool update \"Skyline.DataMiner.CICD.Tools.DataMinerDeploy\" --local --prerelease"
            }
        }

        stage('Create DMAPP')
        {
            steps
            {
                bat "dotnet dataminer-package-create dmapp \"${WORKSPACE}\" --name HelloFromJenkins -o \"${WORKSPACE}\" --type automation"
            }
        }

       stage("Deploy DMAPP")
       {
            steps
            {
            withCredentials([usernamePassword(credentialsId: 'DirectDeployExample', usernameVariable: 'DATAMINER_DEPLOY_USER', passwordVariable: 'DATAMINER_DEPLOY_PASSWORD')])
              {
                bat "dotnet dataminer-package-deploy from-artifact --path-to-artifact \"${WORKSPACE}\\HelloFromJenkins.dmapp\" --dm-server-location dms-it-dev.skyline.local --debug"
              }
            }
       }
    }
    post
    {
        cleanup
        {
            cleanWs()
        }
    }
}