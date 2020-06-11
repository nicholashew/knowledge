# .NET Core Azure CI Pipeline

A simple CI pipeline of a netcore3.1 webapi

Create a sample Web API and a xunit projects using cmd.

- Create the app folder
```
mkdir app && cd app
```

- Create a web api project
```
dotnet new webapi -n Sample -o src\Sample
```

- Create a xunit project
```
dotnet new xunit -n Sample.Tests -o tests\Sample.Tests
```

- Create a .gitignore file
```
dotnet new gitignore
```

- Create a blank azure pipeline file
```
copy nul azure-pipelines.yml
```

Below is the expected project structures

```
app.
|
|   .gitignore
|   azure-pipelines.yml
|
+---src
|   \---Sample
|       |   appsettings.Development.json
|       |   appsettings.json
|       |   Program.cs
|       |   Sample.csproj
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
        |   Sample.Tests.csproj
        |   UnitTest1.cs
```

Update the `azure-pipelines.yml` with the settings below

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

- Create a Azure DevOps project, and push your source code to the Azure Git Repository.

- Navigate to `Pipelines` and then create a new pipeline.

  - Select `Azure Git Repo` (YAML)

  - Select your git repository of the source code

  - Select `Existing Azure Piepelines YAML file`, and then select the `azure-pipelines.yml` from `master` branch

  - Review and Save.
  
 - The pipeline will be automatically run when triggered by the `master` branch, you can configure the trigger conditions to suit your requirements.
     ```
     trigger:
    - master
    ```
  
- You can view the pipeline status by clicking on the Jobs running.
