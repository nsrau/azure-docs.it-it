---
title: Integrazione di Griglia di eventi di Azure e Hub eventi
description: Descrive come usare Griglia di eventi di Azure e Hub eventi per la migrazione dei dati in un SQL Data Warehouse
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: dba17a860dffd87b3784c53cf288b7a312c77e33
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="stream-big-data-into-a-data-warehouse"></a>Trasmettere Big Data a un data warehouse

[Griglia di eventi](overview.md) di Azure è un servizio intelligente di routing di eventi che consente di rispondere alle notifiche inviate da app e servizi. L'[esempio della funzionalità di acquisizione di Hub eventi e della Griglia di eventi](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) descrive come usare la funzionalità di acquisizione di Hub eventi di Azure con Griglia di eventi di Azure per migrare facilmente i dati da un hub eventi a un SQL Data Warehouse.

![Panoramica dell'applicazione](media/event-grid-event-hubs-integration/overview.png)

Quando i dati vengono inviati all'hub eventi, la funzionalità di acquisizione esegue il pull dei dati dal flusso e genera BLOB di archiviazione con i dati in formato Avro. Quando la funzionalità di acquisizione genera il BLOB, attiva un evento. Griglia di eventi distribuisce i dati dell'evento ai sottoscrittori. In questo caso, i dati dell'evento vengono inviati all'endpoint di Funzioni di Azure. I dati dell'evento includono il percorso del BLOB generato. La funzione usa questo URL per recuperare il file e lo invia al data warehouse.

In questo articolo si apprenderà come:

* Distribuire l'infrastruttura seguente:
  * Hub eventi con la funzionalità di acquisizione abilitata
  * Account di archiviazione per i file dalla funzionalità di acquisizione
  * Piano di servizio app di Azure per l'hosting di app per le funzioni
  * App per le funzioni per l'elaborazione dell'evento
  * SQL Server per l'hosting del data warehouse
  * SQL Data Warehouse per l'archiviazione dei dati migrati
* Creare una tabella nel data warehouse
* Aggiungere codice all'app per le funzioni
* Sottoscrivere l'evento
* Eseguire app che invia i dati all'hub eventi
* Visualizzare i dati migrati nel data warehouse

## <a name="about-the-event-data"></a>Informazioni sui dati dell'evento

Griglia di eventi distribuisce i dati dell'evento ai sottoscrittori. L'esempio seguente mostra i dati dell'evento per la creazione di un file di acquisizione. In particolare si noti la proprietà `fileUrl` nell'oggetto `data`. L'app per le funzioni ottiene questo valore e lo usa per recuperare il file di acquisizione.

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

## <a name="prerequisites"></a>prerequisiti

Per completare l'esercitazione, sono necessari:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
* [Visual Studio 2017 versione 15.3.2 o successiva](https://www.visualstudio.com/vs/) con carichi di lavoro per lo sviluppo di desktop .NET, di Azure, Web e ASP.NET, di Node.js e di Python.
* Il [progetto di esempio EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) scaricato sul computer.

## <a name="deploy-the-infrastructure"></a>Distribuire l'infrastruttura

Per semplificare questo articolo, distribuire l'infrastruttura necessaria con un modello di Resource Manager. Per esaminare le risorse che vengono distribuite, visualizzare il [modello](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Usare una delle [aree supportate](overview.md) per la località del gruppo di risorse.

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
az group create -l westcentralus -n rgDataMigrationSample

az group deployment create \
  --resource-group rgDataMigrationSample \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

Per PowerShell, usare:

```powershell
New-AzureRmResourceGroup -Name rgDataMigration -Location westcentralus

New-AzureRmResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```

Fornire un valore di password quando viene richiesto.

## <a name="create-a-table-in-sql-data-warehouse"></a>Creare una tabella in un SQL Data Warehouse

Aggiungere una tabella al data warehouse mediante l'esecuzione dello script [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql). Per eseguire lo script, usare Visual Studio o l'Editor di query nel portale.

Lo script da eseguire è:

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

## <a name="publish-the-azure-functions-app"></a>Pubblicare l'app Funzioni di Azure

1. Aprire il [progetto di esempio EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) in Visual Studio 2017 15.3.2 o versione successiva.

2. In Esplora soluzioni fare clic con il pulsante destro del mouse su **FunctionDWDumper** e selezionare **Pubblica**.

   ![Pubblicare l'app per le funzioni](media/event-grid-event-hubs-integration/publish-function-app.png)

3. Selezionare **App per le funzioni di Azure** e **Seleziona esistente**. Selezionare **OK**.

   ![App per le funzioni di destinazione](media/event-grid-event-hubs-integration/pick-target.png)

4. Selezionare l'app per le funzioni che è stata distribuita tramite il modello. Selezionare **OK**.

   ![Selezionare l'app per le funzioni](media/event-grid-event-hubs-integration/select-function-app.png)

5. Quando il profilo è stato configurato automaticamente in Visual Studio, selezionare **Pubblica**.

   ![Select publish](media/event-grid-event-hubs-integration/select-publish.png)

6. Dopo la pubblicazione della funzione accedere al [portale di Azure](https://portal.azure.com/). Selezionare il gruppo di risorse e l'app per le funzioni.

   ![Visualizzare l'app per le funzioni](media/event-grid-event-hubs-integration/view-function-app.png)

7. Selezionare la funzione.

   ![Selezionare la funzione](media/event-grid-event-hubs-integration/select-function.png)

8. Ottenere l'URL per la funzione. Questo URL è necessario quando si crea la sottoscrizione di eventi.

   ![Ottenere l'URL della funzione](media/event-grid-event-hubs-integration/get-function-url.png)

9. Copiare il valore.

   ![Copia l'URL](media/event-grid-event-hubs-integration/copy-url.png)

## <a name="subscribe-to-the-event"></a>Sottoscrivere l'evento

È possibile usare l'interfaccia della riga di comando di Azure o il portale per sottoscrivere l'evento. Questo articolo illustra entrambi gli approcci.

### <a name="portal"></a>Portale

1. Dallo spazio dei nomi di Hub eventi selezionare **Griglia di eventi** a sinistra.

   ![Selezionare Griglia di eventi](media/event-grid-event-hubs-integration/select-event-grid.png)

2. Aggiungere una sottoscrizione di eventi.

   ![Aggiungere una sottoscrizione di eventi](media/event-grid-event-hubs-integration/add-event-subscription.png)

3. Fornire i valori per la sottoscrizione di eventi. Usare l'URL di Funzioni di Azure che è stato copiato. Selezionare **Create**.

   ![Fornire i valori della sottoscrizione](media/event-grid-event-hubs-integration/provide-values.png)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per sottoscrivere l'evento, eseguire i comandi seguenti (che richiedono la versione 2.0.24 o successiva dell'interfaccia della riga di comando di Azure):

```azurecli-interactive
namespaceid=$(az resource show --namespace Microsoft.EventHub --resource-type namespaces --name <your-EventHubs-namespace> --resource-group rgDataMigrationSample --query id --output tsv)
az eventgrid event-subscription create \
  --resource-id $namespaceid \
  --name captureEventSub \
  --endpoint <your-function-endpoint>
```

## <a name="run-the-app-to-generate-data"></a>Eseguire l'app per generare i dati

È stata completata la configurazione dell'hub eventi, di SQL Data Warehouse, dell'app per le funzioni di Azure e della sottoscrizione di eventi. La soluzione è pronta per la migrazione dei dati dall'hub eventi al data warehouse. Prima di eseguire un'applicazione che genera i dati per l'hub eventi, è necessario configurare alcuni valori.

1. Nel portale selezionare lo spazio dei nomi dell'hub eventi. Selezionare **Stringhe di connessione**.

   ![Selezionare stringhe di connessione](media/event-grid-event-hubs-integration/event-hub-connection.png)

2. Selezionare **RootManageSharedAccessKey**

   ![Selezionare la chiave](media/event-grid-event-hubs-integration/show-root-key.png)

3. Copiare **Stringa di connessione - Chiave primaria**

   ![Copiare la chiave](media/event-grid-event-hubs-integration/copy-key.png)

4. Tornare al progetto Visual Studio. Nel progetto WindTurbineDataGenerator aprire **program.cs**.

5. Sostituire i due valori costanti. Usare il valore copiato per **EventHubConnectionString**. Usare il nome dell'hub eventi per **EventHubName**.

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://tfdatamigratens.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Compilare la soluzione. Eseguire l'applicazione WindTurbineGenerator.exe. Dopo pochi minuti eseguire una query sulla tabella nel data warehouse per i dati migrati.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
* Per informazioni introduttive alla funzionalità di acquisizione di Hub eventi, vedere [Abilitare Acquisizione di Hub eventi usando il portale di Azure](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Per altre informazioni sulla configurazione e l'esecuzione dell'esempio, vedere l'[esempio della funzionalità di acquisizione di Hub eventi e della Griglia di eventi](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).