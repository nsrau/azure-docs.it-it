---
title: Distribuire un processo di analisi di flusso di Azure tramite CI/CD NPM Package
description: Questo articolo descrive come usare il pacchetto NPM di analisi di flusso CI/CD per configurare un processo di integrazione e distribuzione continua.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: deb6c2439cc84f196b7f42fd9f49d3ebfd057cbb
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76962200"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>Distribuire un processo di analisi di flusso di Azure tramite CI/CD NPM Package 

Per configurare un processo di integrazione e distribuzione continua per i processi di analisi di flusso, è possibile usare il pacchetto NPM di analisi di flusso CI/CD di analisi di flusso di Azure. Questo articolo descrive come usare il pacchetto NPM in generale con qualsiasi sistema CI/CD, nonché istruzioni specifiche per la distribuzione con Azure Pipelines.

Per altre informazioni sulla distribuzione con PowerShell, vedere [distribuire con un file di modello Gestione risorse e Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). È anche possibile ottenere altre informazioni su come [usare un oggetto come parametro in un modello di gestione risorse](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

## <a name="build-the-vs-code-project"></a>Compilare il progetto VS Code

È possibile abilitare l'integrazione e la distribuzione continue per i processi di analisi di flusso di Azure usando il pacchetto NPM **ASA-streamanalytics-CICD** . Il pacchetto NPM fornisce gli strumenti per generare Azure Resource Manager modelli di [progetti di analisi di flusso Visual Studio Code](quick-create-vs-code.md). Può essere usato in Windows, macOS e Linux senza installare Visual Studio Code.

È possibile [scaricare il pacchetto](https://www.npmjs.com/package/azure-streamanalytics-cicd) direttamente oppure installarlo a [livello globale](https://docs.npmjs.com/downloading-and-installing-packages-globally) tramite il comando `npm install -g azure-streamanalytics-cicd`. Si tratta dell'approccio consigliato, che può essere usato anche in un'attività script di PowerShell o dell'interfaccia della riga di comando di Azure di una pipeline di compilazione in **Azure Pipelines**.

Dopo aver installato il pacchetto, utilizzare il comando seguente per restituire i modelli di Azure Resource Manager. L'argomento **ScriptPath** è il percorso assoluto del file **asaql** nel progetto. Assicurarsi che i file asaproj. JSON e JobConfig. JSON si trovino nella stessa cartella con il file script. Se **OutputPath** non è specificato, i modelli verranno inseriti nella cartella **deploy** nella cartella **bin** del progetto.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Esempio (in macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Quando un progetto di analisi di flusso Visual Studio Code viene compilato correttamente, genera i seguenti due Azure Resource Manager file modello nella cartella **bin/[debug/Retail]/deploy** : 

*  File del modello di Resource Manager

       [ProjectName].JobTemplate.json 

*  File dei parametri di Resource Manager

       [ProjectName].JobTemplate.parameters.json   

I parametri predefiniti nel file Parameters. JSON sono delle impostazioni nel progetto Visual Studio Code. Se si vuole distribuire in un altro ambiente, sostituire i parametri di conseguenza.

> [!NOTE]
> Per tutte le credenziali, i valori predefiniti sono impostati su Null. È **obbligatorio** impostare i valori prima di eseguire la distribuzione nel cloud.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>Distribuisci con Azure Pipelines

Questa sezione illustra come creare Azure Pipelines pipeline di [compilazione](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) e [rilascio](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) con NPM.

Aprire un Web browser e passare al progetto di analisi di flusso di Azure Visual Studio Code.

1. In **pipeline** nel menu di spostamento a sinistra selezionare **compilazioni**. Quindi selezionare **nuova pipeline**

   ![Crea nuova pipeline di Azure](./media/setup-cicd-vs-code/new-pipeline.png)

2. Selezionare **Usa editor classico** per creare una pipeline senza YAML.

3. Selezionare il tipo di origine, il progetto team e il repository. Selezionare quindi **Continua**.

   ![Selezionare il progetto di analisi di flusso di Azure](./media/setup-cicd-vs-code/select-repo.png)

4. Nella pagina **Scegli un modello** selezionare **processo vuoto**.

### <a name="add-npm-task"></a>Aggiungi attività NPM

1. Nella pagina **attività** , selezionare il segno più accanto a **processo agente 1**. Immettere "NPM" nella ricerca attività e selezionare **NPM**.

   ![Selezionare l'attività NPM](./media/setup-cicd-vs-code/search-npm.png)

2. Assegnare all'attività un **nome visualizzato**. Modificare l'opzione di **comando** in *Custom* e immettere il comando seguente in **Command and arguments**. Lasciare le opzioni predefinite rimanenti.

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![Immettere le configurazioni per l'attività NPM](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>Aggiungi attività riga di comando

1. Nella pagina **attività** , selezionare il segno più accanto a **processo agente 1**. Cerca **riga di comando**.

2. Assegnare all'attività un **nome visualizzato** e immettere lo script seguente. Modificare lo script con il nome del repository e il nome del progetto.

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![Immettere le configurazioni per l'attività della riga di comando](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>Attività Aggiungi copia file

1. Nella pagina **attività** , selezionare il segno più accanto a **processo agente 1**. Cercare **i file di copia**. Quindi, immettere le configurazioni seguenti.

   |Parametro|Input|
   |-|-|
   |Nome visualizzato|Copia file in: $ (Build. artifactstagingdirectory)|
   |Cartella di origine|`$(system.defaultworkingdirectory)`| 
   |Contenuti| `**\Deploy\**` |
   |Cartella di destinazione| `$(build.artifactstagingdirectory)`|

   ![Immettere le configurazioni per l'attività di copia](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>Aggiungi attività pubblica artefatti di compilazione

1. Nella pagina **attività** , selezionare il segno più accanto a **processo agente 1**. Cercare **pubblica artefatti di compilazione** e selezionare l'opzione con l'icona a forma di freccia nera. 

2. Non modificare le configurazioni predefinite.

### <a name="save-and-run"></a>Salva ed Esegui

Dopo aver completato l'aggiunta delle attività NPM, riga di comando, copia file e pubblicazione elementi di compilazione, selezionare **salva & coda**. Quando viene richiesto, immettere un commento Salva e selezionare **Salva ed Esegui**.

## <a name="release-with-azure-pipelines"></a>Rilascia con Azure Pipelines

Aprire un Web browser e passare al progetto di analisi di flusso di Azure Visual Studio Code.

1. In **pipeline** nel menu di spostamento a sinistra selezionare **versioni**. Quindi selezionare **nuova pipeline**.

2. Selezionare **inizia con un processo vuoto**.

3. Nella casella **elementi** selezionare **+ Aggiungi un artefatto**. In **origine**selezionare la pipeline di compilazione appena creata e selezionare **Aggiungi**.

   ![Immettere l'artefatto della pipeline di compilazione](./media/setup-cicd-vs-code/build-artifact.png)

4. Modificare il nome della **fase 1** per **distribuire il processo nell'ambiente di test**.

5. Aggiungere una nuova fase e denominarla **Distribuisci processo in ambiente di produzione**.

### <a name="add-tasks"></a>Aggiungere attività

1. Dall'elenco a discesa attività selezionare **Distribuisci processo in ambiente di testing**. 

2. Selezionare il **+** accanto a **processo di Agent** e cercare *distribuzione gruppo di risorse di Azure*. Immettere i parametri seguenti:

   |Impostazione|Valore|
   |-|-|
   |Nome visualizzato| *Distribuire myASAJob*|
   |Sottoscrizione di Azure| Scegliere la propria sottoscrizione.|
   |Azione| *Crea o aggiorna un gruppo di risorse*|
   |Gruppo di risorse| Scegliere un nome per il gruppo di risorse di test che conterrà il processo di analisi di flusso.|
   |Percorso|Scegliere il percorso del gruppo di risorse di test.|
   |Percorso del modello| *Artefatto collegato*|
   |Modello| $ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.json |
   |Parametri del modello|($ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.parameters.json|
   |Eseguire l'override dei parametri del modello|-Input_IoTHub1_iotHubNamespace $ (test_eventhubname)|
   |Modalità di distribuzione|Incrementale|

3. Dall'elenco a discesa attività selezionare **Distribuisci processo in ambiente di produzione**.

4. Selezionare il **+** accanto a **processo di Agent** e cercare *distribuzione gruppo di risorse di Azure*. Immettere i parametri seguenti:

   |Impostazione|Valore|
   |-|-|
   |Nome visualizzato| *Distribuire myASAJob*|
   |Sottoscrizione di Azure| Scegliere la propria sottoscrizione.|
   |Azione| *Crea o aggiorna un gruppo di risorse*|
   |Gruppo di risorse| Scegliere un nome per il gruppo di risorse di produzione che conterrà il processo di analisi di flusso.|
   |Percorso|Scegliere il percorso del gruppo di risorse di produzione.|
   |Percorso del modello| *Artefatto collegato*|
   |Modello| $ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.json |
   |Parametri del modello|($ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.parameters.json|
   |Eseguire l'override dei parametri del modello|-Input_IoTHub1_iotHubNamespace $ (eventhubname)|
   |Modalità di distribuzione|Incrementale|

### <a name="create-release"></a>Crea versione

Per creare una versione, selezionare **Crea versione** nell'angolo superiore destro.

![Creare una versione usando Azure Pipelines](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>Risorse aggiuntive

Per usare le identità gestite per Azure Data Lake Store Gen1 come sink di output, è necessario fornire l'accesso all'entità servizio con PowerShell prima della distribuzione in Azure. Altre informazioni su come [distribuire ADLS Gen1 con identità gestite usando un modello di Resource Manager](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva: creare un processo cloud di analisi di flusso di Azure in Visual Studio Code (anteprima)](quick-create-vs-code.md)
* [Testare le query di analisi di flusso localmente con Visual Studio Code (anteprima)](visual-studio-code-local-run.md)
* [Esplora analisi di flusso di Azure con Visual Studio Code (anteprima)](visual-studio-code-explore-jobs.md)
