---
title: Esercitazione per distribuire un processo di Analisi di flusso di Azure con CI/CD tramite VSTS
description: Questo articolo descrive come distribuire un processo di Analisi di flusso con CI/CD tramite VSTS.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 7/10/2018
ms.openlocfilehash: 303c1cfaf2b91712f706c5b78e027bb02739c770
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39074367"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-vsts"></a>Esercitazione: Distribuire un processo di Analisi di flusso di Azure con CI/CD tramite VSTS
Questa esercitazione illustra come configurare l'integrazione e la distribuzione continue per un processo di Analisi di flusso di Azure tramite Visual Studio Team Services. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere il controllo del codice sorgente al progetto
> * Creare una definizione di compilazione in Team Services
> * Creare una definizione di versione in Team Services
> * Distribuire automaticamente e aggiornare un'applicazione

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, verificare di disporre degli elementi seguenti:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Installare [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) e i carichi di lavoro **Sviluppo di Azure** o **Elaborazione ed archiviazione dati**.
* Creare un [progetto di Analisi di flusso in Visual Studio](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-quick-create-vs).
* Creare un account di [Visual Studio Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).

## <a name="configure-nuget-package-dependency"></a>Configurare la dipendenza del pacchetto NuGet
Per la compilazione e la distribuzione automatiche in un computer arbitrario, è necessario usare il pacchetto NuGet `Microsoft.Azure.StreamAnalytics.CICD`. Fornisce MSBuild, l'esecuzione locale e gli strumenti di distribuzione che supportano l'integrazione continua e il processo di distribuzione dei progetti di Analisi di flusso per Visual Studio. Per altre informazioni, vedere [Strumenti CI/CD di Analisi di flusso](stream-analytics-tools-for-visual-studio-cicd.md).

Aggiungere **packages.config** alla directory del progetto.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>Condividere la soluzione di Visual Studio in un nuovo repository GIT di Team Services
È possibile condividere i file di origine dell'applicazione in un progetto team in Team Services, in modo da poter generare le compilazioni.  

1. Creare un nuovo repository Git locale per il progetto selezionando **Aggiungi al controllo del codice sorgente**, quindi **Git** nella barra di stato nell'angolo in basso a destra di Visual Studio. 

2. Nella visualizzazione **Sincronizzazione** in **Team Explorer** selezionare il pulsante **Pubblica repository GIT** nella sezione **Effettua il push in Visual Studio Team Services**.

   ![Pubblicare il repository GIT](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishgitrepo.png)

3. Controllare la posta elettronica e selezionare il proprio account nell'elenco a discesa **Dominio Team Services**. Immettere il nome del repository e selezionare **Pubblica repository**.

   ![Pubblicare il repository GIT](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishcode.png)

    Con la pubblicazione del repository viene creato un nuovo progetto team nell'account con lo stesso nome del repository locale. Per creare il repository in un progetto team esistente, fare clic su **Avanzate** accanto al nome del **Repository** e selezionare un progetto team. È possibile visualizzare il codice nel browser selezionando **Visualizza nel Web**.
 
## <a name="configure-continuous-delivery-with-vsts"></a>Configurare il recapito continuo con VSTS
Una definizione di compilazione di Team Services descrive un flusso di lavoro costituito da istruzioni di compilazione che vengono eseguite in sequenza. Sono disponibili maggiori informazioni sulle [definizioni di compilazione di Team Services](https://www.visualstudio.com/docs/build/define/create). 

Una definizione di versione di Team Services descrive un flusso di lavoro che distribuisce un pacchetto di applicazione in un cluster. Se usate insieme, la definizione di compilazione e la definizione di versione eseguono l'intero flusso di lavoro a partire dai file di origine fino alla creazione di un'applicazione funzionante nel cluster. Sono disponibili maggiori informazioni sulle [definizioni di versione](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)di Team Services.

### <a name="create-a-build-definition"></a>Creare una definizione di compilazione
Aprire un Web browser e passare al progetto team appena creato in [Visual Studio Team Services](https://app.vsaex.visualstudio.com/). 

1. Nella scheda **Compilazione e versione** selezionare **Compilazioni** e quindi **+Nuovo**.  Selezionare **GIT VSTS** e **Continua**.
    
    ![Selezionare l'origine](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source.png)

2. In **Selezionare un modello** fare clic su **Processo vuoto** per iniziare con una definizione vuota.
    
    ![Scegliere il modello di compilazione](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template.png)

3. In **Trigger** abilitare l'integrazione continua selezionando lo stato del trigger **Abilita l'integrazione continua**.  Selezionare **Salva e accoda** per avviare manualmente una compilazione. 
    
    ![Stato del trigger](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger.png)

4. Le compilazioni vengono attivate anche al momento del push o dell'archiviazione. Per controllare lo stato di avanzamento della compilazione, passare alla scheda **Compilazioni**.  Dopo aver verificato che la compilazione viene eseguita correttamente, è necessario definire una definizione di versione per la distribuzione dell'applicazione in un cluster. Fare clic con il pulsante destro del mouse sui puntini di sospensione accanto alla definizione di compilazione e scegliere **Modifica**.

5.  In **Attività** immettere "Hosted" come **Coda agente**.
    
    ![Seleziona una coda agente](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue.png) 

6. In **Fase 1** fare clic su **+** e aggiungere un'attività **NuGet**.
    
    ![Aggiungere un'attività NuGet](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget.png)

7. Espandere **Avanzate** e aggiungere `$(Build.SourcesDirectory)\packages` alla **Directory di destinazione**. Mantenere invariati gli atri valori predefiniti della configurazione NuGet.

   ![Configurare l'attività NuGet](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-config.png)

8. In **Fase 1** fare clic su **+** e aggiungere un'attività **MSBuild**.

   ![Aggiungere un'attività MSBuild](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild-task.png)

9. Modificare gli **Argomenti MSBuild** con i valori seguenti:

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![Configurare l'attività MSBuild](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild.png)

10. In **Fase 1** fare clic su **+** e aggiungere un'attività **Distribuzione gruppo di risorse di Azure**. 
    
    ![Aggiungere un'attività Distribuzione gruppo di risorse di Azure](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy.png)

11. Espandere **Dettagli su Azure** e immettere i valori di configurazione seguenti:
    
    |**Impostazione**  |**Valore consigliato**  |
    |---------|---------|
    |Sottoscrizione  |  Scegliere la propria sottoscrizione.   |
    |Azione  |  Creare o aggiornare un gruppo di risorse   |
    |Gruppo di risorse  |  Immettere il nome di un gruppo di risorse.   |
    |Modello  | [Percorso della soluzione]\bin\Debug\Deploy\\[Nome progetto].JobTemplate.json   |
    |Parametri del modello  | [Percorso della soluzione]\bin\Debug\Deploy\\[Nome progetto].JobTemplate.parameters.json   |
    |Eseguire l'override dei parametri del modello  | Digitare nella casella di testo i parametri del modello di cui eseguire l'override. Esempio: –storageName fabrikam –adminUsername $(vmusername) -adminPassword $(password) –azureKeyVaultName $(fabrikamFibre). Questa proprietà è facoltativa, ma si verificheranno errori nella compilazione se non viene eseguito l'override dei parametri chiave.    |
    
    ![Impostare le proprietà](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy-2.png)

12. Fare clic su **Salva e accoda** per testare la definizione di compilazione.
    
    ![Impostare l'override dei parametri](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-queue.png)

### <a name="failed-build-process"></a>Processo di compilazione non riuscito
È possibile ricevere errori per i parametri di distribuzione Null se non è stato eseguito l'override dei parametri del modello nell'attività **Distribuzione gruppo di risorse di Azure** della definizione di compilazione. Tornare alla definizione di compilazione ed eseguire l'override dei parametri Null per risolvere l'errore.

   ![Processo di compilazione non riuscito](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>Eseguire il commit e il push delle modifiche per attivare una versione
Per verificare che la pipeline di integrazione continua funzioni correttamente, è possibile archiviare alcune modifiche al codice in Team Services.    

Durante la scrittura del codice, le modifiche vengono rilevate automaticamente da Visual Studio. Per eseguire il commit delle modifiche nel repository GIT locale, selezionare l'icona di modifiche in sospeso nella barra di stato in basso a destra.

1. Nella visualizzazione **Modifiche** in Team Explorer aggiungere un messaggio che descrive l'aggiornamento ed eseguire il commit delle modifiche.

    ![Commit e push delle modifiche](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes.png)

2. Selezionare l'icona della barra di stato delle modifiche non pubblicate o la visualizzazione Sincronizzazione in Team Explorer. Selezionare **Push** per aggiornare il codice in Team Services/TFS.

    ![Commit e push delle modifiche](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-2.png)

Il push delle modifiche in Team Services attiva automaticamente una compilazione.  Quando la definizione di compilazione viene completata correttamente, viene creata automaticamente una versione con avvio dell'aggiornamento del processo nel cluster.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il processo di streaming e tutte le risorse correlate. Eliminando il processo si evita di pagare per le unità di streaming usate dal processo. Se si prevede di usare il processo in futuro, è possibile arrestarlo e riavviarlo in un secondo momento, quando è necessario. Se non si intende continuare a usare il processo, eliminare tutte le risorse create tramite questa esercitazione seguendo questa procedura:

1. Scegliere **Gruppi di risorse** dal menu a sinistra del portale di Azure e quindi fare clic sul nome della risorsa creata.  
2. Nella pagina del gruppo di risorse fare clic su **Elimina**, digitare il nome della risorsa da eliminare nella casella di testo e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso degli strumenti di Visual Studio per Analisi di flusso di Azure per configurare un processo di distribuzione e integrazione continue, procedere con l'articolo relativo alla configurazione della pipeline CI/CD:

> [!div class="nextstepaction"]
> [Eseguire integrazione e sviluppo in modo continuo con gli strumenti di Analisi di flusso](stream-analytics-tools-for-visual-studio-cicd.md)