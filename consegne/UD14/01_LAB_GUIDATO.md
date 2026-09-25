# UD14 — Consegna LAB guidato

- ACR: acrud131546xe5qaf4ai56
- admin user: False
- test locali:
```
Ran 3 tests
OK
```
- Docker build locale: PASS
```json
{
    "status": "ok",
    "service": "catalog-backend",
    "version": "ci-v1"
}
```
- sc-acr-ud14: Creata
- WIF: sì
- YAML:
trigger: 
```yaml
trigger:
- main
```
Pool: 
```yaml
pool:
  vmImage: ubuntu-latest
```
stage Test:
```yaml
- stage: Test
  displayName: Test application
  jobs:
  - job: PythonTests
    displayName: Python tests on Microsoft-hosted agent
    steps:

    - checkout: self
      clean: true

    - bash: |
        echo "Agent.Name=$(Agent.Name)"
        echo "Agent.OS=$(Agent.OS)"
        echo "Agent.MachineName=$(Agent.MachineName)"
        echo "Build.SourcesDirectory=$(Build.SourcesDirectory)"
        echo
        python3 --version
      displayName: Inspect hosted test environment

    - bash: |
        python3 -m unittest discover \
          -s app/catalog-backend-ci/tests \
          -v
      displayName: Run Python tests

```
stage BuildPush:
```yaml
- stage: BuildPush
  displayName: Build and push image
  dependsOn: Test
  condition: succeeded()
  jobs:
  - job: DockerBuild
    displayName: Docker build on Microsoft-hosted agent
    steps:

    - checkout: self
      clean: true

    - bash: |
        echo "Agent.Name=$(Agent.Name)"
        echo "Agent.OS=$(Agent.OS)"
        echo "Agent.MachineName=$(Agent.MachineName)"
        echo "Build.SourcesDirectory=$(Build.SourcesDirectory)"
        echo
        docker --version
      displayName: Inspect hosted Docker environment

    - task: Docker@2
      displayName: Build and push to ACR
      inputs:
        command: buildAndPush
        containerRegistry: '$(dockerRegistryServiceConnection)'
        repository: '$(imageRepository)'
        Dockerfile: '$(dockerfilePath)'
        buildContext: '$(buildContext)'
        tags: |
          $(imageTag)
```
- Stage Test:
```
Agent.Name=Azure Pipelines 1
Agent.OS=Linux
Agent.MachineName=runnervm1fde3
Python 3.12.3
```
- Stage BuildPush:
```
Agent.Name=Azure Pipelines 1
Agent.OS=Linux
Agent.MachineName=runnervm1fde3
Docker version 28.0.4, build b8034c0
```
- run CI:
UD14_AGENT_MODE=MICROSOFT_HOSTED
Lo step che ha eseguito i test è `Run Python tests`.
Lo step che ha costruito l'immagine è `Build and push to ACR`/`Docker@2`.
- Build ID: bb2ec949704eebdd8043d13da235e185d9bd9d764ff7693d89ec1e4a87273881
- tag ACR: 8
