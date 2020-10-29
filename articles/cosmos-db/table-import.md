---
title: Eseguire la migrazione dei dati in un account dell'API Tabella in Azure Cosmos DB
description: Informazioni su come eseguire la migrazione o importare un'istanza locale o cloud dei dati all'account API Tabella di Azure in Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: f99d7430b76c0b1c4e9c07835267a4a0807efdb1
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92481364"
---
# <a name="migrate-your-data-to-azure-cosmos-db-table-api-account"></a>Eseguire la migrazione dei dati nell'account dell'API Tabella di Azure Cosmos DB

Questa esercitazione fornisce le istruzioni per importare i dati da usare con l'[API Tabelle](table-introduction.md) di Azure Cosmos DB. Se sono stati archiviati dati nell'archiviazione tabelle di Azure, è possibile usare sia l'Utilità di migrazione dati che il comando AzCopy per importare i dati nell'API Tabella di Azure Cosmos DB. Se sono presenti dati archiviati in un account dell'API Table (anteprima) di Azure Cosmos DB, è necessario usare l'Utilità di migrazione dati per la migrazione dei dati. 

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Importazione dei dati con l'Utilità di migrazione dati
> * Importazione dei dati con AzCopy
> * Migrazione dall'API Table (anteprima) all'API Table 

## <a name="prerequisites"></a>Prerequisiti

* **Aumentare la velocità effettiva:** la durata della migrazione dei dati dipende dalla velocità effettiva configurata per un singolo contenitore o un set di contenitori. Assicurarsi di aumentare la velocità effettiva per le migrazioni dei dati di dimensioni più grandi. Dopo avere completato la migrazione, diminuire la velocità effettiva per ridurre i costi. Per altre informazioni sull'aumento della velocità effettiva nel portale di Azure, vedere Livelli di prestazioni e piani tariffari in Azure Cosmos DB.

* **Creare risorse di Azure Cosmos DB:** prima di iniziare la migrazione dei dati, creare tutte le tabelle dal portale di Azure. Se si esegue la migrazione a un account Azure Cosmos DB con velocità effettiva a livello di database, assicurarsi di fornire una chiave di partizione quando si creano le tabelle di Azure Cosmos DB.

## <a name="data-migration-tool"></a>Utilità di migrazione dati

L'Utilità di migrazione dati di Azure Cosmos DB da riga di comando (dt.exe) consente di importare i dati dell'archiviazione tabelle di Azure esistenti in un account GA dell'API Table o di migrare i dati da un account dell'API Table (anteprima) a un account GA dell'API Table. Al momento non sono supportate altre origini. L'Utilità di migrazione dati basata su interfaccia utente (dtui.exe) non è attualmente supportata per gli account dell'API Table. 

Per eseguire una migrazione dei dati di tabelle, completare le attività seguenti:

1. Scaricare l'Utilità di migrazione dati da [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Eseguire `dt.exe` usando gli argomenti della riga di comando per lo scenario in questione. `dt.exe` accetta un comando nel formato seguente:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

Le opzioni supportate per questo comando sono:

* **/ErrorLog:** Facoltativa. Nome del file CSV per il reindirizzamento degli errori di trasferimento dei dati
* **/OverwriteErrorLog:** Facoltativa. Sovrascrive il file di log degli errori
* **/ProgressUpdateInterval:** Facoltativa, il valore predefinito è 00:00:01. Intervallo di tempo per l'aggiornamento dello stato di avanzamento del trasferimento dei dati sullo schermo
* **/ErrorDetails:** Facoltativa, l'impostazione predefinita è None. Specifica che devono essere visualizzate informazioni dettagliate sull'errore per gli errori seguenti: None, Critical, All
* **/EnableCosmosTableLog:** Facoltativa. Indirizza il log a un account tabella Cosmos. Se impostata, il valore predefinito è la stringa di connessione dell'account di destinazione, a meno che non sia stata specificata anche l'opzione /CosmosTableLogConnectionString. Utile se più istanze dell'utilità di migrazione dati vengono eseguite simultaneamente.
* **/CosmosTableLogConnectionString:** Facoltativa. Stringa di connessione per indirizzare il log a un account tabella Cosmos remoto.

### <a name="command-line-source-settings"></a>Impostazioni dell'origine della riga di comando

Usare le opzioni dell'origine seguenti per definire l'archiviazione tabelle di Azure o l'anteprima dell'API Table come origine della migrazione.

* **/s:AzureTable:** Esegue la lettura dei dati da un archivio tabelle di Azure
* **/s.ConnectionString:** Stringa di connessione per l'endpoint tabella. Può essere recuperata dal portale di Azure
* **/s.LocationMode:** Facoltativa, l'impostazione predefinita è PrimaryOnly. Specifica la modalità della posizione da usare per la connessione all'archiviazione tabelle di Azure: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
* **/s.Table:** Nome della tabella di Azure
* **/s.InternalFields:** Impostata su All per la migrazione della tabella poiché RowKey e PartitionKey sono necessari per l'importazione.
* **/s.Filter:** Facoltativa. Stringa di filtro da applicare
* **/s.Projection:** Facoltativa. Elenco di colonne da selezionare

Per recuperare la stringa di connessione di origine durante l'importazione dall'archiviazione tabelle di Azure, aprire il portale di Azure e fare clic su **Account di archiviazione** > **Account** > **Chiavi di accesso** , quindi usare il pulsante Copia per copiare la **stringa di connessione** .

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="Screenshot che mostra le opzioni Account di archiviazione > Account > Chiavi di accesso ed evidenzia il pulsante Copia.":::

Per recuperare la stringa di connessione di origine durante l'importazione da un account dell'API Table (anteprima) di Azure Cosmos DB, aprire il portale di Azure e fare clic su **Azure Cosmos DB** > **Account** > **Stringa di connessione** , quindi usare il pulsante Copia per copiare la **stringa di connessione** .

:::image type="content" source="./media/table-import/cosmos-connection-string.png" alt-text="Screenshot che mostra le opzioni Account di archiviazione > Account > Chiavi di accesso ed evidenzia il pulsante Copia.":::

[Comando di archiviazione tabelle di Azure di esempio](#azure-table-storage)

[Comando di API Table (anteprima) di Azure Cosmos DB](#table-api-preview)

### <a name="command-line-target-settings"></a>Impostazioni della destinazione della riga di comando

Usare le opzioni della destinazione seguenti per definire l'API Table di Azure Cosmos DB come destinazione della migrazione.

* **/t:TableAPIBulk:** Carica i dati nella tabella di Azure CosmosDB in batch
* **/t.ConnectionString:** Stringa di connessione per l'endpoint tabella
* **/t.TableName:** Specifica il nome della tabella in cui eseguire la scrittura
* **/t.Overwrite:** Facoltativa, l'impostazione predefinita è false. Specifica se i valori esistenti devono essere sovrascritti
* **/t.MaxInputBufferSize:** Facoltativa, l'impostazione predefinita è 1 GB. Stima approssimativa dei byte di input da memorizzare nel buffer prima di scaricare i dati nel sink
* **/t.Throughput:** Facoltativa. Se non è specificata, vengono usate le impostazioni predefinite del servizio. Specifica la velocità effettiva da configurare per la tabella
* **/t.MaxBatchSize:** Facoltativa, l'impostazione predefinita è 2 MB. Specifica le dimensioni del batch in byte

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Comando di esempio: l'origine è l'archiviazione tabelle di Azure

Ecco un esempio di riga di comando per importare dall'archiviazione tabelle di Azure all'API Table:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Comando di esempio: l'origine è l'API Tabella di Azure Cosmos DB (anteprima)

Ecco un esempio di riga di comando per importare dall'anteprima dell'API Table all'API Table GA:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Eseguire la migrazione dei dati mediante AzCopy

L'utilità da riga di comando AzCopy è l'altra opzione per eseguire la migrazione dei dati dall'archiviazione tabelle di Azure all'API Table di Azure Cosmos DB. Per usare AzCopy, è prima necessario esportare i dati come descritto in [Esportare dati da Archiviazione tabelle](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage), quindi importare i dati in Azure Cosmos DB come descritto nell'[API Tabelle di Azure Cosmos DB](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage).

Quando si esegue l'importazione in Azure Cosmos DB, vedere l'esempio seguente. Si noti che il valore /Dest usa cosmosdb, non core.

Comando di importazione di esempio:

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Migrare dall'API Table (anteprima) all'API Table

> [!WARNING]
> Per sfruttare immediatamente i vantaggi delle tabelle disponibili a livello generale, eseguire la migrazione delle tabelle di anteprima esistenti come specificato in questa sezione; in caso contrario si eseguiranno migrazioni automatiche per i clienti dell'anteprima esistenti nelle prossime settimane. Tenere presente tuttavia che le tabelle di anteprima migrate automaticamente presenteranno alcune limitazioni rispetto alle tabelle appena create.

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