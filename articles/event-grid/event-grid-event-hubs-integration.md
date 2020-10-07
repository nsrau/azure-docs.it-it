---
title: 'Esercitazione: Inviare i dati di Hub eventi di Azure al data warehouse: Griglia di eventi'
description: 'Esercitazione: Informazioni su come usare Griglia di eventi di Azure e Hub eventi per eseguire la migrazione dei dati ad Azure Synapse Analytics. Usa una Funzione di Azure per recuperare un file di Acquisizione.'
ms.topic: tutorial
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 64d4b9769e1a228294bd7d8741f6f4b1260fb0dd
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91270560"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Esercitazione: Trasmettere Big Data a un data warehouse
[Griglia di eventi](overview.md) di Azure è un servizio intelligente di routing di eventi che consente di rispondere alle notifiche (eventi) inviate da applicazioni e servizi. Per esempio, può attivare una Funzione di Azure per elaborare i dati di Hub eventi che sono stati acquisiti in un archivio BLOB di Azure o Azure Data Lake Store ed eseguire la migrazione dei dati su altri repository di dati. Questo [esempio di integrazione di Hub eventi e di Griglia di eventi](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) mostra come usare Hub eventi con Griglia di eventi per migrare facilmente i dati di Hub eventi acquisiti da una risorsa di archiviazione BLOB a un'istanza di Azure Synapse Analytics (in precedenza SQL Data Warehouse).

![Panoramica dell'applicazione](media/event-grid-event-hubs-integration/overview.png)

Questo diagramma illustra il flusso di lavoro della soluzione compilata in questa esercitazione: 

1. I dati inviati a un hub eventi di Azure vengono acquisiti in una risorsa di archiviazione BLOB di Azure.
2. Una volta completata l'acquisizione dei dati, viene generato un evento e viene inviato a una griglia di eventi di Azure. 
3. La griglia di eventi inoltra i dati di tale evento a un'app per le funzioni di Azure.
4. L'app per le funzioni usa l'URL del BLOB nei dati dell'evento per recuperare il BLOB dalla risorsa di archiviazione. 
5. L'app per le funzioni esegue la migrazione dei dati del BLOB a un'istanza di Azure Synapse Analytics. 

In questo articolo, si eseguono i passaggi seguenti:

> [!div class="checklist"]
> * Usare un modello di Azure Resource Manager per distribuire l'infrastruttura, ovvero un hub eventi, un account di archiviazione, un'app per le funzioni e un'istanza di Synapse Analytics.
> * Creare una tabella nel data warehouse.
> * Aggiungere codice all'app per le funzioni.
> * Abbonarsi all'evento. 
> * Eseguire un'app che invia i dati all'hub eventi.
> * Visualizzare i dati di cui è stata eseguita la migrazione nel data warehouse.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per completare l'esercitazione, sono necessari:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Visual Studio 2019](https://www.visualstudio.com/vs/) con carichi di lavoro per lo sviluppo di desktop .NET, di Azure, Web e ASP.NET, di Node.js e di Python.
* Scaricare il [progetto di esempio EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) sul computer.

## <a name="deploy-the-infrastructure"></a>Distribuire l'infrastruttura
In questo passaggio, implementare l'infrastruttura necessaria con un [modello di Resource Manager](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Quando si implementa il modello, vengono create le risorse seguenti:

* Hub eventi con la funzionalità Acquisizione abilitata.
* Account di archiviazione per i file acquisiti. 
* Piano di servizio app per l'hosting dell'app per le funzioni
* App per le funzioni per l'elaborazione dell'evento
* SQL Server per l'hosting del data warehouse
* Azure Synapse Analytics per l'archiviazione dei dati trasferiti

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>Avviare Azure Cloud Shell nel portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Selezionare il pulsante **Cloud Shell** nella parte superiore.

    ![Portale di Azure](media/event-grid-event-hubs-integration/azure-portal.png)
3. Si noterà che Cloud Shell è aperto nella parte inferiore del browser.

    ![Cloud Shell](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. In Cloud Shell, se viene visualizzata un'opzione per scegliere tra **Bash** e **PowerShell**, selezionare **Bash**. 
5. Se si usa Cloud Shell per la prima volta, creare un account di archiviazione selezionando **Crea risorsa di archiviazione**. Azure Cloud Shell richiede un account di archiviazione di Azure per archiviare alcuni file. 

    ![Screenshot che mostra la finestra di dialogo "Non sono state montate risorse di archiviazione" con il pulsante "Crea risorsa di archiviazione" selezionato.](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. Attendere finché non viene inizializzata Cloud Shell. 

    ![Creare una risorsa di archiviazione per la shell cloud](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

1. Creare un gruppo di risorse di Azure eseguendo il comando CLI seguente: 
    1. Copiare e incollare il comando seguente nella finestra di Cloud Shell. Se si desidera, modificare il nome del gruppo di risorse e la località.

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. Premere **INVIO**. 

        Esempio:
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n ehubegridgrp
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/ehubegridgrp",
          "location": "eastus",
          "managedBy": null,
          "name": "ehubegridgrp",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. Distribuire tutte le risorse menzionate nella sezione precedente (hub eventi, account di archiviazione, app per le funzioni, Azure Synapse Analytics) eseguendo il comando dell'interfaccia della riga di comando seguente: 
    1. Copiare e incollare il comando nella finestra di Cloud Shell. In alternativa, è possibile copiare/incollare in un editor di propria scelta, impostare i valori, quindi copiare il comando in Cloud Shell. 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. Specificare i valori per le entità seguenti:
        1. Nome del gruppo di risorse creato in precedenza.
        2. Nome dello spazio dei nomi dell'hub eventi. 
        3. Nome dell'hub eventi. È possibile lasciare il valore così com'è (hubdatamigration).
        4. Nome del server SQL.
        5. Nome dell'utente e della password SQL. 
        6. Nome dell'istanza di Azure Synapse Analytics
        7. Nome dell'account di archiviazione. 
        8. Nome dell’app per le funzioni. 
    3.  Premere **INVIO** nella finestra di Cloud Shell per eseguire il comando. Questo processo potrebbe richiedere qualche minuto, poiché si sta creando una serie di risorse. Nel risultato del comando, assicurarsi che non si siano verificati errori. 
    

### <a name="use-azure-powershell"></a>Usare Azure PowerShell

1. In Azure Cloud Shell, passare alla modalità PowerShell. Selezionare la freccia Giù nell'angolo superiore sinistro di Azure Cloud Shell e selezionare **PowerShell**.

    ![Passare a PowerShell](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. Creare un gruppo di risorse di Azure eseguendo il comando seguente: 
    1. Copiare e incollare il comando seguente nella finestra di Cloud Shell.

        ```powershell
        New-AzResourceGroup -Name rgDataMigration -Location eastus
        ```
    2. Specificare un nome per il **gruppo di risorse**.
    3. Premere INVIO. 
3. Distribuire tutte le risorse menzionate nella sezione precedente (hub eventi, account di archiviazione, app per le funzioni, Azure Synapse Analytics) eseguendo il comando seguente:
    1. Copiare e incollare il comando nella finestra di Cloud Shell. In alternativa, è possibile copiare/incollare in un editor di propria scelta, impostare i valori, quindi copiare il comando in Cloud Shell. 

        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
        ```
    2. Specificare i valori per le entità seguenti:
        1. Nome del gruppo di risorse creato in precedenza.
        2. Nome dello spazio dei nomi dell'hub eventi. 
        3. Nome dell'hub eventi. È possibile lasciare il valore così com'è (hubdatamigration).
        4. Nome del server SQL.
        5. Nome dell'utente e della password SQL. 
        6. Nome dell'istanza di Azure Synapse Analytics
        7. Nome dell'account di archiviazione. 
        8. Nome dell’app per le funzioni. 
    3.  Premere **INVIO** nella finestra di Cloud Shell per eseguire il comando. Questo processo potrebbe richiedere qualche minuto, poiché si sta creando una serie di risorse. Nel risultato del comando, assicurarsi che non si siano verificati errori. 

### <a name="close-the-cloud-shell"></a>Chiudere Cloud Shell 
Chiudere la shell cloud selezionando il pulsante **Cloud Shell** nel portale (oppure) il pulsante **X** nell'angolo superiore destro della finestra di Cloud Shell. 

### <a name="verify-that-the-resources-are-created"></a>Verificare che siano state create le risorse

1. Nel portale di Azure, selezionare **Gruppi di risorse** nel menu a sinistra. 
2. Filtrare l'elenco dei gruppi di risorse immettendo il nome del gruppo di risorse nella casella di ricerca. 
3. Selezionare il gruppo di risorse nell'elenco.

    ![Selezionare un gruppo di risorse](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Confermare che le risorse seguenti sono visibili nel gruppo di risorse:

    ![Risorse nel gruppo di risorse](media/event-grid-event-hubs-integration/resources-in-resource-group.png)

### <a name="create-a-table-in-azure-synapse-analytics"></a>Creare una tabella in Azure Synapse Analytics
Creare una tabella nel data warehouse mediante l'esecuzione dello script [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql). Per eseguire lo script, è possibile usare Visual Studio o l'Editor di query nel portale. I passaggi seguenti mostrano come usare l'Editor di query: 

1. Nell'elenco di risorse nel gruppo di risorse, selezionare il **Pool Synapse SQL (data warehouse)** . 
2. Nella pagina Azure Synapse Analytics selezionare **Editor di query (anteprima)** nel menu sinistro. 

    ![Pagina Azure Synapse Analytics](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. Immettere il nome dell'**utente** e la **password** per il server SQL e selezionare **OK**. Per accedere a SQL Server, può essere necessario aggiungere l'indirizzo IP del client al firewall. 

    ![Autenticazione del server di SQL](media/event-grid-event-hubs-integration/sql-server-authentication.png)
4. Nella finestra di query, copiare ed eseguire lo script SQL seguente: 

    ```sql
    CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
        [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
        [MeasureTime] datetime NULL, 
        [GeneratedPower] float NULL, 
        [WindSpeed] float NULL, 
        [TurbineSpeed] float NULL
    )
    WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    ```

    ![Eseguire query SQL](media/event-grid-event-hubs-integration/run-sql-query.png)
5. Mantenere questa scheda o finestra aperta in modo che sia possibile verificare che i dati vengono creati al termine dell'esercitazione. 

### <a name="update-the-function-runtime-version"></a>Aggiornare la versione del runtime della funzione

1. Nel portale di Azure, selezionare **Gruppi di risorse** nel menu a sinistra.
2. Selezionare il gruppo di risorse in cui è presente l'app per le funzioni. 
3. Selezionare l'app per le funzioni di tipo **servizio app** nell'elenco delle risorse nel gruppo di risorse.
4. Selezionare **Configurazione** in **Impostazioni** nel menu a sinistra. 
5. Passare alla scheda **Impostazioni di runtime della funzione** nel riquadro a destra. 
5. Aggiornare **Versione runtime** a **~3**. 

    ![Aggiornare la versione del runtime della funzione](media/event-grid-event-hubs-integration/function-runtime-version.png)
    

## <a name="publish-the-azure-functions-app"></a>Pubblicare l'app Funzioni di Azure

1. Avviare Visual Studio.
2. Aprire la soluzione **EventHubsCaptureEventGridDemo.sln** scaricata dal [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) come parte dei prerequisiti.
3. In Esplora soluzioni fare clic con il pulsante destro del mouse su **FunctionDWDumper** e selezionare **Pubblica**.

   ![Pubblicare l'app per le funzioni](media/event-grid-event-hubs-integration/publish-function-app.png)
4. Se si visualizza la schermata seguente, selezionare **Avvia**. 

   ![Screenshot che mostra Visual Studio con il pulsante "Avvia" nella sezione Pubblica.](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. Nella finestra di dialogo **Pubblica** selezionare **Azure** per **Destinazione**, quindi fare clic su **Avanti**. 

   ![Avviare il pulsante di pubblicazione](media/event-grid-event-hubs-integration/publish-select-azure.png)
6. Selezionare **App per le funzioni di Azure (Windows)** e quindi **Avanti**. 

   ![Selezionare App per le funzioni di Azure (Windows)](media/event-grid-event-hubs-integration/select-azure-function-windows.png)
7. Nella scheda **Istanza di Funzioni** selezionare la sottoscrizione di Azure, espandere il gruppo di risorse, selezionare l'app per le funzioni e quindi selezionare **Fine**. È necessario accedere al proprio account Azure, se non si è già connessi. 

   ![Selezionare l'app per le funzioni desiderata](media/event-grid-event-hubs-integration/publish-select-function-app.png)
8. Nella sezione **Dipendenze del servizio** selezionare **Configura**.
9. Nella pagina **Configura la dipendenza** selezionare l'account di archiviazione creato in precedenza e quindi selezionare **Avanti**. 
10. Mantenere le impostazioni per il nome e il valore della stringa di connessione e selezionare **Avanti**.
11. Deselezionare l'opzione **Archivio segreti** e quindi selezionare **Fine**.  
8. Quando il profilo è stato configurato automaticamente in Visual Studio, selezionare **Pubblica**.

   ![Select publish](media/event-grid-event-hubs-integration/select-publish.png)

Dopo aver pubblicato la funzione, si è pronti per sottoscrivere l'evento.

## <a name="subscribe-to-the-event"></a>Sottoscrivere l'evento

1. In una nuova scheda o in una nuova finestra di un Web browser, passare al [portale di Azure](https://portal.azure.com).
2. Nel portale di Azure, selezionare **Gruppi di risorse** nel menu a sinistra. 
3. Filtrare l'elenco dei gruppi di risorse immettendo il nome del gruppo di risorse nella casella di ricerca. 
4. Selezionare il gruppo di risorse nell'elenco.

    ![Selezionare un gruppo di risorse](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Selezionare il piano di servizio app (non il servizio app) nell'elenco delle risorse nel gruppo di risorse. 
5. Nella pagina del piano di servizio app, selezionare **App** nel menu a sinistra e selezionare l'app per le funzioni. 

    ![Selezionare l'app per le funzioni](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. Espandere l'app per le funzioni, espandere le funzioni, quindi selezionare la funzione. 
7. Selezionare **Aggiungi sottoscrizione a Griglia di eventi** sulla barra degli strumenti. 

    ![Selezionare la funzione di Azure](media/event-grid-event-hubs-integration/select-function-add-button.png)
8. Nella pagina **Creare sottoscrizione a Griglia di eventi**, eseguire le azioni seguenti: 
    1. Nella pagina **DETTAGLI SOTTOSCRIZIONE EVENTI**, immettere un nome per la sottoscrizione (per esempio: captureEventSub) e selezionare **Crea**. 
    2. Nella sezione **DETTAGLI ARGOMENTO**, eseguire le azioni seguenti:
        1. Selezionare **Spazi dei nomi di Hub eventi** per **Tipi di argomento**. 
        2. Selezionare la sottoscrizione di Azure.
        2. Selezionare il gruppo di risorse di Azure.
        3. Selezionare lo spazio dei nomi di Hub eventi.
    3. Nella sezione **TIPI DI EVENTO** verificare che per **Filtra per tipi di evento** sia selezionato il **file di acquisizione creato**. 
    4. Nella sezione **DETTAGLI ENDPOINT** verificare che l'opzione **Tipo di endpoint** sia impostata su **Funzione di Azure** e che l'opzione **Endpoint** sia impostata sulla funzione di Azure. 
    
        ![Creare la sottoscrizione a Griglia di eventi](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>Eseguire l'app per generare i dati
È stata completata la configurazione dell'hub eventi, dell'istanza di Azure Synapse Analytics, dell'app per le funzioni di Azure e della sottoscrizione di eventi. Prima di eseguire un'applicazione che genera i dati per l'hub eventi, è necessario configurare alcuni valori.

1. Nel portale di Azure, passare al gruppo di risorse come in precedenza. 
2. Selezionare lo spazio dei nomi di Hub eventi.
3. Nella pagina **Spazio dei nomi di Hub eventi**, selezionare **Criteri di accesso condivisi** nel menu a sinistra.
4. Selezionare **RootManageSharedAccessKey** nell'elenco dei criteri. 
5. Selezionare il pulsante di copia accanto alla casella di testo **Chiave primaria della stringa di connessione**. 

    ![Stringa di connessione dello spazio dei nomi dell'hub eventi](media/event-grid-event-hubs-integration/get-connection-string.png)
1. Tornare alla soluzione Visual Studio. 
2. Nel progetto WindTurbineDataGenerator aprire **program.cs**.
5. Sostituire i due valori costanti. Usare il valore copiato per **EventHubConnectionString**. Usare **hubdatamigration** come nome dell'hub eventi. Se è stato usato un nome diverso per l'hub eventi, specificare tale nome. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Compilare la soluzione. Eseguire l'applicazione **WindTurbineGenerator.exe**. 
7. Dopo pochi minuti eseguire una query sulla tabella nel data warehouse per i dati migrati.

    ![Risultati query](media/event-grid-event-hubs-integration/query-results.png)

### <a name="event-data-generated-by-the-event-hub"></a>Dati di evento generati dall'hub eventi
Griglia di eventi distribuisce i dati dell'evento ai sottoscrittori. L'esempio seguente mostra i dati di evento generati quando i dati trasmessi attraverso un hub eventi vengono acquisiti in un BLOB. In particolare, si noti la `fileUrl` proprietà nei `data` punti oggetto nel BLOB nella risorsa di archiviazione. L'app per le funzioni usa questo URL per recuperare il file BLOB con i dati acquisiti.

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```


## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle differenze tra i servizi di messaggistica di Azure, vedere [Scegliere tra i servizi di Azure che recapitano messaggi](compare-messaging-services.md).
* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
* Per informazioni introduttive alla funzionalità di acquisizione di Hub eventi, vedere [Abilitare Acquisizione di Hub eventi usando il portale di Azure](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Per altre informazioni sulla configurazione e l'esecuzione dell'esempio, vedere l'[esempio della funzionalità di acquisizione di Hub eventi e della Griglia di eventi](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
