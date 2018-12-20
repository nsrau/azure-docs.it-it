---
title: Distribuire un processo di Analisi di flusso di Azure con CI/CD tramite Azure DevOps
description: Questo articolo descrive come distribuire un processo di Analisi di flusso di Azure usando Azure DevOps Services.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 7e9ce598dbd8987ab32747f5fa9d14646ed4ee71
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164076"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-azure-pipelines"></a>Esercitazione: Distribuire un processo di Analisi di flusso di Azure con CI/CD usando Azure Pipelines
Questa esercitazione illustra come configurare l'integrazione continua e la distribuzione continua per un processo di Analisi di flusso di Azure usando Azure Pipelines. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere il controllo del codice sorgente al progetto
> * Creare una pipeline di compilazione in Azure Pipelines
> * Creare una pipeline di versione in Azure Pipelines
> * Distribuire automaticamente e aggiornare un'applicazione

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, verificare di disporre degli elementi seguenti:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Installare [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) e i carichi di lavoro **Sviluppo di Azure** o **Elaborazione ed archiviazione dati**.
* Creare un [progetto di Analisi di flusso in Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-vs).
* Creare un'organizzazione di [Azure DevOps](https://visualstudio.microsoft.com/team-services/).

## <a name="configure-nuget-package-dependency"></a>Configurare la dipendenza del pacchetto NuGet
Per la compilazione e la distribuzione automatiche in un computer arbitrario, è necessario usare il pacchetto NuGet `Microsoft.Azure.StreamAnalytics.CICD`. Fornisce MSBuild, l'esecuzione locale e gli strumenti di distribuzione che supportano l'integrazione continua e il processo di distribuzione dei progetti di Analisi di flusso per Visual Studio. Per altre informazioni, vedere [Strumenti CI/CD di Analisi di flusso](stream-analytics-tools-for-visual-studio-cicd.md).

Aggiungere **packages.config** alla directory del progetto.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-azure-repos-git-repo"></a>Condividere la soluzione di Visual Studio in un nuovo repository GIT di Azure Repos

Condividere i file di origine dell'applicazione in un progetto in Azure DevOps, in modo da poter generare le compilazioni.  

1. Creare un nuovo repository Git locale per il progetto selezionando **Aggiungi al controllo del codice sorgente**, quindi **Git** nella barra di stato nell'angolo in basso a destra di Visual Studio. 

2. Nella visualizzazione **Sincronizzazione** in **Team Explorer** selezionare il pulsante **Pubblica repository GIT** in **Esegui push in Azure DevOps Services**.

   ![Pulsante Pubblica repository GIT in esegui il push in Azure DevOps Services](./media/stream-analytics-tools-visual-studio-cicd-vsts/publish-git-repo-devops.png)

3. Controllare la posta elettronica e selezionare l'organizzazione nell'elenco a discesa **Azure DevOps Services Domain** (Dominio Azure DevOps Services). Immettere il nome del repository e selezionare **Pubblica repository**.

   ![Pulsante Pubblica Repository GIT in Pubblica repository](./media/stream-analytics-tools-visual-studio-cicd-vsts/publish-repository-devops.png)

    Con la pubblicazione del repository viene creato un nuovo progetto nell'organizzazione con lo stesso nome del repository locale. Per creare il repository in un progetto esistente, fare clic su **Avanzate** accanto al nome del **repository** e selezionare un progetto. È possibile visualizzare il codice nel browser selezionando **Visualizza nel Web**.
 
## <a name="configure-continuous-delivery-with-azure-devops"></a>Configurare la distribuzione continua con Azure DevOps
Una pipeline di compilazione di Azure Pipelines descrive un flusso di lavoro costituito da istruzioni di compilazione eseguite in sequenza. Altre informazioni sulle [pipeline di compilazione di Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav). 

Una pipeline di versione di Azure Pipelines descrive un flusso di lavoro che distribuisce un pacchetto dell'applicazione in un cluster. Se usate insieme, la pipeline di compilazione e la pipeline di versione eseguono l'intero flusso di lavoro, a partire dai file di origine fino alla creazione di un'applicazione in esecuzione nel cluster. Altre informazioni sulle [pipeline di versione](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) di Azure Pipelines.

### <a name="create-a-build-pipeline"></a>Creare una pipeline di compilazione
Aprire un Web browser e passare al progetto appena creato in [Azure DevOps](https://app.vsaex.visualstudio.com/). 

1. Nella scheda **Compilazione e versione** selezionare **Compilazioni** e quindi **+Nuovo**.  Selezionare **Azure DevOps Services Git** (GIT per Azure DevOps Services) e **Continua**.
    
    ![Selezionare l'origine DevOps GIT in Azure DevOps](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source-devops.png)

2. In **Seleziona un modello** fare clic su **Processo vuoto** per iniziare con una pipeline vuota.
    
    ![Selezionare il processo vuoto dalle opzioni del modello in DevOps](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template-empty-process.png)

3. In **Trigger** abilitare l'integrazione continua selezionando lo stato del trigger **Abilita l'integrazione continua**.  Selezionare **Salva e accoda** per avviare manualmente una compilazione. 
    
    ![Abilitare lo stato del trigger dell'integrazione continua](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger-status-ci.png)

4. Le compilazioni vengono attivate anche al momento del push o dell'archiviazione. Per controllare lo stato di avanzamento della compilazione, passare alla scheda **Compilazioni**.  Dopo aver verificato la corretta esecuzione della compilazione, è necessario definire una pipeline di versione per la distribuzione dell'applicazione in un cluster. Fare clic con il pulsante destro del mouse sui puntini di sospensione accanto alla pipeline di compilazione e scegliere **Modifica**.

5.  In **Attività** immettere "Hosted" come **Coda agente**.
    
    ![Selezionare la coda agente nel menu attività](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue-task.png) 

6. In **Fase 1** fare clic su **+** e aggiungere un'attività **NuGet**.
    
    ![Aggiungere un'attività NuGet nella coda agente](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-nuget-task.png)

7. Espandere **Avanzate** e aggiungere `$(Build.SourcesDirectory)\packages` alla **Directory di destinazione**. Mantenere invariati gli atri valori predefiniti della configurazione NuGet.

   ![Configurare le attività di ripristino di NuGet](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-restore-config.png)

8. In **Fase 1** fare clic su **+** e aggiungere un'attività **MSBuild**.

   ![Aggiungere un'attività MSBuild nella coda agente](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-msbuild-task.png)

9. Modificare gli **Argomenti MSBuild** con i valori seguenti:

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![Configurare l'attività MSBuild in DevOps](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-config-msbuild-task.png)

10. In **Fase 1** fare clic su **+** e aggiungere un'attività **Distribuzione gruppo di risorse di Azure**. 
    
    ![Aggiungere un'attività Distribuzione gruppo di risorse di Azure](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-resource-group-deployment.png)

11. Espandere **Dettagli su Azure** e immettere i valori di configurazione seguenti:
    
    |**Impostazione**  |**Valore consigliato**  |
    |---------|---------|
    |Sottoscrizione  |  Scegliere la propria sottoscrizione.   |
    |Azione  |  Creare o aggiornare un gruppo di risorse   |
    |Gruppo di risorse  |  Immettere il nome di un gruppo di risorse.   |
    |Modello  | [Percorso della soluzione]\bin\Debug\Deploy\\[Nome progetto].JobTemplate.json   |
    |Parametri del modello  | [Percorso della soluzione]\bin\Debug\Deploy\\[Nome progetto].JobTemplate.parameters.json   |
    |Eseguire l'override dei parametri del modello  | Digitare nella casella di testo i parametri del modello di cui eseguire l'override. Esempio: –storageName fabrikam –adminUsername $(vmusername) -adminPassword $(password) –azureKeyVaultName $(fabrikamFibre). Questa proprietà è facoltativa, ma si verificheranno errori nella compilazione se non viene eseguito l'override dei parametri chiave.    |
    
    ![Impostare le proprietà per Distribuzione gruppo di risorse di Azure](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deployment-properties.png)

12. Fare clic su **Salva e accoda** per testare la pipeline di compilazione.
    
    ![Salvare e accodare compilazione in DevOps](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-and-queue-build.png)

### <a name="failed-build-process"></a>Processo di compilazione non riuscito
È possibile che vengano visualizzati errori per i parametri di distribuzione Null se non è stato eseguito l'override dei parametri del modello nell'attività **Distribuzione gruppo di risorse di Azure** della pipeline di compilazione. Tornare alla pipeline di compilazione ed eseguire l'override dei parametri Null per risolvere l'errore.

   ![Processo di compilazione di analisi di flusso DevOps non riuscito](./media/stream-analytics-tools-visual-studio-cicd-vsts/devops-build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>Eseguire il commit e il push delle modifiche per attivare una versione
Per verificare che la pipeline di integrazione continua funzioni correttamente, è possibile archiviare alcune modifiche al codice in Azure DevOps.    

Durante la scrittura del codice, le modifiche vengono rilevate automaticamente da Visual Studio. Per eseguire il commit delle modifiche nel repository GIT locale, selezionare l'icona di modifiche in sospeso nella barra di stato in basso a destra.

1. Nella visualizzazione **Modifiche** in Team Explorer aggiungere un messaggio che descrive l'aggiornamento ed eseguire il commit delle modifiche.

    ![Eseguire il commit delle modifiche di repository da Visual Studio](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-commit-changes-visual-studio.png)

2. Selezionare l'icona della barra di stato delle modifiche non pubblicate o la visualizzazione Sincronizzazione in Team Explorer. Selezionare **Esegui push** per aggiornare il codice in Azure DevOps.

    ![Eseguire il push delle modifiche di repository da Visual Studio](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-visual-studio.png)

Il push delle modifiche in Azure DevOps Services attiva automaticamente una compilazione.  Al completamento della pipeline di compilazione viene creata automaticamente una versione e viene avviato l'aggiornamento del processo nel cluster.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il processo di streaming e tutte le risorse correlate. Eliminando il processo si evita di pagare per le unità di streaming usate dal processo. Se si prevede di usare il processo in futuro, è possibile arrestarlo e riavviarlo in un secondo momento, quando è necessario. Se non si intende continuare a usare il processo, eliminare tutte le risorse create tramite questa esercitazione seguendo questa procedura:

1. Scegliere **Gruppi di risorse** dal menu a sinistra del portale di Azure e quindi fare clic sul nome della risorsa creata.  
2. Nella pagina del gruppo di risorse fare clic su **Elimina**, digitare il nome della risorsa da eliminare nella casella di testo e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso degli strumenti di Visual Studio per Analisi di flusso di Azure per configurare un processo di distribuzione e integrazione continue, procedere con l'articolo relativo alla configurazione della pipeline CI/CD:

> [!div class="nextstepaction"]
> [Eseguire integrazione e sviluppo in modo continuo con gli strumenti di Analisi di flusso](stream-analytics-tools-for-visual-studio-cicd.md)
