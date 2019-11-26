---
title: Recapitare costantemente gli aggiornamenti del codice di funzione tramite Azure DevOps-funzioni di Azure
description: Informazioni su come configurare una pipeline di Azure DevOps destinata a funzioni di Azure.
author: ahmedelnably
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.openlocfilehash: e2dbcadab662caf641716272db1f860c3a6bafa5
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230545"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Recapito continuo tramite Azure DevOps

È possibile distribuire automaticamente la funzione in un'app funzioni di Azure usando [Azure Pipelines](/azure/devops/pipelines/).

Per la definizione della pipeline sono disponibili due opzioni:

- **File YAML**: un file YAML descrive la pipeline. Il file potrebbe includere una sezione relativa ai passaggi di compilazione e una sezione di rilascio. Il file YAML deve trovarsi nello stesso repository dell'app.
- **Modello**: i modelli sono attività già create che compilano o distribuiscono l'app.

## <a name="yaml-based-pipeline"></a>Pipeline basata su YAML

Per creare una pipeline basata su YAML, compilare prima l'app e quindi distribuire l'app.

### <a name="build-your-app"></a>Creare l'app

La modalità di compilazione dell'app in Azure Pipelines dipende dal linguaggio di programmazione dell'app. Ogni linguaggio dispone di passaggi di compilazione specifici che creano un artefatto di distribuzione. Viene usato un artefatto di distribuzione per distribuire l'app per le funzioni in Azure.

#### <a name="net"></a>.NET

È possibile usare l'esempio seguente per creare un file YAML per compilare un'app .NET:

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

È possibile usare l'esempio seguente per creare un file YAML per compilare un'app Python. Python è supportato solo per le funzioni di Azure per Linux. YAML per Python 3,7 può essere compilato sostituendo tutte le istanze di 3,6 con 3,7 in questo YAML.

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

È possibile usare l'esempio seguente per creare un file YAML per creare il pacchetto di un'app PowerShell. PowerShell è supportato solo per le funzioni di Windows Azure.

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

### <a name="deploy-your-app"></a>Distribuire l'app Web

È necessario includere uno degli esempi YAML seguenti nel file YAML, a seconda del sistema operativo host.

#### <a name="windows-function-app"></a>App per le funzioni di Windows

Per distribuire un'app per le funzioni di Windows, è possibile usare il frammento di codice seguente:

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

Per distribuire un'app per le funzioni Linux è possibile usare il frammento di codice seguente:

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

## <a name="template-based-pipeline"></a>Pipeline basata su modelli

I modelli in Azure DevOps sono gruppi predefiniti di attività che compilano o distribuiscono un'app.

### <a name="build-your-app"></a>Creare l'app

La modalità di compilazione dell'app in Azure Pipelines dipende dal linguaggio di programmazione dell'app. Ogni linguaggio dispone di passaggi di compilazione specifici che creano un artefatto di distribuzione. Viene usato un artefatto di distribuzione per aggiornare l'app per le funzioni in Azure.

Per utilizzare i modelli di compilazione predefiniti, quando si crea una nuova pipeline di compilazione, selezionare **utilizzare l'editor classico** per creare una pipeline utilizzando modelli di progettazione.

![Selezionare l'editor classico Azure Pipelines](media/functions-how-to-azure-devops/classic-editor.png)

Dopo aver configurato l'origine del codice, cercare i modelli di compilazione di funzioni di Azure. Selezionare il modello che corrisponde alla lingua dell'app.

![Selezionare un modello di compilazione di funzioni di Azure](media/functions-how-to-azure-devops/build-templates.png)

In alcuni casi, gli artefatti di compilazione hanno una struttura di cartelle specifica. Potrebbe essere necessario selezionare la casella **di controllo anteporre il nome della cartella radice ai percorsi di archiviazione** .

![Opzione per anteporre il nome della cartella radice](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>App JavaScript

Se l'app JavaScript dipende da moduli nativi di Windows, è necessario aggiornare la versione del pool di agenti a **Hosted VS2017**.

![Aggiornare la versione del pool di agenti](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Distribuire l'app Web

Quando si crea una nuova pipeline di versione, cercare il modello di rilascio di funzioni di Azure.

![Cercare il modello di rilascio di funzioni di Azure](media/functions-how-to-azure-devops/release-template.png)

La distribuzione in uno slot di distribuzione non è supportata nel modello di versione.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Creare una pipeline di compilazione usando l'interfaccia della riga di comando di Azure

Per creare una pipeline di compilazione in Azure, usare il [comando](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create)`az functionapp devops-pipeline create`. La pipeline di compilazione viene creata per compilare e rilasciare tutte le modifiche al codice apportate nel repository. Il comando genera un nuovo file YAML che definisce la pipeline di compilazione e rilascio e quindi ne viene eseguito il commit nel repository. I prerequisiti per questo comando dipendono dalla posizione del codice.

- Se il codice è in GitHub:

    - È necessario disporre delle autorizzazioni di **scrittura** per la sottoscrizione.

    - È necessario essere amministratore del progetto in Azure DevOps.

    - È necessario disporre delle autorizzazioni per creare un token di accesso personale GitHub che disponga di autorizzazioni sufficienti. Per altre informazioni, vedere i [requisiti di autorizzazione per GITHUB Pat.](https://aka.ms/azure-devops-source-repos)

    - È necessario disporre delle autorizzazioni per eseguire il commit nel ramo master nel repository GitHub, in modo da poter eseguire il commit del file YAML generato automaticamente.

- Se il codice è Azure Repos:

    - È necessario disporre delle autorizzazioni di **scrittura** per la sottoscrizione.

    - È necessario essere amministratore del progetto in Azure DevOps.

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di funzioni di Azure](functions-overview.md).
- Vedere [Panoramica di Azure DevOps](/azure/devops/pipelines/).
