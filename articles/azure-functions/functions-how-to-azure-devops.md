---
title: Fine di realizzare costantemente funzione degli aggiornamenti del codice usando Azure DevOps
description: Informazioni su come impostare una pipeline di DevOps di Azure come destinazione di funzioni di Azure.
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.custom: ''
ms.openlocfilehash: 86f1c36bd5f972a6ebd573019a22b0c0d07dc480
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928093"
---
# <a name="continuous-delivery-using-azure-devops"></a>Distribuzione continua tramite Azure DevOps

È possibile distribuire automaticamente la funzione in un'app di funzione di Azure usando [pipeline di Azure](/azure/devops/pipelines/).
Per definire la pipeline, è possibile usare:

- File daemonset YAML: Questo file illustra la pipeline, può presentare una sezione di passaggi di compilazione e una sezione di rilascio. Il file YAML deve essere nello stesso repository dell'app.

- modelli: I modelli sono pronti effettuate le attività che compila o si distribuisce l'app.

## <a name="yaml-based-pipeline"></a>Della pipeline basati su YAML

### <a name="build-your-app"></a>Crea la tua app

Creazione dell'app nelle pipeline di Azure dipende dal linguaggio di programmazione dell'app.
Ogni linguaggio include i passaggi di compilazione specifica per creare un elemento di distribuzione, che può essere usato per distribuire l'app per le funzioni in Azure.

#### <a name="net"></a>.NET

È possibile utilizzare l'esempio seguente per creare un file YAML per compilare l'app .NET.

```yaml
jobs:
  - job: Build
    pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: true
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output/s"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="javascript"></a>JavaScript

È possibile usare l'esempio seguente per creare un file YAML per compilare l'app JavaScript:

```yaml
jobs:
  - job: Build
    pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output/s"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="python"></a>Python

È possibile usare l'esempio seguente per creare un file YAML per compilare l'app di Python, Python è supportata solo per le funzioni di Azure di Linux:

```yaml
jobs:
  - job: Build
    pool:
      vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    python3.6 -m venv worker_venv
    source worker_venv/bin/activate
    pip3.6 install setuptools
    pip3.6 install -r requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output/s"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/$(Build.BuildId).zip'
    name: 'drop'
```

### <a name="deploy-your-app"></a>Distribuire l'app

A seconda del sistema operativo host, è necessario includere l'esempio YAML seguente nel file YAML.

#### <a name="windows-function-app"></a>App per le funzioni Windows

Il seguente frammento può essere usato per distribuire un'app per Windows

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
```

#### <a name="linux-function-app"></a>App per le funzioni Linux

Il seguente frammento può essere usato per distribuire un'app per le funzioni Linux

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
```

## <a name="template-based-pipeline"></a>In base al modello di pipeline

I modelli di Azure DevOps, sono gruppo predefinito di attività che compila o si distribuisce un'app.

### <a name="build-your-app"></a>Crea la tua app

Creazione dell'app nelle pipeline di Azure dipende dal linguaggio di programmazione dell'app. Ogni linguaggio include i passaggi di compilazione specifica per creare un elemento di distribuzione, che può essere utilizzato per aggiornare l'app per le funzioni in Azure.
Per usare i modelli di compilazione predefiniti, quando si crea una nuova pipeline di compilazione, scegliere **usare l'editor classico** per creare una pipeline usando i modelli di progettazione

![Editor classico di pipeline di Azure](media/functions-how-to-azure-devops/classic-editor.png)

Dopo aver configurato l'origine del codice, cercare funzioni di Azure di modelli di compilazione e scegliere il modello che corrisponde alla lingua l'app.

![Modelli di compilazione di funzioni di Azure](media/functions-how-to-azure-devops/build-templates.png)

#### <a name="javascript-apps"></a>App JavaScript

Se l'app JavaScript presenta una dipendenza da moduli nativi di Windows, è necessario aggiornare:

- La versione del Pool di agenti da **Hosted VS2017**

  ![Cambiare l'agente di compilazione del sistema operativo](media/functions-how-to-azure-devops/change-agent.png)

- Lo script nel **compilare estensioni** passaggio nel modello per `IF EXIST *.csproj dotnet build extensions.csproj --output ./bin`

  ![Script delle modifiche](media/functions-how-to-azure-devops/change-script.png)

### <a name="deploy-your-app"></a>Distribuire l'app

Quando si crea una nuova pipeline di rilascio, cercare il modello di rilascio di funzioni di Azure.

![](media/functions-how-to-azure-devops/release-template.png)

## <a name="creating-an-azure-pipeline-using-the-azure-cli"></a>Creazione di una Pipeline di Azure tramite la CLI di Azure

Usando il `az functionapp devops-pipeline create` [comando](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create), verrà creata una pipeline di Azure per compilare e rilasciare modifiche al codice nel repository. Il comando genererà un nuovo file YAML che definisce la pipeline di compilazione e rilascio ed eseguirne il commit nel repository.
I prerequisiti per questo comando dipendono dal percorso del codice:

- Se il codice in GitHub:

    - È necessario disporre **scrivere** dell'autorizzazione per la sottoscrizione.

    - Sei l'amministratore di progetto DevOps di Azure.

    - Si è autorizzati a creare un Personal Access Token GitHub con autorizzazioni sufficienti. [Requisiti di autorizzazione del token di accesso personale GitHub.](https://aka.ms/azure-devops-source-repos)

    - Si è autorizzati a eseguire il commit al ramo master nel repository GitHub per salvare il file YAML generato automaticamente.

- Se il codice nel repository di Azure:

    - È necessario disporre **scrivere** dell'autorizzazione per la sottoscrizione.

    - Sei l'amministratore di progetto DevOps di Azure.

## <a name="next-steps"></a>Passaggi successivi

+ [Panoramica di Funzioni di Azure](functions-overview.md)
+ [Panoramica di DevOps di Azure](/azure/devops/pipelines/)
