---
title: Offrire in modo continuo funzione degli aggiornamenti del codice con DevOps di Azure - funzioni di Azure
description: Informazioni su come impostare una pipeline di DevOps di Azure che fa riferimento a funzioni di Azure.
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.openlocfilehash: 0fdad0caa2deef0d7d55b30a85632f72f4ff0ecc
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594469"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Recapito continuo tramite Azure DevOps

È possibile distribuire automaticamente la funzione a un'app per le funzioni di Azure usando [pipeline di Azure](/azure/devops/pipelines/).

Sono disponibili due opzioni per la definizione della pipeline:

- **YAML file**: Un file YAML descrive la pipeline. Il file potrebbe essere presente una sezione di passaggi di compilazione e una sezione di rilascio. Il file YAML deve essere nello stesso repository dell'app.
- **Modello**: I modelli sono attività pronte all'uso che compila o si distribuisce l'app.

## <a name="yaml-based-pipeline"></a>Della pipeline basati su YAML

Per creare una pipeline in base al YAML, innanzitutto compilare l'app e quindi distribuire l'app.

### <a name="build-your-app"></a>Creare l'app

Come si compila l'app nelle pipeline di Azure dipende dal linguaggio di programmazione dell'app. Ogni linguaggio ha istruzioni di compilazione specifiche che creare un elemento di distribuzione. Un elemento di distribuzione viene usato per distribuire l'app per le funzioni in Azure.

#### <a name="net"></a>.NET

Per creare un file YAML per compilare un'app .NET, è possibile usare l'esempio seguente:

```yaml
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
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="javascript"></a>JavaScript

È possibile usare l'esempio seguente per creare un file YAML per compilare un'app JavaScript:

```yaml
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
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="python"></a>Python

È possibile usare l'esempio seguente per creare un file YAML per compilare un'app Python. Python è supportata solo per le funzioni di Azure di Linux.

```yaml
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
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```
#### <a name="powershell"></a>PowerShell

È possibile usare l'esempio seguente per creare un file YAML per creare un pacchetto un'app di PowerShell. PowerShell è supportata solo per le funzioni di Windows Azure.

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

### <a name="deploy-your-app"></a>Distribuire l'app

È necessario includere uno degli esempi di YAML seguenti nel file YAML, a seconda del sistema operativo host.

#### <a name="windows-function-app"></a>App per le funzioni Windows

È possibile usare il frammento di codice seguente per distribuire un'app per Windows:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>App per le funzioni Linux

È possibile usare il frammento di codice seguente per distribuire un'app per Linux:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>In base al modello di pipeline

I modelli di Azure DevOps sono gruppi predefiniti di attività che compila o si distribuisce un'app.

### <a name="build-your-app"></a>Creare l'app

Come si compila l'app nelle pipeline di Azure dipende dal linguaggio di programmazione dell'app. Ogni linguaggio ha istruzioni di compilazione specifiche che creare un elemento di distribuzione. Un elemento di distribuzione è utilizzato per aggiornare l'app per le funzioni in Azure.

Per usare modelli di compilazione predefiniti, quando si crea una nuova pipeline di compilazione, selezionare **usare l'editor classico** per creare una pipeline usando Progettazione modelli.

![Selezionare l'editor di pipeline di Azure classico](media/functions-how-to-azure-devops/classic-editor.png)

Dopo aver configurato l'origine del codice, modelli di compilazione ricerca per le funzioni di Azure. Selezionare il modello che corrisponde alla lingua l'app.

![Selezionare un modello di compilazione di funzioni di Azure](media/functions-how-to-azure-devops/build-templates.png)

In alcuni casi, gli artefatti di compilazione hanno una struttura di cartelle specifiche. Potrebbe essere necessario selezionare la **nome della cartella radice anteposto ai percorsi di archivio** casella di controllo.

![L'opzione per anteporre il nome della cartella radice](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>App JavaScript

Se l'app JavaScript ha una dipendenza in moduli nativi di Windows, è necessario aggiornare la versione di pool dell'agente per **Hosted VS2017**.

![Aggiornare la versione di pool di agenti](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Distribuire l'app

Quando si crea una nuova pipeline di rilascio, cercare il modello di rilascio di funzioni di Azure.

![Eseguire la ricerca per le funzioni di Azure della versione di modello](media/functions-how-to-azure-devops/release-template.png)

Distribuzione in uno slot di distribuzione non è supportata nel modello di rilascio.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Creare una pipeline di compilazione tramite la CLI di Azure

Per creare una pipeline di compilazione in Azure, usare il `az functionapp devops-pipeline create` [comando](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create). La pipeline di compilazione viene creata per compilare e rilasciare modifiche al codice apportate nel repository. Il comando genera un nuovo file YAML che definisce la pipeline di compilazione e rilascio e quindi si esegue il commit nel repository. I prerequisiti per questo comando dipendono dal percorso del codice.

- Se il codice in GitHub:

    - È necessario disporre **scrivere** autorizzazioni per la sottoscrizione.

    - È necessario essere l'amministratore di progetto DevOps di Azure.

    - È necessario disporre delle autorizzazioni per creare un token di accesso personale GitHub (PAT) che disponga di autorizzazioni sufficienti. Per altre informazioni, vedere [token di accesso personale GitHub requisiti relativi alle autorizzazioni.](https://aka.ms/azure-devops-source-repos)

    - È necessario disporre delle autorizzazioni per eseguire il commit al ramo master nel repository GitHub in modo che è possibile eseguire il commit il file YAML generato automaticamente.

- Se il codice nel repository di Azure:

    - È necessario disporre **scrivere** autorizzazioni per la sottoscrizione.

    - È necessario essere l'amministratore di progetto DevOps di Azure.

## <a name="next-steps"></a>Passaggi successivi

- Rivedere le [Panoramica di funzioni di Azure](functions-overview.md).
- Rivedere le [Panoramica di Azure DevOps](/azure/devops/pipelines/).
