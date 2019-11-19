---
title: CI/CD con Azure Pipelines e modelli
description: Viene descritto come configurare l'integrazione continua in Azure Pipelines usando i progetti di distribuzione del gruppo di risorse di Azure in Visual Studio per distribuire modelli Gestione risorse.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 51122e314ebd0a97647fc4026b1f49619950c351
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143762"
---
# <a name="integrate-resource-manager-templates-with-azure-pipelines"></a>Integrare modelli di Gestione risorse con Azure Pipelines

Visual Studio fornisce il progetto gruppo di risorse di Azure per la creazione di modelli e la relativa distribuzione nella sottoscrizione di Azure. È possibile integrare questo progetto con Azure Pipelines per l'integrazione continua e la distribuzione continua (CI/CD).

Esistono due modi per distribuire i modelli con Azure Pipelines:

* **Aggiungere un'attività che esegue uno script Azure PowerShell**. Questa opzione offre il vantaggio di garantire la coerenza durante tutto il ciclo di vita dello sviluppo, perché si usa lo stesso script incluso nel progetto di Visual Studio (Deploy-azureresourcegroup. ps1). Lo script crea una fase di creazione degli artefatti dal progetto a un account di archiviazione a cui Gestione risorse può accedere. Gli artefatti sono elementi del progetto, ad esempio modelli collegati, script e file binari dell'applicazione. Quindi, lo script distribuisce il modello.

* **Aggiunta di attività per la copia e la distribuzione di attività**. Questa opzione offre una comoda alternativa allo script del progetto. Si configurano due attività nella pipeline. Un'attività esegue la fase degli artefatti e l'altra distribuisce il modello.

Questo articolo illustra entrambi gli approcci.

## <a name="prepare-your-project"></a>Preparare il progetto

Questo articolo presuppone che il progetto di Visual Studio e l'organizzazione DevOps di Azure siano pronti per la creazione della pipeline. I passaggi seguenti illustrano come assicurarsi di essere pronti:

* Si dispone di un'organizzazione di Azure DevOps. Se non è disponibile, [crearne uno](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)gratuitamente. Se il team ha già un'organizzazione DevOps di Azure, assicurarsi di essere un amministratore del progetto Azure DevOps che si vuole usare.

* È stata configurata una [connessione al servizio](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) per la sottoscrizione di Azure. Le attività nella pipeline vengono eseguite con l'identità dell'entità servizio. Per i passaggi necessari per creare la connessione, vedere [creare un progetto DevOps](resource-manager-tutorial-use-azure-pipelines.md#create-a-devops-project).

* Si dispone di un progetto di Visual Studio creato dal modello di partenza del **gruppo di risorse di Azure** . Per informazioni sulla creazione di questo tipo di progetto, vedere [creazione e distribuzione di gruppi di risorse di Azure tramite Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

* Il progetto di Visual Studio è [connesso a un progetto DevOps di Azure](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops).

## <a name="create-pipeline"></a>Creare una pipeline

1. Se non è stata aggiunta una pipeline in precedenza, è necessario creare una nuova pipeline. Dall'organizzazione DevOps di Azure selezionare **pipeline** e **nuova pipeline**.

   ![Aggiungi nuova pipeline](./media/vs-resource-groups-project-devops-pipelines/new-pipeline.png)

1. Specificare la posizione in cui è archiviato il codice. La figura seguente mostra la selezione di **Azure Repos git**.

   ![Seleziona origine codice](./media/vs-resource-groups-project-devops-pipelines/select-source.png)

1. Da tale origine selezionare il repository che contiene il codice per il progetto.

   ![Seleziona repository](./media/vs-resource-groups-project-devops-pipelines/select-repo.png)

1. Consente di selezionare il tipo di pipeline da creare. È possibile selezionare **pipeline di avvio**.

   ![Seleziona pipeline](./media/vs-resource-groups-project-devops-pipelines/select-pipeline.png)

È ora possibile aggiungere un'attività di Azure PowerShell o il file di copia e distribuire le attività.

## <a name="azure-powershell-task"></a>Attività Azure PowerShell

Questa sezione illustra come configurare la distribuzione continua usando una singola attività che esegue lo script di PowerShell nel progetto. Il file YAML seguente crea un' [attività Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops):

```yaml
pool:
  name: Hosted Windows 2019 with VS2019
  demands: azureps

steps:
- task: AzurePowerShell@3
  inputs:
    azureSubscription: 'demo-deploy-sp'
    ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
    ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus'
    azurePowerShellVersion: LatestVersion
```

Quando si imposta l'attività su `AzurePowerShell@3`, la pipeline usa i comandi del modulo AzureRM per autenticare la connessione. Per impostazione predefinita, lo script di PowerShell nel progetto di Visual Studio usa il modulo AzureRM. Se lo script è stato aggiornato per usare il [modulo AZ](/powershell/azure/new-azureps-module-az), impostare l'attività su `AzurePowerShell@4`.

```yaml
steps:
- task: AzurePowerShell@4
```

Per `azureSubscription`, specificare il nome della connessione al servizio creata.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Per `scriptPath`, specificare il percorso relativo dal file della pipeline allo script. È possibile esaminare il repository per visualizzare il percorso.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Se non è necessario gestire gli artefatti, è sufficiente passare il nome e il percorso di un gruppo di risorse da usare per la distribuzione. Lo script di Visual Studio crea il gruppo di risorse, se non esiste già.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Se è necessario organizzare gli artefatti in un account di archiviazione esistente, usare:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Ora che si è appreso come creare l'attività, procedere con la procedura per modificare la pipeline.

1. Aprire la pipeline e sostituire il contenuto con YAML:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019
     demands: azureps

   steps:
   - task: AzurePowerShell@3
     inputs:
       azureSubscription: 'demo-deploy-sp'
       ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
       ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName 'stage3a4176e058d34bb88cc'
       azurePowerShellVersion: LatestVersion
   ```

1. Selezionare **Salva**.

   ![Salvare la pipeline](./media/vs-resource-groups-project-devops-pipelines/save-pipeline.png)

1. Fornire un messaggio per il commit ed eseguire il commit direttamente nel **database master**.

1. Quando si seleziona **Salva**, la pipeline di compilazione viene eseguita automaticamente. Tornare al riepilogo per la pipeline di compilazione e controllare lo stato.

   ![Visualizzare i risultati](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

È possibile selezionare la pipeline attualmente in esecuzione per visualizzare i dettagli relativi alle attività. Al termine, vengono visualizzati i risultati per ogni passaggio.

## <a name="copy-and-deploy-tasks"></a>Copiare e distribuire attività

In questa sezione viene illustrato come configurare la distribuzione continua utilizzando due attività per organizzare gli elementi e distribuire il modello.

Il seguente YAML mostra l' [attività copia file di Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops):

```yaml
- task: AzureFileCopy@3
  displayName: 'Stage files'
  inputs:
    SourcePath: 'AzureResourceGroup1'
    azureSubscription: 'demo-deploy-sp'
    Destination: 'AzureBlob'
    storage: 'stage3a4176e058d34bb88cc'
    ContainerName: 'democontainer'
    outputStorageUri: 'artifactsLocation'
    outputStorageContainerSasToken: 'artifactsLocationSasToken'
    sasTokenTimeOutInMinutes: '240'
```

Esistono diverse parti di questa attività da rivedere per l'ambiente in uso. Il `SourcePath` indica il percorso degli elementi relativi al file della pipeline. In questo esempio, i file sono presenti in una cartella denominata `AzureResourceGroup1` che era il nome del progetto.

```yaml
SourcePath: '<path-to-artifacts>'
```

Per `azureSubscription`, specificare il nome della connessione al servizio creata.

```yaml
azureSubscription: '<your-connection-name>'
```

Per archiviazione e nome contenitore specificare i nomi dell'account di archiviazione e del contenitore che si vuole usare per archiviare gli artefatti. L'account di archiviazione deve esistere.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

Il YAML seguente mostra l' [attività di distribuzione del modello di Azure Resource Manager](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md):

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    deploymentScope: 'Resource Group'
    ConnectedServiceName: 'demo-deploy-sp'
    subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'Central US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
    deploymentMode: 'Incremental'
```

Esistono diverse parti di questa attività da rivedere per l'ambiente in uso.

- `deploymentScope`: selezionare l'ambito di distribuzione dalle opzioni: `Management Group`, `Subscription` e `Resource Group`. Usare il **gruppo di risorse** in questa procedura dettagliata. Per altre informazioni sugli ambiti, vedere [Ambiti di distribuzione](./resource-group-template-deploy-rest.md#deployment-scope).

- `ConnectedServiceName`: specificare il nome della connessione al servizio creata.

    ```yaml
    ConnectedServiceName: '<your-connection-name>'
    ```

- `subscriptionName`: specificare l'ID sottoscrizione di destinazione. Questa proprietà si applica solo all'ambito di distribuzione del gruppo di risorse e alla distribuzione della sottoscrizione.

- `resourceGroupName` e `location`: specificare il nome e il percorso del gruppo di risorse in cui si vuole eseguire la distribuzione. L'attività crea il gruppo di risorse, se non esiste.

    ```yaml
    resourceGroupName: '<resource-group-name>'
    location: '<location>'
    ```

L'attività di distribuzione è collegata a un modello denominato `WebSite.json` e a un file di parametri denominato WebSite. Parameters. JSON. Usare i nomi dei file di modello e di parametri.

Ora che si è appreso come creare le attività, procedere con la procedura per modificare la pipeline.

1. Aprire la pipeline e sostituire il contenuto con YAML:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019

   steps:
   - task: AzureFileCopy@3
     displayName: 'Stage files'
     inputs:
       SourcePath: 'AzureResourceGroup1'
       azureSubscription: 'demo-deploy-sp'
       Destination: 'AzureBlob'
       storage: 'stage3a4176e058d34bb88cc'
       ContainerName: 'democontainer'
       outputStorageUri: 'artifactsLocation'
       outputStorageContainerSasToken: 'artifactsLocationSasToken'
       sasTokenTimeOutInMinutes: '240'
    - task: AzureResourceGroupDeployment@2
      displayName: 'Deploy template'
      inputs:
        deploymentScope: 'Resource Group'
        ConnectedServiceName: 'demo-deploy-sp'
        subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
        action: 'Create Or Update Resource Group'
        resourceGroupName: 'demogroup'
        location: 'Central US'
        templateLocation: 'URL of the file'
        csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
        csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
        overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
        deploymentMode: 'Incremental'
   ```

1. Selezionare **Salva**.

1. Fornire un messaggio per il commit ed eseguire il commit direttamente nel **database master**.

1. Quando si seleziona **Salva**, la pipeline di compilazione viene eseguita automaticamente. Tornare al riepilogo per la pipeline di compilazione e controllare lo stato.

   ![Visualizzare i risultati](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

È possibile selezionare la pipeline attualmente in esecuzione per visualizzare i dettagli relativi alle attività. Al termine, vengono visualizzati i risultati per ogni passaggio.

## <a name="next-steps"></a>Passaggi successivi

Per istruzioni dettagliate sull'uso di Azure Pipelines con i modelli di Gestione risorse, vedere [esercitazione: integrazione continua dei modelli di Azure Resource Manager con Azure Pipelines](resource-manager-tutorial-use-azure-pipelines.md).
