---
title: Configurare e usare Collegamento ad Azure Synapse per Azure Cosmos DB
description: Informazioni su come abilitare il collegamento sinapsi per gli account Azure Cosmos DB, creare un contenitore con l'archivio analitico abilitato, connettere il database di Azure Cosmos all'area di lavoro sinapsi ed eseguire le query.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/30/2020
ms.author: rosouz
ms.custom: references_regions
ms.openlocfilehash: 32b64cd0d83c51a77b7bc58bde80e00e1980c233
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463331"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db"></a>Configurare e usare Collegamento ad Azure Synapse per Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Il [collegamento di una sinapsi di Azure per Azure Cosmos DB](synapse-link.md) è una funzionalità ibrida di elaborazione transazionale e analitica (HTAP) nativa del cloud che consente di eseguire analisi quasi in tempo reale sui dati operativi in Azure Cosmos DB. Collegamento a Synapse crea una stretta integrazione tra Azure Cosmos DB e Azure Synapse Analytics.

Il collegamento sinapsi di Azure è disponibile per Azure Cosmos DB contenitori di API SQL o per Azure Cosmos DB API per le raccolte di database Mongo. Usare la procedura seguente per eseguire query analitiche con il collegamento sinapsi di Azure per Azure Cosmos DB:

* [Abilitare il collegamento sinapsi per gli account di Azure Cosmos DB](#enable-synapse-link)
* [Creare un archivio analitico abilitato Azure Cosmos DB contenitore](#create-analytical-ttl)
* [Connettere il database Azure Cosmos DB a un'area di lavoro sinapsi](#connect-to-cosmos-database)
* [Eseguire query sull'archivio analitico con Synapse Spark](#query-analytical-store-spark)
* [Eseguire query nell'archivio analitico usando un pool SQL senza server](#query-analytical-store-sql-on-demand)
* [Usare un pool SQL senza server per analizzare e visualizzare i dati in Power BI](#analyze-with-powerbi)

## <a name="enable-azure-synapse-link-for-azure-cosmos-db-accounts"></a><a id="enable-synapse-link"></a>Abilitare il collegamento a sinapsi di Azure per gli account di Azure Cosmos DB

### <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Consente [di creare un nuovo account Azure](create-sql-api-dotnet.md#create-account)o di selezionare un account di Azure Cosmos DB esistente.

1. Passare all'account di Azure Cosmos DB e aprire il riquadro **funzionalità** .

1. Selezionare **Collegamento a Synapse** nell'elenco di funzionalità.

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="Trova la funzionalità di collegamento sinapsi":::

1. Viene quindi chiesto di abilitare Collegamento a Synapse nell'account. Selezionare **Abilita**. Il completamento di questo processo può richiedere da 1 a 5 minuti.

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="Abilitare la funzionalità Collegamento a Synapse":::

1. L'account è ora abilitato per l'uso di Collegamento a Synapse. Vedere quindi come creare contenitori abilitati per l'archivio analitico per iniziare automaticamente a replicare i dati operativi dall'archivio transazionale all'archivio analitico.

> [!NOTE]
> Quando si attiva il collegamento sinapsi, l'archivio analitico non viene attivato automaticamente. Dopo aver abilitato il collegamento sinapsi nell'account Cosmos DB, abilitare l'archivio analitico nei contenitori al momento della creazione, per avviare la replica dei dati dell'operazione nell'archivio analitico. 

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a> Creare un contenitore Azure Cosmos con l'archivio analitico

È possibile attivare l'archivio analitico in un contenitore Azure Cosmos durante la creazione del contenitore. È possibile usare il portale di Azure o configurare la proprietà `analyticalTTL` durante la creazione del contenitore tramite Azure Cosmos DB SDK.

> [!NOTE]
> Attualmente, l'archivio analitico può essere abilitato solo per i **nuovi** contenitori (in account nuovi ed esistenti). È possibile eseguire la migrazione dei dati dai contenitori esistente ai nuovi contenitori usando [Azure Cosmos DB strumenti di migrazione.](cosmosdb-migrationchoices.md)

### <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/) o al [Azure Cosmos DB Explorer](https://cosmos.azure.com/).

1. Passare all'account Azure Cosmos DB e aprire la scheda **Esplora dati**.

1. Selezionare **Nuovo contenitore** e immettere un nome per il database, il contenitore, la chiave di partizione e i dettagli relativi alla velocità effettiva. Attivare l'opzione **Analytical store** (Archivio analitico). Dopo aver abilitato l'archivio analitico, viene creato un contenitore con la proprietà `AnalyicalTTL` impostata sul valore predefinito -1 (conservazione infinita). Questo archivio analitico conserva tutte le versioni cronologiche dei record.

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="Attivare l'archivio analitico per il contenitore Azure Cosmos":::

1. Se in precedenza Collegamento a Synapse non è stato abilitato per questo account, verrà chiesto di farlo perché è un prerequisito per creare un contenitore abilitato per l'archivio analitico. Se richiesto, selezionare **Enable Synapse Link** (Abilita Collegamento a Synapse). Il completamento di questo processo può richiedere da 1 a 5 minuti.

1. Selezionare **OK** per creare un contenitore Azure Cosmos abilitato per l'archivio analitico.

1. Dopo aver creato il contenitore, verificare che l'archivio analitico sia stato abilitato facendo clic su **Impostazioni**, al di sotto dei documenti Esplora dati e controllare se l'opzione **durata dell'archivio analitico** è attivata.

### <a name="net-sdk"></a>.NET SDK

Il codice seguente crea un contenitore con archivio analitico tramite .NET SDK. Impostare la proprietà TTL dei dati analitici sul valore richiesto. Per l'elenco dei valori consentiti, vedere l'articolo [Valori di TTL supportati per i dati analitici](analytical-store-introduction.md#analytical-ttl):

```csharp
// Create a container with a partition key, and analytical TTL configured to  -1 (infinite retention)
string containerId = “myContainerName”;
int analyticalTtlInSec = -1;
ContainerProperties cpInput = new ContainerProperties()
            {
Id = containerId,
PartitionKeyPath = "/id",
AnalyticalStorageTimeToLiveInSeconds = analyticalTtlInSec,
};
 await this. cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(cpInput);
```

### <a name="java-v4-sdk"></a>Java v4 SDK

Il codice seguente crea un contenitore con archivio analitico tramite Java V4 SDK. Impostare la proprietà `AnalyticalStoreTimeToLiveInSeconds` sul valore richiesto:

```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="python-v4-sdk"></a>SDK per Python V4

Python 2,7 e Azure Cosmos DB SDK 4.1.0 sono le versioni minime richieste e l'SDK è compatibile solo con l'API SQL.

Il primo passaggio consiste nel verificare di usare almeno la versione 4.1.0 di [Azure Cosmos DB Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos):

```python
import azure.cosmos as cosmos

print (cosmos.__version__)
```
Il passaggio successivo consente di creare un contenitore con archivio analitico usando il Azure Cosmos DB Python SDK:

```python
# Azure Cosmos DB Python SDK, for SQL API only.
# Creating an analytical store enabled container.

import azure.cosmos.cosmos_client as cosmos_client
import azure.cosmos.exceptions as exceptions
from azure.cosmos.partition_key import PartitionKey

HOST = 'your-cosmos-db-account-URI'
KEY = 'your-cosmos-db-account-key'
DATABASE = 'your-cosmos-db-database-name'
CONTAINER = 'your-cosmos-db-container-name'

client = cosmos_client.CosmosClient(HOST,  KEY )
# setup database for this sample. 
# If doesn't exist, creates a new one with the name informed above.
try:
    db = client.create_database(DATABASE)

except exceptions.CosmosResourceExistsError:
    db = client.get_database_client(DATABASE)

# Creating the container with analytical store enabled, using the name informed above.
# If a container with the same name exists, an error is returned.
#
# The 3 options for the analytical_storage_ttl parameter are:
# 1) 0 or Null or not informed (Not enabled).
# 2) -1 (The data will be stored in analytical store infinitely).
# 3) Any other number is the actual ttl, in seconds.

try:
    container = db.create_container(
        id=CONTAINER,
        partition_key=PartitionKey(path='/id', kind='Hash'),analytical_storage_ttl=-1
    )
    properties = container.read()
    print('Container with id \'{0}\' created'.format(container.id))
    print('Partition Key - \'{0}\''.format(properties['partitionKey']))

except exceptions.CosmosResourceExistsError:
    print('A container with already exists')
```

### <a name="update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a> Aggiornare la durata (TTL) dell'archivio analitico

Dopo aver abilitato l'archivio analitico con un valore specifico di TTL, è possibile aggiornarlo con un altro valore valido in un secondo momento. È possibile aggiornare il valore usando il portale di Azure o gli SDK. Per informazioni sulle varie opzioni di configurazione del TTL dei dati analitici, vedere l'articolo [Valori di TTL supportati per i dati analitici](analytical-store-introduction.md#analytical-ttl).

#### <a name="azure-portal"></a>Portale di Azure

Se è stato creato un contenitore abilitato per l'archivio analitico tramite il portale di Azure, conterrà il valore predefinito -1 del TTL per i dati analitici. Per aggiornare questo valore, usare la procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com/) o al [Azure Cosmos DB Explorer](https://cosmos.azure.com/).

1. Passare all'account Azure Cosmos DB e aprire la scheda **Esplora dati**.

1. Selezionare un contenitore esistente con l'archivio analitico abilitato. Espanderlo e modificare i valori seguenti:

  * Aprire la finestra **Scalabilità e impostazioni**.
  * In **Impostazione** trovare** Analytical Storage Time to Live** (Durata TTL dell'archivio analitico).
  * Selezionare **Sì (nessun valore predefinito)** oppure selezionare **Sì** e impostare un valore TTL
  * È consigliabile fare clic su **Salva** per salvare le modifiche.

#### <a name="net-sdk"></a>.NET SDK

Il codice seguente illustra come aggiornare il TTL per l'archivio analitico tramite .NET SDK:

```csharp
// Get the container, update AnalyticalStorageTimeToLiveInSeconds 
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Update analytical store TTL
containerResponse.Resource. AnalyticalStorageTimeToLiveInSeconds = 60 * 60 * 24 * 180  // Expire analytical store data in 6 months;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

#### <a name="java-v4-sdk"></a>Java v4 SDK

Il codice seguente illustra come aggiornare il TTL per l'archivio analitico tramite Java v4 SDK:

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

// Update analytical store TTL to expire analytical store data in 6 months;
containerProperties.setAnalyticalStoreTimeToLiveInSeconds (60 * 60 * 24 * 180 );  
 
// Update container settings
container.replace(containerProperties).block();
```

## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a> Connettersi a un'area di lavoro di Synapse

Usare le istruzioni riportate in [Connettersi a Collegamento ad Azure Synapse](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md) per accedere a un database Azure Cosmos DB da Azure Synapse Analytics Studio con Collegamento ad Azure Synapse.

## <a name="query-analytical-store-using-apache-spark-for-azure-synapse-analytics"></a><a id="query-analytical-store-spark"></a> Eseguire query sull'archivio analitico usando Apache Spark per Azure sinapsi Analytics

Usare le istruzioni riportate nell'articolo [Eseguire query sull'archivio analitico di Azure Cosmos DB](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md) per eseguire query con Synapse Spark. Questo articolo include alcuni esempi su come interagire con l'archivio analitico dai movimenti di Synapse. Questi movimenti sono visibili quando si fa clic con il pulsante destro del mouse su un contenitore. Con i movimenti è possibile generare rapidamente il codice e modificarlo in base alle esigenze. Sono inoltre perfetti per l'individuazione dei dati con un singolo clic.

## <a name="query-the-analytical-store-using-serverless-sql-pool-in-azure-synapse-analytics"></a><a id="query-analytical-store-sql-on-demand"></a> Eseguire query nell'archivio analitico usando un pool SQL senza server in Azure sinapsi Analytics

Il pool SQL senza server consente di eseguire query e analizzare i dati nei contenitori Azure Cosmos DB abilitati con il collegamento sinapsi di Azure. È possibile analizzare i dati quasi in tempo reale senza compromettere le prestazioni dei carichi di lavoro transazionali. Offre una nota sintassi T-SQL per eseguire query sui dati dall'archivio analitico e la connettività integrata a un'ampia gamma di strumenti di query ad hoc e BI tramite l'interfaccia T-SQL. Per altre informazioni, vedere l'articolo [Archivio analitico query con pool SQL senza server](../synapse-analytics/sql/query-cosmos-db-analytical-store.md) .

## <a name="use-serverless-sql-pool-to-analyze-and-visualize-data-in-power-bi"></a><a id="analyze-with-powerbi"></a>Usare un pool SQL senza server per analizzare e visualizzare i dati in Power BI

È possibile creare un database del pool SQL senza server e le visualizzazioni sul collegamento sinapsi per Azure Cosmos DB. In un secondo momento è possibile eseguire una query sui contenitori di Azure Cosmos e quindi creare un modello con Power BI su tali viste per riflettere tale query. Per altre informazioni, vedere l'articolo come usare un [pool SQL senza server per analizzare i dati Azure Cosmos DB con il collegamento sinapsi](synapse-link-power-bi.md) .

## <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager

Il [modello di Azure Resource Manager](./manage-with-templates.md#azure-cosmos-account-with-analytical-store) crea un collegamento sinapsi abilitato Azure Cosmos DB account per l'API SQL. Questo modello crea un account dell'API (SQL) Core in un'area con un contenitore configurato con il TTL dei dati analitici abilitato e un'opzione per usare la velocità effettiva manuale o con scalabilità automatica. Per distribuire questo modello, fare clic su **Distribuisci in Azure** nella pagina README.

## <a name="getting-started-with-azure-synpase-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a> Introduzione a Collegamento ad Azure Synapse - Esempi

È possibile trovare esempi per iniziare a usare il collegamento a sinapsi di Azure in [GitHub](https://aka.ms/cosmosdb-synapselink-samples). Queste sono la presentazione di soluzioni end-to-end con scenari Internet e di tutto. È anche possibile trovare gli esempi corrispondenti a Azure Cosmos DB API per MongoDB nello stesso repository nella cartella [MongoDB](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/MongoDB) . 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere la documentazione seguente:

* [Collegamento ad Azure Synapse per Azure Cosmos DB.](synapse-link.md)

* [Panoramica dell'archivio analitico di Azure Cosmos DB.](analytical-store-introduction.md)

* [Domande frequenti su Collegamento ad Azure Synapse per Azure Cosmos DB.](synapse-link-frequently-asked-questions.md)

* [Apache Spark in Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md).

* [Supporto del runtime del pool SQL senza server in Azure sinapsi Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md).
