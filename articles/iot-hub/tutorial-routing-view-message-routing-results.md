---
title: Visualizzare i risultati del routing di messaggi nell'hub IoT di Azure (.NET) | Microsoft Docs
description: Visualizzare il risultati del routing di messaggi nell'hub IoT di Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: f34799bbf2142ba07c29915deae5b5dbe590c9fc
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330549"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>Esercitazione: Parte 2 - Visualizzare i messaggi instradati

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>Regole per il routing dei messaggi

Queste sono le regole per il routing dei messaggi, che sono state configurate nella parte 1 di questa esercitazione e che in questa seconda parte vengono viste in azione.

|value |Risultato|
|------|------|
|level="storage" |Scrivere ad archiviazione di Azure|
|level="critical" |Scrivere a una coda del bus di servizio. Un'app per la logica recupera il messaggio dalla coda e usa Office 365 per inviare il messaggio con posta elettronica.|
|default |Visualizzare questi dati con Power BI.|

Creare ora le risorse a cui verranno instradati i messaggi, eseguire un'app per inviare i messaggi all'hub e vedere il routing in azione.

## <a name="create-a-logic-app"></a>Creare un'app per la logica  

La coda del Bus di servizio viene usata per la ricezione di messaggi designati come critici. Configurare un'app per la logica per monitorare la coda del Bus di servizio e inviare un messaggio di posta elettronica quando viene aggiunto un messaggio alla coda.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **+ Crea una risorsa**. Inserire **app per la logica** nella casella di ricerca e fare clic su Invio. Nei risultati della ricerca visualizzati selezionare App per la logica, quindi **Crea** per passare al riquadro **Crea app per la logica**. Compilare i campi.

   **Nome**: il nome dell'app per la logica. Questa esercitazione usa **ContosoLogicApp**.

   **Sottoscrizione** Selezionare la sottoscrizione di Azure.

   **Gruppo di risorse**: selezionare **Usa esistente** e quindi il gruppo di risorse. Questa esercitazione usa **ContosoResources**.

   **Località**: usare la propria località. Questa esercitazione usa **Stati Uniti occidentali**.

   **Log Analytics**: questo interruttore deve essere disattivato.

   ![Schermata Crea app per la logica](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   Selezionare **Create** (Crea).

2. Passare ora ad App per la logica. Il modo più semplice per ottenere l'App per la logica è fare clic su **Gruppi di risorse**, selezionare il gruppo di risorse (questa esercitazione usa **ContosoResources**), quindi selezionare l'App per la logica nell'elenco di risorse. Viene visualizzata la pagina della finestra di progettazione dell'App per la logica (potrebbe essere necessario scorrere verso destra per visualizzare l'intera pagina). Nella pagina della finestra di progettazione delle app per la logica, scorrere verso il basso fino a visualizzare il riquadro **App per la logica vuota +** , quindi selezionarlo. La scheda predefinita è "Per l'utente". Se questo riquadro è vuoto, selezionare **Tutti** per visualizzare tutti i connettori e i trigger disponibili.

3. Selezionare **Bus di servizio** nell'elenco di connettori.

   ![L'elenco di connettori](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. Verrà visualizzato un elenco di trigger. Selezionare **Quando un messaggio viene ricevuto in una coda (completamento automatico)/Bus di servizio**.

   ![L'elenco di trigger per il bus di servizio](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. Nella schermata successiva, inserire il nome della connessione. Questa esercitazione usa **ContosoConnection**.

   ![Configurazione della connessione per la coda del bus di servizio](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   Selezionare lo spazio dei nomi del bus di servizio. Questa esercitazione usa **ContosoSBNamespace**. Quando si seleziona lo spazio dei nomi, il portale esegue una query per lo spazio dei nomi del Bus di servizio per recuperare le chiavi. Selezionare **RootManageSharedAccessKey** e quindi **Crea**.

   ![Completamento della configurazione della connessione](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. Nella schermata successiva, selezionare il nome della coda (questa esercitazione usata **contososbqueue**) dall'elenco a discesa. È possibile usare le impostazioni predefinite nei campi rimanenti.

   ![Le opzioni della coda](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. Configurare ora l'azione per inviare un messaggio di posta elettronica quando viene ricevuto un messaggio nella coda. Nella finestra di progettazione delle app per la logica selezionare **+ Nuovo passaggio** per aggiungere un passaggio, quindi selezionare **Tutti** per visualizzare tutte le opzioni disponibili. Nel riquadro **Scegliere un'azione**, trovare e selezionare **Office 365 Outlook**. Nella schermata dei trigger, selezionare **Invia un messaggio di posta elettronica/Office 365 Outlook**.  

   ![Le opzioni di Office365](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. Accedere all'account di Office 365 per configurare la connessione. Se si verifica un timeout, riprovare. Specificare gli indirizzi di posta elettronica per i destinatari del messaggio di posta elettronica. Specificare anche l'oggetto e digitare il messaggio che si desidera il destinatario visualizzi nel corpo. Per i test, inserire il proprio indirizzo di posta elettronica come destinatario.

   Selezionare **Aggiungi contenuto dinamico** per visualizzare il contenuto del messaggio che è possibile includere. Selezionare **Contenuto**: includerà il messaggio di posta elettronica.

   ![Le opzioni di posta elettronica per l'app per la logica](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. Selezionare **Salva**. Quindi chiudere la finestra di progettazione delle app per la logica

## <a name="set-up-azure-stream-analytics"></a>Configurare Analisi di flusso di Azure

Per visualizzare i dati in una visualizzazione di Power BI, innanzitutto impostare un processo di Analisi di flusso di Azure per recuperare i dati. Tenere presente che solo i messaggi in cui il **livello** è **normale** vengono inviati all'endpoint predefinito e verranno recuperati dal processo di analisi di flusso per la visualizzazione di Power BI.

### <a name="create-the-stream-analytics-job"></a>Creare il processo di Analisi di flusso

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** > **Internet delle cose** > **Processo di Analisi di flusso**.

2. Immettere le seguenti informazioni per il processo.

   **Nome processo**: Nome del processo. Il nome deve essere univoco a livello globale. Questa esercitazione usa **contosoJob**.

   **Sottoscrizione** la sottoscrizione di Azure usata per l'esercitazione.

   **Gruppo di risorse**: usare lo stesso gruppo di risorse usato dall'hub IoT dell'utente. Questa esercitazione usa **ContosoResources**.

   **Località**: usare la stessa località usata nello script di configurazione. Questa esercitazione usa **Stati Uniti occidentali**.

   ![Creare il processo di Analisi di flusso](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. Fare clic su **Crea** per creare il processo. Per tornare al processo, selezionare **Gruppi di risorse**. Questa esercitazione usa **ContosoResources**. Selezionare il gruppo di risorse, quindi il processo di Analisi di flusso nell'elenco di risorse.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Aggiungere un input al processo di Analisi di flusso

4. In **Topologia processo** selezionare **Input**.

5. Nel riquadro **Input** selezionare **Aggiungi input del flusso** e quindi Hub IoT. Nella schermata che viene visualizzata, compilare i campi seguenti:

   **Alias di input**: Questa esercitazione usa **contosoinputs**.

   **Selezionare l'hub IoT dalle sottoscrizioni correnti**: selezionare questa opzione del pulsante di opzione.

   **Sottoscrizione** selezionare la sottoscrizione usata per questa esercitazione.

   **Hub IoT**: selezionare l'hub IoT. Questa esercitazione usa **ContosoTestHub**.

   **Endpoint**: selezionare **Messaggistica**. (Se si seleziona Monitoraggio delle operazioni, si ottengono i dati di telemetria relativi all'hub IoT anziché i dati che si stanno inviando.) 

   **Nome criteri di accesso condiviso**: Selezionare **Servizio**. Nel portale viene inserita la chiave di criteri di accesso condivisi per l'utente.

   **Gruppo di consumer**: selezionare il gruppo di consumer configurato nel passaggio 1 di questa esercitazione. Questa esercitazione usa **contosoconsumers**.
   
   Accettare i valori predefiniti nei campi rimanenti. 

   ![Configurare gli input per il processo di Analisi di flusso](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

6. Selezionare **Salva**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Aggiungere un output al processo di Analisi di flusso

1. In **Topologia processo** selezionare **Output**.

2. Nel riquadro **Output** selezionare **Aggiungi** e quindi **Power BI**. Nella schermata che viene visualizzata, compilare i campi seguenti:

   **Alias di output**: l'alias univoco per l'output. Questa esercitazione usa **contosooutputs**. 

   **Nome del set di dati**: il nome del set di dati da usare in Power BI. Questa esercitazione usa **contosodataset**. 

   **Nome tabella**: il nome della tabella da usare in Power BI. Questa esercitazione usa **contosotable**.

   Accettare le impostazioni predefinite nei campi rimanenti.

3. Selezionare **Autorizza** e accedere all'account di Power BI. Questa operazione potrebbe richiedere più di un tentativo.

   ![Configurare gli output per il processo di Analisi di flusso](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. Selezionare **Salva**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurare la query del processo di Analisi di flusso

1. In **Topologia processo** selezionare **Query**.

2. Sostituire `[YourInputAlias]` con l'alias di input del processo. Questa esercitazione usa **contosoinputs**.

3. Sostituire `[YourOutputAlias]` con l'alias di output del processo. Questa esercitazione usa **contosooutputs**.

   ![Configurare la query per il processo di Analisi di flusso](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. Selezionare **Salva**.

5. Chiudere il riquadro della query. Si tornerà alla visualizzazione delle risorse nel gruppo di risorse. Selezionare il processo di Analisi di flusso. In questa esercitazione è denominato **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Eseguire il processo di Analisi di flusso

Nel processo di Analisi di flusso selezionare **Avvia** > **Ora** > **Avvia**. Dopo aver avviato correttamente il processo, lo stato del processo passa da **Interrotto** a **In esecuzione**.

Per configurare il report di Power BI, sono necessari dei dati, pertanto si imposterà Power BI dopo la creazione del dispositivo e dopo l'esecuzione dell'applicazione di simulazione del dispositivo.

## <a name="run-simulated-device-app"></a>Eseguire l'app del dispositivo simulato

Nella parte 1 di questa esercitazione è stato configurato un dispositivo per simulare l'uso di un dispositivo IoT. In questa sezione scaricare l'app console .NET che simula questo dispositivo che invia messaggi da dispositivo a cloud a un hub IoT, presupponendo che l'app e le risorse non siano già state scaricate nella parte 1 dell'esercitazione.

Questa applicazione invia messaggi per ognuno dei diversi metodi di routing. Il download include anche una cartella contenente il modello di Azure Resource Manager completo e il file di parametri, oltre agli script dell'interfaccia della riga di comando di Azure e di PowerShell.

Se i file non sono stati scaricati dal repository nel passaggio 1 di questa esercitazione, procedere a scaricarli ora dalla [simulazione di dispositivi IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Selezionando questo collegamento viene scaricato un repository contenente diverse applicazioni; la soluzione desiderata si trova in iot-hub/Tutorials/Routing/IoT_SimulatedDevice.sln. 

Fare doppio clic sul file di soluzione (IoT_SimulatedDevice.sln) per aprire il codice in Visual Studio, quindi aprire Program.cs. Sostituire `{iot hub hostname}` con il nome host dell'hub IoT. Il formato del nome host dell'hub IoT **{nome hub iot} .azure-devices.net**. Per questa esercitazione, il nome host dell'hub è **ContosoTestHub.azure devices.net**. Successivamente, sostituire `{device key}` con la chiave del dispositivo salvato in precedenza durante la configurazione del dispositivo simulato. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Esecuzione e test

Eseguire l'applicazione console. Attendere qualche minuto. È possibile visualizzare i messaggi inviati nella finestra della console dell'applicazione.

L'app invia un nuovo messaggio da dispositivo a cloud all'hub IoT ogni secondo. Il messaggio contiene un oggetto serializzato JSON con l'ID del dispositivo, la temperatura, l'umidità e il livello del messaggio, che per impostazione predefinita è `normal`. Assegnare in modo casuale un livello di `critical` o `storage`, eseguendo quindi il routing del messaggio all'account di archiviazione o alla coda del Bus di servizio (che attiva l'App per la logica per l'invio del messaggio di posta elettronica). Le letture del valore predefinito (`normal`) saranno visualizzate nel report BI che sarà configurato in seguito.

Se tutto è configurato correttamente, a questo punto si dovrebbero visualizzare i seguenti risultati:

1. Si iniziano a ricevere messaggi di posta elettronica relative ai messaggi critici.

   ![I messaggi di posta elettronica risultanti](./media/tutorial-routing-view-message-routing-results/results-in-email.png)

   Questo risultato implica che le affermazioni seguenti sono vere. 

   * Il routing alla coda del Bus di servizio funziona correttamente.
   * L'app per la logica che recupera il messaggio dalla coda del Bus di servizio funziona correttamente.
   * Il connettore app per la logica per Outlook funziona correttamente. 

2. Nel [portale di Azure](https://portal.azure.com) selezionare **Gruppi di risorse** e quindi il proprio gruppo di risorse. Questa esercitazione usa **ContosoResources**. Selezionare l'account di archiviazione, **BLOB** e quindi il contenitore. Questa esercitazione usa **contosoresults**. Dovrebbe essere visualizzata una cartella ed è possibile eseguire il drill-down nelle directory fino a quando non viene visualizzato uno o più file. Aprire uno di tali file; essi contengono le voci indirizzate all'account di archiviazione. 

   ![I file dei risultati nella risorsa di archiviazione](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

Questo risultato implica che l'affermazione seguente è vera.

   * Il routing all'account di archiviazione funziona correttamente.

Ora con l'applicazione ancora in esecuzione, configurare la visualizzazione di Power BI per visualizzare i messaggi in arrivo tramite il routing predefinito.

## <a name="set-up-the-power-bi-visualizations"></a>Configurare le visualizzazioni di Power BI

1. Accedere all'account [Power BI](https://powerbi.microsoft.com/).

2. Passare all'**area di lavoro** e selezionare l'area di lavoro impostata quando è stato creato l'output del processo di analisi di flusso. In questa esercitazione viene usato **Area di lavoro personale**. 

3. Selezionare **Set di dati**. Se non sono disponibili set di dati, attendere qualche minuto e ricontrollare.

   Dovrebbero essere elencati i set di dati specificati durante la creazione di output per il processo di analisi di flusso. Questa esercitazione usa **contosodataset**. (La prima volta, potrebbero volerci da 5 a 10 minuti per il set di dati da visualizzare).

4. In **AZIONI** selezionare prima icona per creare un report.

   ![Area di lavoro di Power BI con Azioni e l'icona dei report evidenziate](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. Creare un grafico a linee per visualizzare la temperatura in tempo reale nel tempo.

   * Nella pagina della creazione dei report aggiungere un grafico a linee selezionando l'icona del grafico a linee.

     ![Le visualizzazioni e i campi](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * Nel riquadro **Campi** espandere la tabella specificata durante la creazione di output per il processo di analisi di flusso. Questa esercitazione usa **contosotable**.

   * Trascinare **EventEnqueuedUtcTime** in **Asse** sul riquadro **Visualizzazioni**.

   * Trascinare la **temperatura** in **Valori**.

   Viene creato un grafico a linee. L'asse x mostra data e ora per il fuso orario UTC. L'asse y mostra la temperatura dal sensore.

6. Creare un altro grafico a linee in modo da visualizzare in tempo reale l'umidità nel tempo. Per configurare il secondo grafico, attenersi alla stessa procedura precedente e inserire **EventEnqueuedUtcTime** sull'asse x e l'**umidità** sull'asse y.

   ![Il report finale di Power BI con i due grafici](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. Selezionare **Salva** per salvare il report.

Dovrebbe essere possibile visualizzare i dati su entrambi i grafici. Questo risultato implica che le affermazioni seguenti sono vere:

   * Il routing all'account dell'endpoint predefinito funziona correttamente.
   * Il processo di Analisi di flusso di Azure è trasmesso correttamente.
   * La visualizzazione di Power BI è configurata correttamente.

È possibile aggiornare i grafici per visualizzare i dati più recenti, selezionando il pulsante Aggiorna nella parte superiore della finestra di Power BI. 

## <a name="clean-up-resources"></a>Pulire le risorse 

Per rimuovere tutte le risorse create nelle due parti di questa esercitazione, eliminare il gruppo di risorse. Questa azione elimina tutte le risorse contenute all'interno del gruppo. In questo caso, rimuove l'hub IoT, lo spazio dei nomi del Bus di servizio e la coda, l'App per la logica, l'account di archiviazione e il gruppo di risorse. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Pulire le risorse nella visualizzazione di Power BI

Accedere all'account [Power BI](https://powerbi.microsoft.com/). Passa all'area di lavoro. In questa esercitazione viene usato **Area di lavoro personale**. Per rimuovere la visualizzazione di Power BI, passare al Set di dati e selezionare l'icona del Cestino per eliminare il set di dati. Questa esercitazione usa **contosodataset**. Quando si rimuove il set di dati, viene rimosso anche il report.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Usare l'interfaccia della riga di comando di Azure per pulire le risorse

Per rimuovere il gruppo di risorse, usare il comando [eliminazione del gruppo az](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup` è stato impostato di nuovo su **ContosoResources** all'inizio di questa esercitazione.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>Usare PowerShell per pulire le risorse

Per rimuovere il gruppo di risorse, usare il comando [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup). `$resourceGroup` è stato impostato di nuovo su **ContosoResources** all'inizio di questa esercitazione.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Nella parte 2 di questa esercitazione è stato descritto come usare il routing dei messaggi per instradare i messaggi dell'hub IoT in destinazioni diverse eseguendo le attività seguenti.  

**Parte I: Creare risorse e configurare il routing dei messaggi**
> [!div class="checklist"]
> * Creare le risorse, ossia un hub IoT, un account di archiviazione, una coda del bus di servizio e un dispositivo simulato.
> * Configurare gli endpoint e le route dei messaggi nell'hub IoT per l'account di archiviazione e la coda del bus di servizio.

**Parte II: Inviare messaggi all'hub e visualizzare i risultati instradati**
> [!div class="checklist"]
> * Creare un'app per la logica che viene attivata e che invia e-mail quando viene aggiunto un messaggio alla coda del Bus di servizio.
> * Scaricare ed eseguire un'app che simuli un dispositivo IoT che invia messaggi all'hub per le diverse opzioni di routing.
> * Creare una visualizzazione di Power BI per i dati inviati all'endpoint predefinito.
> * Visualizzare i risultati...
> * ...nella Coda del bus di servizio e nei messaggi di posta elettronica.
> * ...nell'account di archiviazione.
> * ... nella visualizzazione di Power BI.

Passare all'esercitazione successiva per imparare a gestire lo stato di un dispositivo hub IoT. 

> [!div class="nextstepaction"]
> [Configurare e usare le metriche e la diagnostica con un hub IoT](tutorial-use-metrics-and-diags.md)
