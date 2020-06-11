# .NET Core Azure CI Pipeline

A simple CI pipeline of a netcore3.1 webapi

Sample Web API project structure

```
app.
|
|   .gitignore
|   azure-pipelines.yml
|   sample.code-workspace
|
+---src
|   \---Sample
|       |   appsettings.Development.json
|       |   appsettings.json
|       |   netcore.csproj
|       |   Program.cs
|       |   Startup.cs
|       |   WeatherForecast.cs
|       |
|       +---Controllers
|       |       WeatherForecastController.cs
|       |
|       \---Properties
|               launchSettings.json
|
\---tests
    \---Sample.Tests
            Sample.Tests.csproj
            UnitTest1.cs
```

Sample YAML 

```yaml
# Starter pipeline
# Start with a minimal pipeline that you can customize to build and deploy your code.
# Add steps that build, run tests, deploy, and more:
# https://aka.ms/yaml

trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

# the global variables
variables:
  buildConfiguration: 'Release'

# the pipeline steps
steps:

# run restore task
- task: DotNetCoreCLI@2
  displayName: dotnet restore
  inputs:
    command: 'restore'
    projects: '**/*.csproj'
    feedsToUse: 'select'

# run build task
- task: DotNetCoreCLI@2
  displayName: dotnet build
  inputs:
    command: 'build'
    projects: '**/*.csproj'
    arguments: '--configuration $(buildConfiguration)'

# run unit testing task
- task: DotNetCoreCLI@2
  displayName: dotnet test
  inputs:
    command: 'test'
    projects: '**/*Tests/*.csproj'
    arguments: '--configuration $(buildConfiguration)'

# run publish task, binary files will be publish to the ArtifactStagingDirectory and zipped
- task: DotNetCoreCLI@2
  displayName: dotnet publish
  inputs:
    command: 'publish'
    publishWebProjects: false
    projects: '**/*.csproj'
    arguments: '--configuration $(buildConfiguration) --output $(Build.ArtifactStagingDirectory)'
    zipAfterPublish: true

# raise Publish Artifact event from the ArtifactStagingDirectory, 
# we will need to configure a Azure Release Pipeline to pick up the files and perform actions
- task: PublishBuildArtifacts@1
  displayName: 'Publish Artifact'
  inputs:
    PathtoPublish: '$(Build.ArtifactStagingDirectory)'
    ArtifactName: 'drop'
    publishLocation: 'Container'

```
