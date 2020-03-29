---
title: Distribuire un processo di Analisi di flusso di Azure usando il pacchetto CI/CD npmDeploy an Azure Stream Analytics job using CI/CD npm package
description: Questo articolo descrive come usare il pacchetto CI/CD npm di Azure Stream Analytics per configurare un processo di integrazione e distribuzione continuo.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: deb6c2439cc84f196b7f42fd9f49d3ebfd057cbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76962200"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>Distribuire un processo di Analisi di flusso di Azure usando il pacchetto CI/CD npmDeploy an Azure Stream Analytics job using CI/CD npm package 

È possibile usare il pacchetto CI/CD npm di Analisi di flusso di Azure per configurare un processo di integrazione e distribuzione continuo per i processi di Analisi di flusso. Questo articolo descrive come usare il pacchetto npm in generale con qualsiasi sistema CI/CD, nonché istruzioni specifiche per la distribuzione con le pipeline di Azure.This article describes how to use the npm package in general with any CI/CD system, as well as specific instructions for deployment with Azure Pipelines.

Per altre informazioni sulla distribuzione con Powershell, vedere Distribuire con un file modello di Resource Manager e Azure PowerShell.For more information about deploying with Powershell, see deploy with a [Resource Manager template file and Azure PowerShell.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) Ulteriori informazioni su come [utilizzare un oggetto come parametro in un modello](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)di Resource Manager .

## <a name="build-the-vs-code-project"></a>Compilare il progetto di codice VSBuild the VS Code project

È possibile abilitare l'integrazione e la distribuzione continue per i processi di Analisi di flusso di Azure usando il pacchetto **asa-streamanalytics-cicd** npm. Il pacchetto npm fornisce gli strumenti per generare modelli di Azure Resource Manager di [Progetti di codice di Visual Studio di Analisi di flusso.](quick-create-vs-code.md) Può essere utilizzato in Windows, macOS e Linux senza installare Visual Studio Code.

È possibile scaricare direttamente [il pacchetto](https://www.npmjs.com/package/azure-streamanalytics-cicd) o installarlo [globalmente](https://docs.npmjs.com/downloading-and-installing-packages-globally) tramite il `npm install -g azure-streamanalytics-cicd` comando. Questo è l'approccio consigliato, che può essere usato anche in un'attività di script PowerShell o DELL'interfaccia della riga di comando di Azure di una pipeline di compilazione nelle pipeline di Azure.This is the recommended approach, which can also be used in a PowerShell or Azure CLI script task of a build pipeline in **Azure Pipelines**.

Dopo aver installato il pacchetto, usare il comando seguente per generare i modelli di Azure Resource Manager.Once you have installed the package, use the following command to output the Azure Resource Manager templates. L'argomento **scriptPath** è il percorso assoluto del file **asaql** nel progetto. Assicurarsi che i file asaproj.json e JobConfig.json si trovino nella stessa cartella del file di script. Se **outputPath** non è specificato, i modelli verranno inseriti nella cartella **Deploy** all'altra nella cartella **bin** del progetto.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Esempio (su macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Quando un progetto di codice di Analisi di flusso viene compilato correttamente, genera i due file di modello di Azure Resource Manager seguenti nella cartella **bin/[Debug/Retail]/Deploy:** 

*  File del modello di Resource Manager

       [ProjectName].JobTemplate.json 

*  File dei parametri di Resource Manager

       [ProjectName].JobTemplate.parameters.json   

I parametri predefiniti nel file parameters.json provengono dalle impostazioni nel progetto di visual Studio Code. Se si vuole distribuire in un altro ambiente, sostituire i parametri di conseguenza.

> [!NOTE]
> Per tutte le credenziali, i valori predefiniti sono impostati su Null. È **obbligatorio** impostare i valori prima di eseguire la distribuzione nel cloud.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>Distribuire con Azure Pipelines

Questa sezione descrive in dettaglio come creare pipeline di [compilazione](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) e [rilascio](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) di Azure Pipelines usando npm.

Aprire un Web browser e passare al progetto di Visual Studio Code di Azure Stream Analytics.Open a web browser and navigate to your Azure Stream Analytics Visual Studio Code project.

1. In **Pipeline** nel menu di spostamento a sinistra selezionare **Compilazioni**. Selezionare **quindi Nuova pipeline**

   ![Creare una nuova pipeline di AzureCreate new Azure Pipeline](./media/setup-cicd-vs-code/new-pipeline.png)

2. Selezionare **Usa l'editor classico** per creare una pipeline senza YAML.

3. Selezionare il tipo di origine, il progetto team e il repository. Quindi selezionare **Continua**.

   ![Selezionare il progetto di Analisi di flusso di AzureSelect Azure Stream Analytics project](./media/setup-cicd-vs-code/select-repo.png)

4. Nella pagina **Scegliere un modello** selezionare Processo **vuoto**.

### <a name="add-npm-task"></a>Aggiungi attività npm

1. Nella pagina **Attività** selezionare il segno più accanto a **Processo agente 1**. Immettere "npm" nella ricerca dell'attività e selezionare **npm**.

   ![Selezionare l'attività npm](./media/setup-cicd-vs-code/search-npm.png)

2. Assegnare all'attività un **nome visualizzato**. Modificare l'opzione **Comando su** *personalizzato* e immettere il comando seguente in Comando **e argomenti**. Lasciare le opzioni predefinite rimanenti.

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![Immettere le configurazioni per l'attività npm](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>Aggiungi attività della riga di comando

1. Nella pagina **Attività** selezionare il segno più accanto a **Processo agente 1**. Cercare **Riga di comando**.

2. Assegnare all'attività un **nome visualizzato** e immettere lo script seguente. Modificare lo script con il nome del repository e il nome del progetto.

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![Immettere le configurazioni per l'attività della riga di comando](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>Attività Aggiungi file di copia

1. Nella pagina **Attività** selezionare il segno più accanto a **Processo agente 1**. Cercare **Copia file**. Quindi immettere le seguenti configurazioni.

   |Parametro|Input|
   |-|-|
   |Nome visualizzato|Copia di file in: (build.artifactstagingdirectory)|
   |Cartella di origine|`$(system.defaultworkingdirectory)`| 
   |Sommario| `**\Deploy\**` |
   |Cartella di destinazione| `$(build.artifactstagingdirectory)`|

   ![Immettere le configurazioni per l'attività Copia](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>Attività Aggiungi pubblicazione elementi di compilazione

1. Nella pagina **Attività** selezionare il segno più accanto a **Processo agente 1**. Cercare **Pubblica elementi di compilazione** e selezionare l'opzione con l'icona a freccia nera. 

2. Non modificare le configurazioni predefinite.

### <a name="save-and-run"></a>Salvare ed eseguire

Dopo aver aggiunto le attività npm, riga di comando, file di copia e pubblicazione di elementi di compilazione, selezionare **Salva & coda**. Quando richiesto, immettere un commento di salvataggio e selezionare **Salva ed esegui**.

## <a name="release-with-azure-pipelines"></a>Rilascio con le pipeline di AzureRelease with Azure Pipelines

Aprire un Web browser e passare al progetto di Visual Studio Code di Azure Stream Analytics.Open a web browser and navigate to your Azure Stream Analytics Visual Studio Code project.

1. In **Pipeline** nel menu di spostamento a sinistra selezionare **Rilasci**. Selezionare quindi **Nuova pipeline**.

2. Selezionare **inizia con un processo vuoto**.

3. Nella casella **Elementi,** selezionare **Aggiungi un elemento**. In **Origine**selezionare la pipeline di compilazione appena creata e scegliere **Aggiungi**.

   ![Immettere l'elemento della pipeline di compilazioneEnter build pipeline artifact](./media/setup-cicd-vs-code/build-artifact.png)

4. Modificare il nome della **Fase 1** in **Distribuisci processo nell'ambiente**di test .

5. Aggiungere una nuova fase e denominarla **Deploy job to production environment**.

### <a name="add-tasks"></a>Aggiungere attività

1. Nell'elenco a discesa Attività selezionare **Distribuisci processo nell'ambiente**di test. 

2. Selezionare **+** il riquadro di lavoro accanto a **Processo agente** e cercare Distribuzione del gruppo di risorse di *Azure.* Immettere i parametri seguenti:

   |Impostazione|valore|
   |-|-|
   |Nome visualizzato| *Distribuire myASAJobDeploy myASAJob*|
   |Sottoscrizione di Azure| Scegliere la propria sottoscrizione.|
   |Azione| *Creare o aggiornare un gruppo di risorse*|
   |Resource group| Scegliere un nome per il gruppo di risorse di test che conterrà il processo di Analisi di flusso.|
   |Location|Scegliere il percorso del gruppo di risorse di test.|
   |Percorso del modello| *Artefatto collegato*|
   |Modello| (Build.ArtifactStagingDirectory) , drop, myASAJob.JobTemplate.json |
   |Parametri del modello|(|
   |Eseguire l'override dei parametri del modello|-Input_IoTHub1_iotHubNamespace (test_eventhubname)|
   |Modalità di distribuzione|Incrementale|

3. Nell'elenco a discesa Attività selezionare **Distribuisci processo nell'ambiente**di produzione.

4. Selezionare **+** il riquadro di lavoro accanto a **Processo agente** e cercare Distribuzione del gruppo di risorse di *Azure.* Immettere i parametri seguenti:

   |Impostazione|valore|
   |-|-|
   |Nome visualizzato| *Distribuire myASAJobDeploy myASAJob*|
   |Sottoscrizione di Azure| Scegliere la propria sottoscrizione.|
   |Azione| *Creare o aggiornare un gruppo di risorse*|
   |Resource group| Scegliere un nome per il gruppo di risorse di produzione che conterrà il processo di Analisi di flusso.|
   |Location|Scegliere l'ubicazione del gruppo di risorse di produzione.|
   |Percorso del modello| *Artefatto collegato*|
   |Modello| (Build.ArtifactStagingDirectory) , drop, myASAJob.JobTemplate.json |
   |Parametri del modello|(|
   |Eseguire l'override dei parametri del modello|-Input_IoTHub1_iotHubNamespace (eventhubname)|
   |Modalità di distribuzione|Incrementale|

### <a name="create-release"></a>Crea versione

Per creare una release, seleziona **Crea versione** nell'angolo in alto a destra.

![Creare una versione usando le pipeline di AzureCreate a release using Azure Pipelines](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>Risorse aggiuntive

Per usare le identità gestite per Azure Data Lake Store Gen1 come sink di output, è necessario fornire l'accesso all'entità servizio con PowerShell prima della distribuzione in Azure. Altre informazioni su come [distribuire ADLS Gen1 con identità gestite usando un modello di Resource Manager](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva: Creare un processo cloud di Analisi flusso di Azure nel codice di Visual Studio (anteprima)Quickstart: Create an Azure Stream Analytics cloud job in Visual Studio Code (Preview)](quick-create-vs-code.md)
* [Test Stream Analytics esegue query localmente con visual Studio Code (Anteprima)Test Stream Analytics queries locally with Visual Studio Code (Preview)](visual-studio-code-local-run.md)
* [Esplora Analisi di flusso di Azure con il codice di Visual Studio (anteprima)Explore Azure Stream Analytics with Visual Studio Code (Preview)](visual-studio-code-explore-jobs.md)
