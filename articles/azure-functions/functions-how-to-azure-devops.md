---
title: Aggiorna continuamente il codice dell'app per le funzioni usando DevOps di AzureContinuo update function app code using Azure DevOps
description: Informazioni su come configurare una pipeline DevOps di Azure destinata a Funzioni di Azure.Learn how to set up an Azure DevOps pipeline that targets Azure Functions.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.openlocfilehash: 5e2fc8fb06248e2cdad9067c56647da6d9626b50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255761"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Recapito continuo tramite DevOps di AzureContinuous delivery by using Azure DevOps

È possibile distribuire automaticamente la funzione in un'app Funzioni di Azure usando Pipeline di Azure.You can automatically deploy your function to an Azure Functions app by using [Azure Pipelines](/azure/devops/pipelines/).

Sono disponibili due opzioni per la definizione della pipeline:You have two options for defining your pipeline:

- **YAML file**: Un file YAML descrive la pipeline. Il file potrebbe avere una sezione relativa alle istruzioni di compilazione e una sezione di rilascio. Il file YAML deve trovarsi nello stesso repository dell'app.
- **Modello:** i modelli sono attività pronte per compilare o distribuire l'app.

## <a name="yaml-based-pipeline"></a>Pipeline basata su YAML

Per creare una pipeline basata su YAML, compilare prima l'app e quindi distribuire l'app.

### <a name="build-your-app"></a>Creare l'app

La modalità di compilazione dell'app in Pipeline di Azure dipende dal linguaggio di programmazione dell'app. Ogni linguaggio include istruzioni di compilazione specifiche che creano un elemento di distribuzione. Un elemento di distribuzione viene usato per distribuire l'app per le funzioni in Azure.A deployment artifact is used to deploy your function app in Azure.

# <a name="c"></a>[C\#](#tab/csharp)

Puoi usare l'esempio seguente per creare un file YAML per compilare un'app .NET:You can use the following sample to create a YAML file to build a .NET app:

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
    modifyOutputPath: false
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
    artifactName: 'drop'
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Puoi usare l'esempio seguente per creare un file YAML per compilare un'app JavaScript:You can use the following sample to create a YAML file to build a JavaScript app:

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
    artifactName: 'drop'
```

# <a name="python"></a>[Python](#tab/python)

È possibile usare uno degli esempi seguenti per creare un file YAML per compilare un'app per una versione specifica di Python.You can use one of the following samples to create a YAML file to build an app for a specific Python version. Python è supportato solo per le app per le funzioni in esecuzione su Linux.

**Versione 3.7**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.7 as required by functions"
  inputs:
    versionSpec: '3.7'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

**Versione 3.6**

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
    pip install --target="./.python_packages/lib/python3.6/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

È possibile usare l'esempio seguente per creare un file YAML per creare un pacchetto di un'app PowerShell.You can use the following sample to create a YAML file to package a PowerShell app. PowerShell is supported only for Windows Azure Functions.

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
    artifactName: 'drop'
```

---

### <a name="deploy-your-app"></a>Distribuire l'app Web

È necessario includere uno dei seguenti esempi YAML nel file YAML, a seconda del sistema operativo host.

#### <a name="windows-function-app"></a>App per le funzioni di Windows

Puoi usare il frammento di codice seguente per distribuire un'app per le funzioni di Windows:You can use the following snippet to deploy a Windows function app:

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

#### <a name="linux-function-app"></a>App per funzioni Linux

È possibile usare il frammento di codice seguente per distribuire un'app per le funzioni Linux:You can use the following snippet to deploy a Linux function app:

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

La modalità di compilazione dell'app in Pipeline di Azure dipende dal linguaggio di programmazione dell'app. Ogni linguaggio include istruzioni di compilazione specifiche che creano un elemento di distribuzione. Un elemento di distribuzione viene usato per aggiornare l'app per le funzioni in Azure.A deployment artifact is used to update your function app in Azure.

Per usare i modelli di compilazione predefiniti, quando si crea una nuova pipeline di compilazione, selezionare **Usa l'editor classico** per creare una pipeline usando i modelli di progettazione.

![Selezionare l'editor classico delle pipeline di AzureSelect the Azure Pipelines classic editor](media/functions-how-to-azure-devops/classic-editor.png)

Dopo aver configurato l'origine del codice, cercare i modelli di compilazione di Funzioni di Azure.After you configure the source of your code, search for Azure Functions build templates. Seleziona il modello che corrisponde alla lingua dell'app.

![Selezionare un modello di compilazione Funzioni di AzureSelect an Azure Functions build template](media/functions-how-to-azure-devops/build-templates.png)

In alcuni casi, gli elementi di compilazione hanno una struttura di cartelle specifica. Potrebbe essere necessario selezionare la casella di controllo **Antecela nome cartella radice per archiviare i percorsi.**

![L'opzione per anteporre il nome della cartella principale](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>App JavaScript

Se l'app JavaScript ha una dipendenza dai moduli nativi di Windows, è necessario aggiornare la versione del pool di agenti a **Hosted VS2017**.

![Aggiornare la versione del pool di agentiUpdate the agent pool version](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Distribuire l'app Web

Quando si crea una nuova pipeline di rilascio, cercare il modello di versione di Funzioni di Azure.When you create a new release pipeline, search for the Azure Functions release template.

![Cercare il modello di rilascio Funzioni di AzureSearch for the Azure Functions release template](media/functions-how-to-azure-devops/release-template.png)

La distribuzione in uno slot di distribuzione non è supportata nel modello di versione.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Creare una pipeline di compilazione usando l'interfaccia della riga di comando di AzureCreate a build pipeline by using the Azure CLI

Per creare una pipeline di `az functionapp devops-pipeline create` compilazione in Azure, usare il [comando](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create). La pipeline di compilazione viene creata per compilare e rilasciare le modifiche al codice apportate nel repository. Il comando genera un nuovo file YAML che definisce la pipeline di compilazione e rilascio e quindi ne esegue il commit nel repository. I prerequisiti per questo comando dipendono dalla posizione del codice.

- Se il codice è in GitHub:If your code is in GitHub:

    - È necessario disporre delle autorizzazioni di **scrittura** per la sottoscrizione.

    - È necessario essere l'amministratore di progetto in Azure DevOps.You must be the project administrator in Azure DevOps.

    - È necessario disporre delle autorizzazioni per creare un token di accesso personale (PAT) GitHub con autorizzazioni sufficienti. Per altre informazioni, vedere [Requisiti di autorizzazione GitHub PAT.](https://aka.ms/azure-devops-source-repos)

    - È necessario disporre delle autorizzazioni per eseguire il commit nel ramo master nel repository GitHub in modo da poter eseguire il commit del file YAML generato automaticamente.

- Se il codice si trova in Azure Repos:If your code is in Azure Repos:

    - È necessario disporre delle autorizzazioni di **scrittura** per la sottoscrizione.

    - È necessario essere l'amministratore di progetto in Azure DevOps.You must be the project administrator in Azure DevOps.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare la [panoramica di Funzioni](functions-overview.md)di Azure .
- Esaminare la [panoramica di Azure DevOps](/azure/devops/pipelines/).
