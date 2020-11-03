---
title: CI/CD con Azure Pipelines e modelli
description: Viene descritto come configurare l'integrazione continua in Azure Pipelines usando modelli Azure Resource Manager. Mostra come usare uno script di PowerShell o copiare i file in un percorso di gestione temporanea e distribuirli da questa posizione.
ms.topic: conceptual
ms.date: 10/01/2020
ms.openlocfilehash: 86ad2839375b73bf9595cf3369960e614ec03e67
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93233815"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>Integrare i modelli di Azure Resource Manager con Azure Pipelines

È possibile integrare modelli di Azure Resource Manager (modelli ARM) con Azure Pipelines per l'integrazione continua e la distribuzione continua (CI/CD). L'esercitazione [integrazione continua dei modelli ARM con Azure Pipelines](deployment-tutorial-pipeline.md) illustra come usare l' [attività di distribuzione del modello ARM](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md) per distribuire un modello dal repository GitHub. Questo approccio funziona quando si desidera distribuire un modello direttamente da un repository.

In questo articolo vengono illustrati altri due modi per distribuire i modelli con Azure Pipelines. Questo articolo illustra come:

* **Aggiungere un'attività che esegue uno script Azure PowerShell**. Questa opzione offre il vantaggio di garantire la coerenza per tutto il ciclo di vita dello sviluppo, perché è possibile usare lo stesso script usato durante l'esecuzione di test locali. Lo script distribuisce il modello, ma può anche eseguire altre operazioni, ad esempio il recupero di valori da utilizzare come parametri.

   Visual Studio fornisce il [progetto gruppo di risorse di Azure](create-visual-studio-deployment-project.md) che include uno script di PowerShell. Lo script crea una fase di creazione degli artefatti dal progetto a un account di archiviazione a cui Gestione risorse può accedere. Gli artefatti sono elementi del progetto, ad esempio modelli collegati, script e file binari dell'applicazione. Se si vuole continuare a usare lo script dal progetto, usare l'attività script di PowerShell illustrata in questo articolo.

* **Aggiunta di attività per la copia e la distribuzione di attività**. Questa opzione offre una comoda alternativa allo script del progetto. Si configurano due attività nella pipeline. Un'attività consente di eseguire la fase degli elementi in una posizione accessibile. L'altra attività distribuisce il modello da tale percorso.

## <a name="prepare-your-project"></a>Preparare il progetto

Questo articolo presuppone che il modello ARM e l'organizzazione DevOps di Azure siano pronti per la creazione della pipeline. I passaggi seguenti illustrano come assicurarsi di essere pronti:

* Si dispone di un'organizzazione di Azure DevOps. Se non è disponibile, [crearne uno gratuitamente](/azure/devops/pipelines/get-started/pipelines-sign-up). Se il team ha già un'organizzazione DevOps di Azure, assicurarsi di essere un amministratore del progetto Azure DevOps che si vuole usare.

* È stata configurata una [connessione al servizio](/azure/devops/pipelines/library/connect-to-azure) per la sottoscrizione di Azure. Le attività nella pipeline vengono eseguite con l'identità dell'entità servizio. Per i passaggi necessari per creare la connessione, vedere [creare un progetto DevOps](deployment-tutorial-pipeline.md#create-a-devops-project).

* Si dispone di un [modello ARM](quickstart-create-templates-use-visual-studio-code.md) che definisce l'infrastruttura per il progetto.

## <a name="create-pipeline"></a>Creare una pipeline

1. Se non è stata aggiunta una pipeline in precedenza, è necessario creare una nuova pipeline. Dall'organizzazione DevOps di Azure selezionare **pipeline** e **nuova pipeline**.

   ![Aggiungi nuova pipeline](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Specificare la posizione in cui è archiviato il codice. La figura seguente mostra la selezione di **Azure Repos git**.

   ![Seleziona origine codice](./media/add-template-to-azure-pipelines/select-source.png)

1. Da tale origine selezionare il repository che contiene il codice per il progetto.

   ![Seleziona repository](./media/add-template-to-azure-pipelines/select-repo.png)

1. Consente di selezionare il tipo di pipeline da creare. È possibile selezionare **pipeline di avvio**.

   ![Seleziona pipeline](./media/add-template-to-azure-pipelines/select-pipeline.png)

È ora possibile aggiungere un'attività di Azure PowerShell o il file di copia e distribuire le attività.

## <a name="azure-powershell-task"></a>Attività di Azure PowerShell

Questa sezione illustra come configurare la distribuzione continua usando una singola attività che esegue lo script di PowerShell nel progetto. Se è necessario uno script di PowerShell che distribuisce un modello, vedere [Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) o [Deploy-AzureResourceGroup.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzureResourceGroup.ps1).

Il file YAML seguente crea un' [attività Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell):

```yml
trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: AzurePowerShell@5
  inputs:
    azureSubscription: 'script-connection'
    ScriptType: 'FilePath'
    ScriptPath: './Deploy-AzTemplate.ps1'
    ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
    azurePowerShellVersion: 'LatestVersion'
```

Quando si imposta l'attività su `AzurePowerShell@5` , la pipeline usa il [modulo AZ](/powershell/azure/new-azureps-module-az). Se si usa il modulo AzureRM nello script, impostare l'attività su `AzurePowerShell@3` .

```yaml
steps:
- task: AzurePowerShell@3
```

Per `azureSubscription` , specificare il nome della connessione al servizio creata.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Per `scriptPath` , specificare il percorso relativo dal file della pipeline allo script. È possibile esaminare il repository per visualizzare il percorso.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

In `ScriptArguments` specificare tutti i parametri necessari per lo script. Nell'esempio seguente vengono illustrati alcuni parametri per uno script, ma è necessario personalizzare i parametri per lo script.

```yaml
ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
```

Quando si seleziona **Salva** , la pipeline di compilazione viene eseguita automaticamente. Tornare al riepilogo per la pipeline di compilazione e controllare lo stato.

![Visualizzazione dei risultati](./media/add-template-to-azure-pipelines/view-results.png)

È possibile selezionare la pipeline attualmente in esecuzione per visualizzare i dettagli relativi alle attività. Al termine, vengono visualizzati i risultati per ogni passaggio.

## <a name="copy-and-deploy-tasks"></a>Copiare e distribuire attività

In questa sezione viene illustrato come configurare la distribuzione continua utilizzando due attività. La prima attività esegue la fase degli elementi in un account di archiviazione e la seconda attività distribuisce il modello.

Per copiare i file in un account di archiviazione, all'entità servizio per la connessione al servizio deve essere assegnato il ruolo di collaboratore dati BLOB di archiviazione o di proprietario dati BLOB di archiviazione. Per altre informazioni, vedere [Introduzione a AzCopy](../../storage/common/storage-use-azcopy-v10.md).

Il codice YAML seguente mostra l' [attività copia file di Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy).

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
```

Esistono diverse parti di questa attività da rivedere per l'ambiente in uso. `SourcePath`Indica la posizione degli elementi relativi al file della pipeline.

```yaml
SourcePath: '<path-to-artifacts>'
```

Per `azureSubscription` , specificare il nome della connessione al servizio creata.

```yaml
azureSubscription: '<your-connection-name>'
```

Per archiviazione e nome contenitore specificare i nomi dell'account di archiviazione e del contenitore che si vuole usare per archiviare gli artefatti. L'account di archiviazione deve esistere.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

Dopo aver creato l'attività copia file, è possibile aggiungere l'attività per distribuire il modello di gestione temporanea.

Il YAML seguente mostra l' [attività di distribuzione del modello di Azure Resource Manager](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md):

```yaml
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

Esistono diverse parti di questa attività da esaminare in modo più dettagliato.

- `deploymentScope`: Selezionare l'ambito di distribuzione dalle opzioni: `Management Group` , `Subscription` e `Resource Group` . Per altre informazioni sugli ambiti, vedere [Ambiti di distribuzione](deploy-rest.md#deployment-scope).

- `azureResourceManagerConnection`: Specificare il nome della connessione al servizio creata.

- `subscriptionId`: Specificare l'ID sottoscrizione di destinazione. Questa proprietà si applica solo all'ambito di distribuzione del gruppo di risorse e all'ambito di distribuzione della sottoscrizione.

- `resourceGroupName` e `location` : specificare il nome e il percorso del gruppo di risorse in cui si vuole eseguire la distribuzione. L'attività crea il gruppo di risorse, se non esiste.

   ```yml
   resourceGroupName: '<resource-group-name>'
   location: '<location>'
   ```

- `csmFileLink`: Specificare il collegamento per il modello di gestione temporanea. Quando si imposta il valore, utilizzare le variabili restituite dall'attività copia file. Nell'esempio seguente viene collegato a un modello denominato mainTemplate.json. La cartella **templates** è inclusa, in quanto l'attività di copia di file ha copiato il file in. Nella pipeline specificare il percorso del modello e il nome del modello.

   ```yml
   csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
   ```

L'aspetto della pipeline è simile al seguente:

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

Quando si seleziona **Salva** , la pipeline di compilazione viene eseguita automaticamente. Tornare al riepilogo per la pipeline di compilazione e controllare lo stato.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'uso dei modelli ARM con le azioni di GitHub, vedere [distribuire modelli di Azure Resource Manager usando le azioni di GitHub](deploy-github-actions.md).
