---
title: 'Inviare i dati di Hub eventi di Azure al data warehouse: Griglia di eventi'
description: Descrive come usare Griglia di eventi e Hub eventi di Azure per eseguire la migrazione dei dati in un SQL Data Warehouse. Usa una Funzione di Azure per recuperare un file di Acquisizione.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: tutorial
ms.date: 01/13/2019
ms.author: spelluru
ms.openlocfilehash: c2c49563bf505ce70c4900c6c0a8e41c0f6ac9c5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176617"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Esercitazione: Trasmettere Big Data a un data warehouse
[Griglia di eventi](overview.md) di Azure è un servizio intelligente di routing di eventi che consente di rispondere alle notifiche (eventi) inviate da applicazioni e servizi. Per esempio, può attivare una Funzione di Azure per elaborare i dati di Hub eventi che sono stati acquisiti in un archivio BLOB di Azure o Azure Data Lake Store ed eseguire la migrazione dei dati su altri repository di dati. Questo [esempio di integrazione di Hub eventi e di Griglia di eventi](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) mostra come usare Hub eventi con Griglia di eventi per migrare facilmente i dati di Hub eventi acquisiti da una risorsa di archiviazione BLOB a un SQL Data Warehouse.

![Panoramica dell'applicazione](media/event-grid-event-hubs-integration/overview.png)

Questo diagramma illustra il flusso di lavoro della soluzione compilata in questa esercitazione: 

1. I dati inviati a un hub eventi di Azure vengono acquisiti in una risorsa di archiviazione BLOB di Azure.
2. Una volta completata l'acquisizione dei dati, viene generato un evento e viene inviato a una griglia di eventi di Azure. 
3. La griglia di eventi inoltra i dati di tale evento a un'app per le funzioni di Azure.
4. L'app per le funzioni usa l'URL del BLOB nei dati dell'evento per recuperare il BLOB dalla risorsa di archiviazione. 
5. L'app per le funzioni esegue la migrazione dei dati del BLOB in un data warehouse SQL di Azure. 

In questo articolo, si eseguono i passaggi seguenti:

> [!div class="checklist"]
> * Usare un modello di Azure Resource Manager per implementare l'infrastruttura: un hub eventi, un account di archiviazione, un'app per le funzioni, un SQL data warehouse.
> * Creare una tabella nel data warehouse.
> * Aggiungere codice all'app per le funzioni.
> * Abbonarsi all'evento. 
> * Eseguire un'app che invia i dati all'hub eventi.
> * Visualizzare i dati di cui è stata eseguita la migrazione nel data warehouse.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per completare l'esercitazione, sono necessari:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Visual Studio 2017 versione 15.3.2 o successiva](https://www.visualstudio.com/vs/) con carichi di lavoro per lo sviluppo di desktop .NET, di Azure, Web e ASP.NET, di Node.js e di Python.
* Scaricare il [progetto di esempio EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) sul computer.

## <a name="deploy-the-infrastructure"></a>Distribuire l'infrastruttura
In questo passaggio, implementare l'infrastruttura necessaria con un [modello di Resource Manager](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Quando si implementa il modello, vengono create le risorse seguenti:

* Hub eventi con la funzionalità Acquisizione abilitata.
* Account di archiviazione per i file acquisiti. 
* Piano di servizio app per l'hosting dell'app per le funzioni
* App per le funzioni per l'elaborazione dell'evento
* SQL Server per l'hosting del data warehouse
* SQL Data Warehouse per l'archiviazione dei dati migrati

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>Avviare Azure Cloud Shell nel portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Selezionare il pulsante **Cloud Shell** nella parte superiore.

    ![Portale di Azure](media/event-grid-event-hubs-integration/azure-portal.png)
3. Si noterà che Cloud Shell è aperto nella parte inferiore del browser.

    ![Cloud Shell](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. In Cloud Shell, se viene visualizzata un'opzione per scegliere tra **Bash** e **PowerShell**, selezionare **Bash**. 
5. Se si usa Cloud Shell per la prima volta, creare un account di archiviazione selezionando **Crea risorsa di archiviazione**. Azure Cloud Shell richiede un account di archiviazione di Azure per archiviare alcuni file. 

    ![Creare una risorsa di archiviazione per la shell cloud](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. Attendere finché non viene inizializzata Cloud Shell. 

    ![Creare una risorsa di archiviazione per la shell cloud](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

1. Creare un gruppo di risorse di Azure eseguendo il comando CLI seguente: 
    1. Copiare e incollare il comando seguente nella finestra di Cloud Shell

        ```azurecli
        az group create -l eastus -n <Name for the resource group>
        ```
    1. Specificare un nome per il **gruppo di risorse**
    2. Premere **INVIO**. 

        Di seguito è fornito un esempio: 
    
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
2. Distribuire tutte le risorse menzionate nella sezione precedente (hub eventi, account di archiviazione, app per le funzioni, data warehouse di SQL) eseguendo il comando CLI seguente: 
    1. Copiare e incollare il comando nella finestra di Cloud Shell. In alternativa, è possibile copiare/incollare in un editor di propria scelta, impostare i valori, quindi copiare il comando in Cloud Shell. 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigrationSample \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. Specificare i valori per le entità seguenti:
        1. Nome del gruppo di risorse creato in precedenza.
        2. Nome dello spazio dei nomi dell'hub eventi. 
        3. Nome dell'hub eventi. È possibile lasciare il valore così com'è (hubdatamigration).
        4. Nome del server SQL.
        5. Nome dell'utente e della password SQL. 
        6. Nome del data warehouse SQL
        7. Nome dell'account di archiviazione. 
        8. Nome dell’app per le funzioni. 
    3.  Premere **INVIO** nella finestra di Cloud Shell per eseguire il comando. Questo processo potrebbe richiedere qualche minuto, poiché si sta creando una serie di risorse. Nel risultato del comando, assicurarsi che non si siano verificati errori. 
    

### <a name="use-azure-powershell"></a>Usare Azure PowerShell

1. In Azure Cloud Shell, passare alla modalità PowerShell. Selezionare la freccia Giù nell'angolo superiore sinistro di Azure Cloud Shell e selezionare **PowerShell**.

    ![Passare a PowerShell](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. Creare un gruppo di risorse di Azure eseguendo il comando seguente: 
    1. Copiare e incollare il comando seguente nella finestra di Cloud Shell.

        ```powershell
        New-AzResourceGroup -Name rgDataMigration -Location westcentralus
        ```
    2. Specificare un nome per il **gruppo di risorse**.
    3. Premere INVIO. 
3. Distribuire tutte le risorse menzionate nella sezione precedente (hub eventi, account di archiviazione, app per le funzioni, data warehouse di SQL) eseguendo il comando seguente:
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
        6. Nome del data warehouse SQL
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

### <a name="create-a-table-in-sql-data-warehouse"></a>Creare una tabella in un SQL Data Warehouse
Creare una tabella nel data warehouse mediante l'esecuzione dello script [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql). Per eseguire lo script, è possibile usare Visual Studio o l'Editor di query nel portale. I passaggi seguenti mostrano come usare l'Editor di query: 

1. Nell'elenco di risorse nel gruppo di risorse, selezionare il data warehouse SQL. 
2. Nella pagina del data warehouse SQL, selezionare **Editor di query (anteprima)** nel menu a sinistra. 

    ![Pagina del data warehouse di SQL](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. Immettere il nome dell'**utente** e la **password** per il server SQL e selezionare **OK**. 

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


## <a name="publish-the-azure-functions-app"></a>Pubblicare l'app Funzioni di Azure

1. Avviare Visual Studio 2017. 
2. Aprire la soluzione **EventHubsCaptureEventGridDemo.sln** scaricata dal [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) come parte dei prerequisiti.
3. In Esplora soluzioni fare clic con il pulsante destro del mouse su **FunctionDWDumper** e selezionare **Pubblica**.

   ![Pubblicare l'app per le funzioni](media/event-grid-event-hubs-integration/publish-function-app.png)
4. Se si visualizza la schermata seguente, selezionare **Avvia**. 

   ![Avviare il pulsante di pubblicazione](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. Nella pagina **Seleziona una destinazione di pubblicazione**, selezionare l'opzione **Seleziona esistente**, quindi selezionare **Crea profilo**. 

   ![Selezionare una destinazione di pubblicazione](media/event-grid-event-hubs-integration/publish-select-existing.png)
6. Nella pagina del servizio app, selezionare **sottoscrizione di Azure**, selezionare la **app per le funzioni** nel gruppo di risorse e selezionare **OK**. 

   ![Pagina di servizio app](media/event-grid-event-hubs-integration/publish-app-service.png) 
1. Quando il profilo è stato configurato automaticamente in Visual Studio, selezionare **Pubblica**.

   ![Select publish](media/event-grid-event-hubs-integration/select-publish.png)

Dopo aver pubblicato la funzione, si è pronti per sottoscrivere l'evento.

## <a name="subscribe-to-the-event"></a>Sottoscrivere l'evento

1. In una nuova scheda o in una nuova finestra di un Web browser, passare al [portale di Azure](https://portal.azure.com).
2. Nel portale di Azure, selezionare **Gruppi di risorse** nel menu a sinistra. 
3. Filtrare l'elenco dei gruppi di risorse immettendo il nome del gruppo di risorse nella casella di ricerca. 
4. Selezionare il gruppo di risorse nell'elenco.

    ![Selezionare un gruppo di risorse](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Selezionare il piano di servizio app nell'elenco. 
5. Nella pagina del piano di servizio app, selezionare **App** nel menu a sinistra e selezionare l'app per le funzioni. 

    ![Selezionare l'app per le funzioni](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. Espandere l'app per le funzioni, espandere le funzioni, quindi selezionare la funzione. 

    ![Selezionare la funzione di Azure](media/event-grid-event-hubs-integration/select-function-add-button.png)
7. Selezionare **Aggiungi sottoscrizione a Griglia di eventi** sulla barra degli strumenti. 
8. Nella pagina **Creare sottoscrizione a Griglia di eventi**, eseguire le azioni seguenti: 
    1. Nella sezione **DETTAGLI ARGOMENTO**, eseguire le azioni seguenti:
        1. Selezionare la sottoscrizione di Azure.
        2. Selezionare il gruppo di risorse di Azure.
        3. Selezionare lo spazio dei nomi di Hub eventi.
    2. Nella pagina **DETTAGLI SOTTOSCRIZIONE EVENTI**, immettere un nome per la sottoscrizione (per esempio: captureEventSub) e selezionare **Crea**. 

        ![Creare la sottoscrizione a Griglia di eventi](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>Eseguire l'app per generare i dati
È stata completata la configurazione dell'hub eventi, di SQL Data Warehouse, dell'app per le funzioni di Azure e della sottoscrizione di eventi. Prima di eseguire un'applicazione che genera i dati per l'hub eventi, è necessario configurare alcuni valori.

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
