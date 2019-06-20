---
title: CI/CD con i modelli di Resource Manager e le pipeline di Azure
description: Viene descritto come configurare l'integrazione continua nelle pipeline di Azure con i progetti di distribuzione gruppo di risorse di Azure in Visual Studio per distribuire i modelli di Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: article
ms.date: 06/12/2019
ms.author: tomfitz
ms.openlocfilehash: b70b38904c0373c53c3731dd0442511116d9c4de
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191460"
---
# <a name="integrate-resource-manager-templates-with-azure-pipelines"></a>Integrare modelli di Resource Manager con le pipeline di Azure

Visual Studio fornisce il progetto gruppo di risorse di Azure per la creazione di modelli e distribuirle alla sottoscrizione di Azure. È possibile integrare questo progetto con le pipeline di Azure per l'integrazione continua e distribuzione continua (CI/CD).

Esistono due modi per distribuire modelli con le pipeline di Azure:

* **Aggiungere attività che esegue uno script Azure PowerShell**. Questa opzione ha il vantaggio di fornire coerenza durante tutto il ciclo di vita di sviluppo, perché si usa lo stesso script incluso nel progetto di Visual Studio (Deploy-AzureResourceGroup.ps1). Gli script fasi elementi dal progetto in un account di archiviazione che possono accedere a Resource Manager. Gli elementi sono elementi del progetto, ad esempio i modelli collegati, script e file binari dell'applicazione. Quindi, lo script distribuisce il modello.

* **Aggiunta di attività per copiare e distribuire le attività**. Questa opzione offre una comoda alternativa allo script del progetto. Configurare due attività nella pipeline. Gli elementi delle fasi di un'attività e l'altra attività distribuisce il modello.

Questo articolo illustra entrambi gli approcci.

## <a name="prepare-your-project"></a>Preparare il progetto

Questo articolo presuppone che il progetto di Visual Studio e l'organizzazione di Azure DevOps sono pronti per la creazione di pipeline. La procedura seguente illustra come assicurarsi che si è pronti:

* Si dispone di un'organizzazione di Azure DevOps. Se non disponibile, [crearne uno gratuitamente](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). Se il team ha già un'organizzazione di DevOps di Azure, assicurarsi di che essere un amministratore del progetto DevOps di Azure che si desidera utilizzare.

* È stato configurato un [connessione al servizio](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) alla sottoscrizione di Azure. Le attività nella pipeline di esecuzione con l'identità dell'entità servizio. Per i passaggi creare la connessione, vedere [creare un progetto DevOps](resource-manager-tutorial-use-azure-pipelines.md#create-a-devops-project).

* Si dispone di un progetto di Visual Studio che è stato creato dal **gruppo di risorse di Azure** modello iniziale. Per informazioni sulla creazione di tale tipo di progetto, vedere [creazione e distribuzione di gruppi di risorse di Azure tramite Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

* Progetto di Visual Studio viene [connesso a un progetto Azure DevOps](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops).

## <a name="create-pipeline"></a>Creare una pipeline

1. Se è stata aggiunta una pipeline in precedenza, è necessario creare una nuova pipeline. L'organizzazione di DevOps di Azure, selezionare **pipeline** e **nuova pipeline**.

   ![Aggiungi nuova pipeline](./media/vs-resource-groups-project-devops-pipelines/new-pipeline.png)

1. Specificare dove verrà archiviato il codice. L'immagine seguente Mostra selezione **Git repository Azure**.

   ![Selezionare l'origine del codice](./media/vs-resource-groups-project-devops-pipelines/select-source.png)

1. Da tale origine, selezionare il repository con il codice per il progetto.

   ![Seleziona repository](./media/vs-resource-groups-project-devops-pipelines/select-repo.png)

1. Selezionare il tipo di pipeline da creare. È possibile selezionare **pipeline Starter**.

   ![Selezionare pipeline](./media/vs-resource-groups-project-devops-pipelines/select-pipeline.png)

È possibile aggiungere un'attività di Azure PowerShell o il copia file e sulle attività di distribuzione.

## <a name="azure-powershell-task"></a>Attività di Azure PowerShell

In questa sezione viene illustrato come configurare la distribuzione continua tramite una singola attività che esegue lo script di PowerShell nel progetto. Il file YAML seguente crea un [attività di Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops):

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

Quando si imposta l'attività su `AzurePowerShell@3`, la pipeline Usa i comandi del modulo AzureRM per autenticare la connessione. Per impostazione predefinita, lo script di PowerShell nel progetto di Visual Studio Usa il modulo AzureRM. Se è stato aggiornato lo script da usare la [modulo di Az](/powershell/azure/new-azureps-module-az), impostare l'attività su `AzurePowerShell@4`.

```yaml
steps:
- task: AzurePowerShell@4
```

Per `azureSubscription`, specificare il nome della connessione del servizio è stato creato.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Per `scriptPath`, fornire il percorso relativo del file di pipeline per lo script. È possibile esaminare il repository per visualizzare il percorso.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Se non è necessario preparazione degli elementi, è sufficiente passare il nome e il percorso di un gruppo di risorse da usare per la distribuzione. Lo script di Visual Studio crea il gruppo di risorse se non esiste già.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Se è necessario preparazione degli elementi a un account di archiviazione esistente, usare:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Ora, che si sappiano creare l'attività, è opportuno seguire i passaggi per modificare la pipeline.

1. Aprire la pipeline e sostituire il contenuto con il YAML:

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

1. Fornire un messaggio per il commit ed eseguire il commit direttamente al **master**.

1. Quando si seleziona **salvare**, la pipeline di compilazione viene eseguita automaticamente. Tornare al riepilogo per la pipeline di compilazione e controllare lo stato.

   ![Visualizzare i risultati](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

È possibile selezionare la pipeline per visualizzare i dettagli sulle attività attualmente in esecuzione. Al termine, noterete che i risultati per ogni passaggio.

## <a name="copy-and-deploy-tasks"></a>Copiare e distribuire le attività

In questa sezione viene illustrato come configurare la distribuzione continua tramite una due attività per gli artefatti di staging e la distribuzione del modello. 

I seguente YAML il [attività di copia dei file di Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops):

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

Esistono diverse parti di questa attività di revisione per l'ambiente. Il `SourcePath` indica la posizione degli elementi relativi al file pipeline. In questo esempio sono presenti i file in una cartella denominata `AzureResourceGroup1` che era il nome del progetto.

```yaml
SourcePath: '<path-to-artifacts>'
```

Per `azureSubscription`, specificare il nome della connessione del servizio è stato creato.

```yaml
azureSubscription: '<your-connection-name>'
```

Per il nome di archiviazione e contenitore, specificare i nomi di account di archiviazione e contenitore da usare per archiviare gli elementi. L'account di archiviazione deve esistere.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

I seguente YAML il [attività distribuzione gruppo di risorse di Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops):

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    azureSubscription: 'demo-deploy-sp'
    resourceGroupName: 'demogroup'
    location: 'centralus'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
```

Esistono diverse parti di questa attività di revisione per l'ambiente. Per `azureSubscription`, specificare il nome della connessione del servizio è stato creato.

```yaml
azureSubscription: '<your-connection-name>'
```

Per la `resourceGroupName` e `location`, fornire il nome e percorso del gruppo di risorse da distribuire per. L'attività Crea gruppo di risorse, se non esiste.

```yaml
resourceGroupName: '<resource-group-name>'
location: '<location>'
```

I collegamenti alle operazioni di distribuzione in un modello denominato `WebSite.json` e un file di parametri denominato WebSite.parameters.json. Usare i nomi dei file di modello e dei parametri.

Ora, che si sappiano creare le attività, è opportuno seguire i passaggi per modificare la pipeline.

1. Aprire la pipeline e sostituire il contenuto con il YAML:

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
       azureSubscription: 'demo-deploy-sp'
       resourceGroupName: demogroup
       location: 'centralus'
       templateLocation: 'URL of the file'
       csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
       csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
       overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
   ```

1. Selezionare **Salva**.

1. Fornire un messaggio per il commit ed eseguire il commit direttamente al **master**.

1. Quando si seleziona **salvare**, la pipeline di compilazione viene eseguita automaticamente. Tornare al riepilogo per la pipeline di compilazione e controllare lo stato.

   ![Visualizzare i risultati](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

È possibile selezionare la pipeline per visualizzare i dettagli sulle attività attualmente in esecuzione. Al termine, noterete che i risultati per ogni passaggio.

## <a name="next-steps"></a>Passaggi successivi

Per la procedura dettagliata sull'uso di pipeline di Azure con modelli di Resource Manager, vedere [esercitazione: Integrazione continua dei modelli di Azure Resource Manager con le pipeline di Azure](resource-manager-tutorial-use-azure-pipelines.md).
