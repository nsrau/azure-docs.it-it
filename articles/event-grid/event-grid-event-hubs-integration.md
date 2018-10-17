---
title: Integrazione di Griglia di eventi di Azure e Hub eventi
description: Descrive come usare Griglia di eventi di Azure e Hub eventi per la migrazione dei dati in un SQL Data Warehouse
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: tutorial
ms.date: 08/22/2018
ms.author: tomfitz
ms.openlocfilehash: aad7a24d8b0e0bc74815cad3604db1cc21a6db96
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44163227"
---
# <a name="stream-big-data-into-a-data-warehouse"></a>Trasmettere Big Data a un data warehouse

[Griglia di eventi](overview.md) di Azure è un servizio intelligente di routing di eventi che consente di rispondere alle notifiche inviate da app e servizi. Ad esempio, può attivare una funzione di Azure per elaborare i dati di Hub eventi che sono stati acquisiti in un'archiviazione BLOB di Azure o Data Lake Store ed eseguire la migrazione dei dati agli altri repository dei dati. L'[esempio della funzionalità di acquisizione di Hub eventi e della Griglia di eventi](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) descrive come usare la funzionalità di acquisizione di Hub eventi con Griglia di eventi per migrare facilmente i dati di Hub eventi da un'archiviazione BLOB a un SQL Data Warehouse.

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

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
* [Visual Studio 2017 versione 15.3.2 o successiva](https://www.visualstudio.com/vs/) con carichi di lavoro per lo sviluppo di desktop .NET, di Azure, Web e ASP.NET, di Node.js e di Python.
* Il [progetto di esempio EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) scaricato sul computer.

## <a name="deploy-the-infrastructure"></a>Distribuire l'infrastruttura

Per semplificare questo articolo, distribuire l'infrastruttura necessaria con un modello di Resource Manager. Per esaminare le risorse che vengono distribuite, visualizzare il [modello](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json).

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

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su **FunctionDWDumper** e selezionare **Pubblica**.

   ![Pubblicare l'app per le funzioni](media/event-grid-event-hubs-integration/publish-function-app.png)

1. Selezionare **App per le funzioni di Azure** e **Seleziona esistente**. Selezionare **Pubblica**.

   ![App per le funzioni di destinazione](media/event-grid-event-hubs-integration/pick-target.png)

1. Selezionare l'app per le funzioni che è stata distribuita tramite il modello. Selezionare **OK**.

   ![Selezionare l'app per le funzioni](media/event-grid-event-hubs-integration/select-function-app.png)

1. Quando il profilo è stato configurato automaticamente in Visual Studio, selezionare **Pubblica**.

   ![Select publish](media/event-grid-event-hubs-integration/select-publish.png)

Dopo aver pubblicato la funzione, si è pronti per sottoscrivere l'evento.

## <a name="subscribe-to-the-event"></a>Sottoscrivere l'evento

1. Accedere al [portale di Azure](https://portal.azure.com/). Selezionare il gruppo di risorse e l'app per le funzioni.

   ![Visualizzare l'app per le funzioni](media/event-grid-event-hubs-integration/view-function-app.png)

1. Selezionare la funzione.

   ![Selezionare la funzione](media/event-grid-event-hubs-integration/select-function.png)

1. Selezionare **Aggiungi sottoscrizione di Griglia di eventi**.

   ![Aggiungi sottoscrizione](media/event-grid-event-hubs-integration/add-event-grid-subscription.png)

9. Assegnare un nome alla sottoscrizione di Griglia di eventi. Usare **Spazi dei nomi di Hub eventi** come tipo di evento. Indicare i valori per selezionare l'istanza dello spazio dei nomi di Hub eventi. Lasciare l'endpoint del sottoscrittore come valore specificato. Selezionare **Create**.

   ![Creare la sottoscrizione](media/event-grid-event-hubs-integration/set-subscription-values.png)

## <a name="run-the-app-to-generate-data"></a>Eseguire l'app per generare i dati

È stata completata la configurazione dell'hub eventi, di SQL Data Warehouse, dell'app per le funzioni di Azure e della sottoscrizione di eventi. La soluzione è pronta per la migrazione dei dati dall'hub eventi al data warehouse. Prima di eseguire un'applicazione che genera i dati per l'hub eventi, è necessario configurare alcuni valori.

1. Nel portale selezionare lo spazio dei nomi dell'hub eventi. Selezionare **Stringhe di connessione**.

   ![Selezionare stringhe di connessione](media/event-grid-event-hubs-integration/event-hub-connection.png)

2. Selezionare **RootManageSharedAccessKey**

   ![Selezionare la chiave](media/event-grid-event-hubs-integration/show-root-key.png)

3. Copiare **Stringa di connessione - Chiave primaria**

   ![Copiare la chiave](media/event-grid-event-hubs-integration/copy-key.png)

4. Tornare al progetto Visual Studio. Nel progetto WindTurbineDataGenerator aprire **program.cs**.

5. Sostituire i due valori costanti. Usare il valore copiato per **EventHubConnectionString**. Usare **hubdatamigration** come nome dell'hub eventi.

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Compilare la soluzione. Eseguire l'applicazione WindTurbineGenerator.exe. Dopo pochi minuti eseguire una query sulla tabella nel data warehouse per i dati migrati.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle differenze tra i servizi di messaggistica di Azure, vedere [Scegliere tra i servizi di Azure che recapitano messaggi](compare-messaging-services.md).
* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
* Per informazioni introduttive alla funzionalità di acquisizione di Hub eventi, vedere [Abilitare Acquisizione di Hub eventi usando il portale di Azure](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Per altre informazioni sulla configurazione e l'esecuzione dell'esempio, vedere l'[esempio della funzionalità di acquisizione di Hub eventi e della Griglia di eventi](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).