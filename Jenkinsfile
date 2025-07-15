pipeline {
  agent any

  stages {

    stage('Clone') {
      steps {
        echo 'Cloning source code...'
        git branch: 'master', url: 'https://github.com/SpringMuch/WebBanHangOnline'
      }
    }

    stage('Build') {
      steps {
        echo 'Building ASP.NET project using MSBuild...'
        bat '"C:\\Program Files (x86)\\Microsoft Visual Studio\\2022\\BuildTools\\MSBuild\\Current\\Bin\\MSBuild.exe" WebBanHangOnline.sln /p:Configuration=Release'
      }
    }

    stage('Build Tests') {
      steps {
        echo 'Building test project...'
        bat '"C:\\Program Files (x86)\\Microsoft Visual Studio\\2022\\BuildTools\\MSBuild\\Current\\Bin\\MSBuild.exe" WebBanHangOnline.Tests\\WebBanHangOnline.Tests.csproj /p:Configuration=Release'
      }
    }
    
    stage('Run Tests') {
      steps {
        echo 'Running unit tests...'
        bat '"C:\\Program Files (x86)\\Microsoft Visual Studio\\2022\\BuildTools\\Common7\\IDE\\CommonExtensions\\Microsoft\\TestWindow\\vstest.console.exe" "WebBanHangOnline.Tests\\bin\\Release\\net9.0\\WebBanHangOnline.Tests.dll" || echo No tests found or execution failed.'
      }
    }

     stage('Publish to Folder') {
      steps {
        echo 'Publishing project to ./publish...'
        bat '"C:\\Program Files (x86)\\Microsoft Visual Studio\\2022\\BuildTools\\MSBuild\\Current\\Bin\\MSBuild.exe" E:\\school\\Do_an_chuyen_nganh\\WebsiteBanhang\\WebBanHangOnline\\WebBanHangOnline.csproj /p:DeployOnBuild=true /p:OutputPath=E:\\school\\Do_an_chuyen_nganh\\WebsiteBanhang\\publish /p:Configuration=Release'
      }
    }

    stage('Debug Directory') {
      steps {
        echo 'Listing current directory...'
        bat 'cd && dir /s /b publish'
      }
    }
    stage('Copy to IIS Folder') {
      steps {
        echo 'Copying to C:\\wwwroot\\myproject...'
        bat 'xcopy "WebBanHangOnline\\publish" "C:\\wwwroot\\myproject" /E /Y /I /R'
      }
    }

    stage('Deploy to IIS') {
      steps {
        echo 'Deploying to IIS...'
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
