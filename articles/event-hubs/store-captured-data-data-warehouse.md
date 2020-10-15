---
title: 'Esercitazione: Eseguire la migrazione di dati di eventi ad Azure Synapse Analytics - Hub eventi di Azure'
description: "Esercitazione: Questa esercitazione illustra come acquisire i dati dall'hub eventi in Azure Synapse Analytics usando una funzione di Azure attivata da una griglia di eventi."
services: event-hubs
ms.date: 06/23/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: b2a35647422c91d6859e1889f906ae512ce41a56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89436613"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-azure-synapse-analytics-using-event-grid-and-azure-functions"></a>Esercitazione: Eseguire la migrazione dei dati di Hub eventi acquisiti ad Azure Synapse Analytics usando Griglia di eventi e Funzioni di Azure

[Acquisizione](./event-hubs-capture-overview.md) di Hub eventi è il modo più semplice per distribuire automaticamente dati in streaming di Hub eventi a un archivio BLOB di Azure o un Azure Data Lake Store. Successivamente, è possibile elaborare e distribuire i dati a qualsiasi altra destinazione di archiviazione, ad esempio Azure Synapse Analytics o Cosmos DB. In questa esercitazione si apprenderà come acquisire i dati dall'hub eventi in Azure Synapse Analytics usando una funzione di Azure attivata da una [griglia di eventi](../event-grid/overview.md).

![Visual Studio](./media/store-captured-data-data-warehouse/EventGridIntegrationOverview.PNG)

- Per iniziare, si crea un hub eventi con la funzionalità **Acquisizione** abilitata e si imposta un archivio BLOB di Azure come destinazione. I dati generati da WindTurbineGenerator vengono trasmessi in streaming nell'hub eventi e acquisiti automaticamente in Archiviazione di Azure come file Avro.
- Si crea quindi una sottoscrizione a Griglia di eventi di Azure con lo spazio dei nomi di Hub eventi come origine e l'endpoint di Funzioni di Azure come destinazione.
- Ogni volta che la funzionalità Acquisizione di Hub eventi recapita un nuovo file Avro al BLOB di Archiviazione di Azure, la Griglia eventi invia alla funzione di Azure una notifica con l'URI del BLOB. La funzione esegue quindi la migrazione dei dati dal BLOB ad Azure Synapse Analytics.

In questa esercitazione vengono completate le azioni seguenti:

> [!div class="checklist"]
>
> - Distribuire l'infrastruttura
> - Pubblicare il codice in un'app per le funzioni
> - Creare una sottoscrizione a Griglia di eventi dall'app per le funzioni
> - Trasmettere in streaming dati di esempio in Hub eventi
> - Verificare i dati acquisiti in Azure Synapse Analytics

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- [Visual Studio 2019](https://www.visualstudio.com/vs/). Durante l'installazione, assicurarsi di installare i carichi di lavoro seguenti: Sviluppo per desktop .NET, Sviluppo di Azure, Sviluppo ASP.NET e Web, Sviluppo Node.js, Sviluppo Python
- Scaricare l'[esempio Git](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/EventHubsCaptureEventGridDemo). La soluzione di esempio contiene i componenti seguenti:

  - *WindTurbineDataGenerator*: un server di pubblicazione semplice che invia dati di turbine eoliche di esempio a un hub eventi con Acquisizione abilitata
  - *FunctionDWDumper*: una funzione di Azure che riceve una notifica da Griglia di eventi quando un file Avro viene acquisito nel BLOB di Archiviazione di Azure. Riceve il percorso URI del BLOB, ne legge il contenuto ed esegue il push dei dati ad Azure Synapse Analytics.

  In questo esempio viene usato il pacchetto Azure.Messaging.EventHubs più recente. L'esempio precedente che usa il pacchetto Microsoft.Azure.EventHubs è disponibile [qui](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).

### <a name="deploy-the-infrastructure"></a>Distribuire l'infrastruttura

Usare Azure PowerShell o l'interfaccia della riga di comando di Azure per distribuire l'infrastruttura necessaria per questa esercitazione con questo [modello di Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json). Questo modello crea le risorse seguenti:

- Hub eventi con la funzionalità Acquisizione abilitata
- Account di archiviazione per i dati degli eventi acquisiti
- Piano di servizio app di Azure per l'hosting dell'app per le funzioni
- App per le funzioni per l'elaborazione dei file degli eventi acquisiti
- SQL Server logico per l'hosting del Data Warehouse
- Azure Synapse Analytics per l'archiviazione dei dati trasferiti

Le sezioni seguenti riportano i comandi dell'interfaccia della riga di comando di Azure e di Azure PowerShell per distribuire l'infrastruttura necessaria per l'esercitazione. Aggiornare i nomi degli oggetti seguenti prima di eseguire i comandi: 

- Gruppo di risorse di Azure 
- Regione del gruppo di risorse
- Spazio dei nomi di Hub eventi
- Hub eventi
- Server SQL logico
- Utente SQL (e password)
- Database SQL di Azure
- Archiviazione di Azure 
- App per le funzioni di Azure

Questi script richiedono un po' di tempo per creare tutti gli artefatti di Azure. Attendere il completamento dello script prima di procedere. Se la distribuzione non riesce per qualche motivo, eliminare il gruppo di risorse, risolvere il problema segnalato e rieseguire il comando. 

#### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per distribuire il modello tramite l'interfaccia della riga di comando di Azure, usare i comandi seguenti:

```azurecli-interactive
az group create -l westus -n rgDataMigrationSample

az group deployment create `
  --resource-group rgDataMigrationSample `
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json `
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

#### <a name="azure-powershell"></a>Azure PowerShell
Per distribuire il modello tramite PowerShell, usare i comandi seguenti:

```powershell
New-AzResourceGroup -Name rgDataMigration -Location westcentralus

New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```

### <a name="create-a-table-in-azure-synapse-analytics"></a>Creare una tabella in Azure Synapse Analytics

Creare una tabella in Azure Synapse Analytics eseguendo lo script [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) usando [Visual Studio](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-query-visual-studio.md), [SQL Server Management Studio](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-query-ssms.md) o l'editor di query nel portale. 

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

## <a name="publish-code-to-the-functions-app"></a>Pubblicare il codice nell'app per le funzioni

1. Aprire la soluzione *EventHubsCaptureEventGridDemo.sln* in Visual Studio 2019.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su *FunctionDWDumper* e selezionare **Pubblica**.

   ![Pubblicare l'app per le funzioni](./media/store-captured-data-data-warehouse/publish-function-app.png)

1. Selezionare **App per le funzioni di Azure** e **Seleziona esistente**. Selezionare **Pubblica**.

   ![App per le funzioni di destinazione](./media/store-captured-data-data-warehouse/pick-target.png)

1. Selezionare l'app per le funzioni che è stata distribuita tramite il modello. Selezionare **OK**.

   ![Selezionare l'app per le funzioni](./media/store-captured-data-data-warehouse/select-function-app.png)

1. Quando il profilo è stato configurato automaticamente in Visual Studio, selezionare **Pubblica**.

   ![Select publish](./media/store-captured-data-data-warehouse/select-publish.png)

Dopo aver pubblicato la funzione, si è pronti per sottoscrivere l'evento di acquisizione da Hub eventi.


## <a name="create-an-event-grid-subscription-from-the-functions-app"></a>Creare una sottoscrizione a Griglia di eventi dall'app per le funzioni
 
1. Accedere al [portale di Azure](https://portal.azure.com/). Selezionare il gruppo di risorse e l'app per le funzioni.

   ![Visualizzare l'app per le funzioni](./media/store-captured-data-data-warehouse/view-function-app.png)

1. Selezionare la funzione.

   ![Selezionare la funzione](./media/store-captured-data-data-warehouse/select-function.png)

1. Selezionare **Aggiungi sottoscrizione di Griglia di eventi**.

   ![Aggiungere una sottoscrizione](./media/store-captured-data-data-warehouse/add-event-grid-subscription.png)

1. Assegnare un nome alla sottoscrizione di Griglia di eventi. Usare **Spazi dei nomi di Hub eventi** come tipo di evento. Indicare i valori per selezionare l'istanza dello spazio dei nomi di Hub eventi. Lasciare l'endpoint del sottoscrittore come valore specificato. Selezionare **Crea**.

   ![Creare la sottoscrizione](./media/store-captured-data-data-warehouse/set-subscription-values.png)

## <a name="generate-sample-data"></a>Generare i dati di esempio  
È stata configurata la sottoscrizione di Hub eventi, di Azure Synapse Analytics, dell'app per le funzioni di Azure e di Griglia di eventi. È possibile eseguire WindTurbineDataGenerator.exe per generare flussi di dati e inviarli all'Hub eventi dopo aver aggiornato la stringa di connessione e il nome dell'hub eventi nel codice sorgente. 

1. Nel portale selezionare lo spazio dei nomi dell'hub eventi. Selezionare **Stringhe di connessione**.

   ![Selezionare stringhe di connessione](./media/store-captured-data-data-warehouse/event-hub-connection.png)

2. Selezionare **RootManageSharedAccessKey**

   ![Selezionare la chiave](./media/store-captured-data-data-warehouse/show-root-key.png)

3. Copiare **Stringa di connessione - Chiave primaria**

   ![Copiare la chiave](./media/store-captured-data-data-warehouse/copy-key.png)

4. Tornare al progetto Visual Studio. Nel progetto *WindTurbineDataGenerator* aprire *program.cs*.

5. Aggiornare i valori di **EventHubConnectionString** e **EventHubName** con la stringa di connessione e il nome dell'hub eventi. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Compilare la soluzione, quindi eseguire l'applicazione WindTurbineGenerator.exe. 

## <a name="verify-captured-data-in-data-warehouse"></a>Verificare i dati acquisiti nel data warehouse
Dopo un paio di minuti, eseguire una query sulla tabella in Azure Synapse Analytics. Si osserverà che i dati generati da WindTurbineDataGenerator sono stati trasmessi a Hub eventi, acquisiti in un contenitore di Archiviazione di Azure e quindi trasferiti nella tabella di Azure Synapse Analytics tramite la funzione di Azure.  

## <a name="next-steps"></a>Passaggi successivi 
È possibile usare strumenti di visualizzazione avanzata dei dati con il data warehouse per ottenere informazioni operative dettagliate.

Questo articolo illustra come usare [Power BI con Azure Synapse Analytics](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)