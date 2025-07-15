pipeline {
  agent any
  stages {

    stage('Clone') {
      steps {
        echo 'Cloning source code'
        git branch: 'master', url: 'https://github.com/SpringMuch/WebBanHangOnline'
      }
    }

    stage('Restore Packages') {
      steps {
        echo 'Restoring packages...'
        bat 'dotnet restore'
      }
    }

    stage('Build') {
      steps {
        echo 'Building project...'
        bat 'dotnet build --configuration Release'
      }
    }

    stage('Tests') {
      steps {
        echo 'Running tests...'
        bat 'dotnet test --no-build --verbosity normal || echo No tests or build failed.'
      }
    }

    stage('Publish to Folder') {
      steps {
        echo 'Publishing to ./publish...'
        bat 'dotnet publish -c Release -o ./publish'
      }
    }

    stage('Copy to IIS Folder') {
      steps {
        echo 'Copying to C:\\wwwroot\\myproject...'
        bat 'xcopy "%WORKSPACE%\\publish" "C:\\wwwroot\\myproject" /E /Y /I /R'
      }
    }

    stage('Deploy to IIS') {
      steps {
        powershell '''
          Import-Module WebAdministration
          if (-not (Test-Path IIS:\\Sites\\MySite)) {
            New-Website -Name "MySite" -Port 81 -PhysicalPath "C:\\wwwroot\\myproject"
          } else {
            Restart-WebAppPool -Name "DefaultAppPool"
          }
        '''
      }
    }

  }
}
