---
title: Importare i dati da usare con l'API Tabelle di Azure Cosmos DB | Microsoft Docs
description: Informazioni su come importare dati da usare con l'API Tabelle di Azure Cosmos DB.
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: b60743e2-0227-43ab-965a-0ae3ebacd917
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: fd0454252080679fa880eecb677d609ea0734f09
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="import-data-for-use-with-the-azure-cosmos-db-table-api"></a>Importare dati da usare con l'API Tabelle di Azure Cosmos DB

Questa esercitazione fornisce le istruzioni per importare i dati da usare con l'[API Tabelle](table-introduction.md) di Azure Cosmos DB. Se sono stati archiviati dati nell'archiviazione tabelle di Azure, è possibile usare sia l'Utilità di migrazione dati che il comando AzCopy per importare i dati. Se sono presenti dati archiviati in un account dell'API Table (anteprima) di Azure Cosmos DB, è necessario usare l'Utilità di migrazione dati per la migrazione dei dati. Dopo l'importazione dei dati, sarà possibile usufruire delle funzionalità Premium offerte da Azure Cosmos DB, tra cui distribuzione globale chiavi in mano, velocità effettiva dedicata, latenze pari a singole unità di millisecondi al 99° percentile, disponibilità elevata garantita e indicizzazione secondaria automatica.

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Importazione dei dati con l'Utilità di migrazione dati
> * Importazione dei dati con AzCopy
> * Migrazione dall'API Table (anteprima) all'API Table 

## <a name="data-migration-tool"></a>Utilità di migrazione dati

L'Utilità di migrazione dati di Azure Cosmos DB da riga di comando (dt.exe) consente di importare i dati dell'archiviazione tabelle di Azure esistenti in un account GA dell'API Table o di migrare i dati da un account dell'API Table (anteprima) a un account GA dell'API Table. Al momento non sono supportate altre origini.

Per eseguire una migrazione dei dati di tabelle, completare le attività seguenti:

1. Scaricare l'Utilità di migrazione dati dall'[Area download Microsoft](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) o da [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Eseguire `dt.exe` usando gli argomenti della riga di comando per lo scenario in questione.

dt.exe accetta un comando nel formato seguente:

    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 

Le opzioni del comando sono:

    /ErrorLog: Optional. Name of the CSV file to redirect data transfer failures
    /OverwriteErrorLog: Optional. Overwrite error log file
    /ProgressUpdateInterval: Optional, default is 00:00:01. Time interval to refresh on-screen data transfer progress
    /ErrorDetails: Optional, default is None. Specifies that detailed error information should be displayed for the following errors: None, Critical, All

### <a name="command-line-source-settings"></a>Impostazioni dell'origine della riga di comando

Usare le opzioni dell'origine seguenti per definire l'archiviazione tabelle di Azure o l'anteprima dell'API Table come origine della migrazione.

    /s:AzureTable: Reads data from Azure Table storage
    /s.ConnectionString: Connection string for the table endpoint. This can be retrieved from the Azure portal
    /s.LocationMode: Optional, default is PrimaryOnly. Specifies which location mode to use when connecting to Azure Table storage: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
    /s.Table: Name of the Azure Table
    /s.InternalFields: Set to All for table migration as RowKey and PartitionKey are required for import.
    /s.Filter: Optional. Filter string to apply
    /s.Projection: Optional. List of columns to select

Per recuperare la stringa di connessione di origine durante l'importazione dall'archiviazione tabelle di Azure, aprire il portale di Azure e fare clic su **Account di archiviazione** > **Account** >  **Chiavi di accesso**, quindi usare il pulsante Copia per copiare la **stringa di connessione**.

![Schermata di HBase opzioni del codice sorgente](./media/table-import/storage-table-access-key.png)

Per recuperare la stringa di connessione di origine durante l'importazione da un account dell'API Table (anteprima) di Azure Cosmos DB, aprire il portale di Azure e fare clic su **Azure Cosmos DB** > **Account** >  **Stringa di connessione**, quindi usare il pulsante Copia per copiare la **stringa di connessione**.

![Schermata di HBase opzioni del codice sorgente](./media/table-import/cosmos-connection-string.png)

[Comando di archiviazione tabelle di Azure di esempio](#azure-table-storage)

[Comando di API Table (anteprima) di Azure Cosmos DB](#table-api-preview)

### <a name="command-line-target-settings"></a>Impostazioni della destinazione della riga di comando

Usare le opzioni della destinazione seguenti per definire l'API Table di Azure Cosmos DB come destinazione della migrazione.

    /t:TableAPIBulk: Uploads data into Azure CosmosDB Table in batches
    /t.ConnectionString: Connection string for the table endpoint
    /t.TableName: Specifies the name of the table to write to
    /t.Overwrite: Optional, default is false. Specifies if existing values should be overwritten
    /t.MaxInputBufferSize: Optional, default is 1GB. Approximate estimate of input bytes to buffer before flushing data to sink
    /t.Throughput: Optional, service defaults if not specified. Specifies throughput to configure for table
    /t.MaxBatchSize: Optional, default is 2MB. Specify the batch size in bytes

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Comando di esempio: l'origine è l'archiviazione tabelle di Azure

Ecco un esempio di riga di comando per importare dall'archiviazione tabelle di Azure all'API Table:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey==<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```
<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Comando di esempio: l'origine è l'API Table (anteprima) di Azure Cosmos DB

Ecco un esempio di riga di comando per importare dall'anteprima dell'API Table all'API Table GA:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="azcopy-command"></a>Comando AzCopy

L'utilità da riga di comando AzCopy è l'altra opzione per eseguire la migrazione dei dati dall'archiviazione tabelle di Azure all'API Table di Azure Cosmos DB. Per usare AzCopy, è prima necessario esportare i dati come descritto in [Esportare dati da Archiviazione tabelle](../storage/common/storage-use-azcopy.md#export-data-from-table-storage), quindi importare i dati in Azure Cosmos DB come descritto nell'[API Tabelle di Azure Cosmos DB](../storage/common/storage-use-azcopy.md#import-data-into-table-storage).

Quando si esegue l'importazione in Azure Cosmos DB, vedere l'esempio seguente. Si noti che il valore /Dest usa cosmosdb, non core.

Comando di importazione di esempio:

```
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Migrare dall'API Table (anteprima) all'API Table

> [!WARNING]
> Per sfruttare immediatamente i vantaggi delle tabelle disponibili a livello generale, eseguire la migrazione delle tabelle di anteprima esistenti come specificato in questa sezione; in caso contrario si eseguiranno migrazioni automatiche per i clienti dell'anteprima esistenti nelle prossime settimane. Tenere presente tuttavia che le tabelle di anteprima migrate automaticamente presenteranno alcune limitazioni rispetto alle tabelle appena create.
> 

L'API Table è ora disponibile a livello generale. Sussistono differenze tra le versioni di anteprima e GA delle tabelle sia nel codice che viene eseguito nel cloud che nel codice in esecuzione nel client. Pertanto non è consigliabile provare a combinare un client SDK di anteprima con un account dell'API Table GA e viceversa. I clienti dell'anteprima dell'API Table che intendono continuare a usare le tabelle esistenti, ma in un ambiente di produzione, devono eseguire la migrazione dall'anteprima all'ambiente GA o attendere la migrazione automatica. Se si attende la migrazione automatica, verrà ricevuta una notifica relativa alle restrizioni nelle tabelle migrate. Dopo la migrazione sarà possibile creare nuove tabelle nell'account esistente senza restrizioni (solo le tabelle migrate presenteranno restrizioni).

Per eseguire la migrazione dall'API Table (anteprima) all'API Table disponibile a livello generale:

1. Creare un nuovo account Azure Cosmos DB e impostarne il tipo dell'API su Tabella di Azure, come descritto in [Creare un account di database](create-table-dotnet.md#create-a-database-account).

2. Modificare i client per l'uso di una versione GA degli [SDK dell'API Table](table-sdk-dotnet.md).

3. Eseguire la migrazione dei dati del client dalle tabelle di anteprima alle tabelle GA usando l'Utilità di migrazione dati. Le istruzioni su come usare l'Utilità di migrazione dati a tale scopo sono descritte in [Utilità di migrazione dati](#data-migration-tool). 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Importare dati con l'Utilità di migrazione dati
> * Importare dati con AzCopy
> * Migrare dall'API Table (anteprima) all'API Table

È ora possibile passare all'esercitazione successiva per ottenere informazioni su come eseguire query sui dati usando l'API Tabelle di Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Come eseguire query sui dati](../cosmos-db/tutorial-query-table.md)
