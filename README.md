trigger:
- none
 
pool:
  vmImage: 'windows-latest'
 
variables:
  buildPlatform: 'Any CPU'
  buildConfiguration: 'Release'
  solution: '**/RoundTheCode.AzureTestProject.sln'
  project: '**/RoundTheCode.AzureTestProject.csproj'
 
steps:
- task: NuGetToolInstaller@1
  name: 'NuGetToolInstaller'
  displayName: 'NuGet tool installer'
 
- task: NuGetCommand@2
  name: 'NuGetRestore'
  displayName: 'NuGet restore packages'
  inputs:
    command: 'restore'
    restoreSolution: '$(solution)'
    feedsToUse: 'select'
 
- task: DotNetCoreCLI@2
  name: 'Tests'
  displayName: 'Run tests'
  inputs:
    command: 'test'
    projects: '$(solution)'
    arguments: '--configuration $(BuildConfiguration)'
 
- task: DotNetCoreCLI@2
  name: 'Publish'
  displayName: 'Publish'
  inputs:
    command: 'publish'
    publishWebProjects: false
    projects: '$(project)'
    arguments: '--configuration $(BuildConfiguration) --output $(Build.ArtifactStagingDirectory)'
    zipAfterPublish: false
 
- task: PublishBuildArtifacts@1
  name: 'PublishBuildArtifacts'
  displayName: 'Publish build artifacts'
  inputs:
    PathtoPublish: '$(Build.ArtifactStagingDirectory)'
    ArtifactName: 'AzureTestProject'
    publishLocation: 'Container
