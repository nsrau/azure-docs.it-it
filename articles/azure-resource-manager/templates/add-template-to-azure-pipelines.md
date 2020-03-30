---
title: CI/CD con pipeline e modelli di Azure
description: Viene descritto come configurare l'integrazione continua nelle pipeline di Azure usando i progetti di distribuzione del gruppo di risorse di Azure in Visual Studio per distribuire i modelli di Resource Manager.Describes how to set up continuous integration in Azure Pipelines by using Azure Resource Group deployment projects in Visual Studio to deploy Resource Manager templates.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 7617bf47595fce7baa533b0f7cc94a1803ddd349
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153455"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>Integrare i modelli ARM con le pipeline di AzureIntegrate ARM templates with Azure Pipelines

Visual Studio fornisce il progetto Gruppo di risorse di Azure per la creazione di modelli di Azure Resource Manager (ARM) e la distribuzione nella sottoscrizione di Azure.Visual Studio provides the Azure Resource Group project for creating Azure Resource Manager (ARM) templates and deploying them to your Azure subscription. È possibile integrare questo progetto con le pipeline di Azure per l'integrazione continua e la distribuzione continua (CI/CD).

Esistono due modi per distribuire i modelli con le pipeline di Azure:There are two ways to deploy templates with Azure Pipelines:

* **Aggiungere un'attività che esegue uno script di Azure PowerShell.** Questa opzione ha il vantaggio di fornire coerenza durante tutto il ciclo di vita dello sviluppo perché si usa lo stesso script incluso nel progetto di Visual Studio (Deploy-AzureResourceGroup.ps1). Lo script analizza gli elementi dal progetto a un account di archiviazione a cui è possibile accedere Resource Manager.The script stages artifacts from your project to a storage account that Resource Manager can access. Gli elementi del progetto sono elementi del progetto, ad esempio modelli collegati, script e file binari dell'applicazione. Quindi, lo script distribuisce il modello.

* **Aggiungere attività per copiare e distribuire le attività.** Questa opzione offre una comoda alternativa allo script di progetto. Configurare due attività nella pipeline. Un'attività mette in stagese gli elementi e l'altra attività distribuisce il modello.

Questo articolo illustra entrambi gli approcci.

## <a name="prepare-your-project"></a>Preparare il progetto

Questo articolo presuppone che il progetto di Visual Studio e l'organizzazione DevOps di Azure siano pronti per la creazione della pipeline. La procedura seguente illustra come verificare di essere pronta:

* Si dispone di un'organizzazione DevOps di Azure.You have an Azure DevOps organization. Se non ne hai uno, [creane uno gratuitamente.](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops) Se il team dispone già di un'organizzazione DevOps di Azure, assicurarsi di essere un amministratore del progetto DevOps di Azure che si vuole usare.

* È stata configurata una connessione al servizio per la sottoscrizione di Azure.You've configured a [service connection](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) to your Azure subscription. Le attività nella pipeline vengono eseguite con l'identità dell'entità servizio. Per la procedura di creazione della connessione, vedere [Creare un progetto DevOps](template-tutorial-use-azure-pipelines.md#create-a-devops-project).

* Si dispone di un progetto di Visual Studio che è stato creato dal modello di avvio del gruppo di risorse di Azure.You have a Visual Studio project that was created from the **Azure Resource Group** starter template. Per informazioni sulla creazione di tale tipo di progetto, vedere Creazione e distribuzione di gruppi di [risorse di Azure tramite Visual Studio](create-visual-studio-deployment-project.md).

* Il progetto di Visual Studio è [connesso a un progetto DevOps di Azure.](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops)

## <a name="create-pipeline"></a>Creare una pipeline

1. Se non è stata aggiunta una pipeline in precedenza, è necessario creare una nuova pipeline. Dall'organizzazione DevOps di Azure selezionare **Pipeline** e **nuova pipeline.**

   ![Aggiungi nuova pipeline](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Specificare la posizione in cui è memorizzato il codice. L'immagine seguente illustra la selezione di **Azure Repos Git**.

   ![Selezionare l'origine codiceSelect code source](./media/add-template-to-azure-pipelines/select-source.png)

1. Da tale origine, selezionare il repository con il codice per il progetto.

   ![Seleziona repository](./media/add-template-to-azure-pipelines/select-repo.png)

1. Selezionare il tipo di pipeline da creare. È possibile selezionare **Starter pipeline**.

   ![Seleziona pipeline](./media/add-template-to-azure-pipelines/select-pipeline.png)

È possibile aggiungere un'attività di Azure PowerShell o il file di copia e distribuire le attività.

## <a name="azure-powershell-task"></a>Attività di Azure PowerShellAzure PowerShell task

In questa sezione viene illustrato come configurare la distribuzione continua usando una singola attività che esegue lo script di PowerShell nel progetto. Il file YAML seguente crea un'attività di Azure PowerShell:The following YAML file creates an [Azure PowerShell task:](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops)

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

Quando si imposta `AzurePowerShell@3`l'attività su , la pipeline usa i comandi del modulo AzureRM per autenticare la connessione. Per impostazione predefinita, lo script di PowerShell nel progetto di Visual Studio usa il modulo AzureRM.By default, the PowerShell script in the Visual Studio project uses the AzureRM module. Se lo script è stato aggiornato per utilizzare il `AzurePowerShell@4`modulo [Az](/powershell/azure/new-azureps-module-az), impostare l'attività su .

```yaml
steps:
- task: AzurePowerShell@4
```

Per `azureSubscription`, specificare il nome della connessione al servizio creata.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Per `scriptPath`, specificare il percorso relativo dal file della pipeline allo script. È possibile cercare nel repository per visualizzare il percorso.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Se non è necessario gestire temporaneamente gli elementi, è sufficiente passare il nome e il percorso di un gruppo di risorse da usare per la distribuzione. Lo script di Visual Studio crea il gruppo di risorse se non esiste già.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Se è necessario eseguire la distribuzione degli elementi in un account di archiviazione esistente:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

A questo punto, per informazioni su come creare l'attività, è possibile eseguire i passaggi per modificare la pipeline.

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

   ![Salvare la pipeline](./media/add-template-to-azure-pipelines/save-pipeline.png)

1. Fornire un messaggio per il commit ed eseguire direttamente il commit a **master**.

1. Quando si seleziona **Salva**, la pipeline di compilazione viene eseguita automaticamente. Tornare al riepilogo per la pipeline di compilazione e osservare lo stato.

   ![Visualizzazione dei risultati](./media/add-template-to-azure-pipelines/view-results.png)

È possibile selezionare la pipeline attualmente in esecuzione per visualizzare i dettagli sulle attività. Al termine, vengono visualizzati i risultati per ogni passaggio.

## <a name="copy-and-deploy-tasks"></a>Copiare e distribuire attività

In questa sezione viene illustrato come configurare la distribuzione continua usando due attività per gestire temporaneamente gli elementi e distribuire il modello.

Il codice YAML seguente mostra l'attività di copia dei file di Azure:The following YAML shows the [Azure file copy task:](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)

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

Esistono diverse parti di questa attività da rivedere per l'ambiente. Indica `SourcePath` la posizione degli elementi rispetto al file della pipeline. In questo esempio, i file `AzureResourceGroup1` sono presenti in una cartella denominata che era il nome del progetto.

```yaml
SourcePath: '<path-to-artifacts>'
```

Per `azureSubscription`, specificare il nome della connessione al servizio creata.

```yaml
azureSubscription: '<your-connection-name>'
```

Per il nome dell'archiviazione e del contenitore, specificare i nomi dell'account di archiviazione e del contenitore che si vuole usare per l'archiviazione degli elementi. L'account di archiviazione deve esistere.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

Il yAML seguente mostra l'attività di distribuzione del modello di Azure Resource Manager:The following YAML shows the [Azure Resource Manager template deployment task:](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)

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

Esistono diverse parti di questa attività da rivedere per l'ambiente.

- `deploymentScope`: selezionare l'ambito di `Management Group` `Subscription` distribuzione `Resource Group`tra le opzioni: , e . Usare **Il gruppo di risorse** in questa procedura a piedi. Per altre informazioni sugli ambiti, vedere [Ambiti di distribuzione](deploy-rest.md#deployment-scope).

- `ConnectedServiceName`: specificare il nome della connessione al servizio creata.

    ```yaml
    ConnectedServiceName: '<your-connection-name>'
    ```

- `subscriptionName`: specificare l'ID sottoscrizione di destinazione. Questa proprietà si applica solo all'ambito di distribuzione del gruppo di risorse e all'ambito di distribuzione della sottoscrizione.

- `resourceGroupName`e `location`: specificare il nome e il percorso del gruppo di risorse in cui si desidera eseguire la distribuzione. L'attività crea il gruppo di risorse se non esiste.

    ```yaml
    resourceGroupName: '<resource-group-name>'
    location: '<location>'
    ```

L'attività di distribuzione `WebSite.json` è collegata a un modello denominato e a un file di parametri denominato WebSite.parameters.json. Utilizzare i nomi dei file di modelli e parametri.

A questo punto, per informazioni su come creare le attività, è possibile eseguire i passaggi per modificare la pipeline.

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

1. Fornire un messaggio per il commit ed eseguire direttamente il commit a **master**.

1. Quando si seleziona **Salva**, la pipeline di compilazione viene eseguita automaticamente. Tornare al riepilogo per la pipeline di compilazione e osservare lo stato.

   ![Visualizzazione dei risultati](./media/add-template-to-azure-pipelines/view-results.png)

È possibile selezionare la pipeline attualmente in esecuzione per visualizzare i dettagli sulle attività. Al termine, vengono visualizzati i risultati per ogni passaggio.

## <a name="next-steps"></a>Passaggi successivi

Per il processo passo-passo sull'uso di Pipeline di Azure con i modelli ARM, vedere [Esercitazione: Integrazione continua di modelli ARM con le pipeline di Azure.For](template-tutorial-use-azure-pipelines.md)step-by-step process on using Azure Pipelines with ARM templates, see Tutorial: Continuous integration of ARM templates with Azure Pipelines.
