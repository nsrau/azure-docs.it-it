---
title: Copiare e trasformare i dati in Azure Cosmos DB (API SQL)Copy and transform data in Azure Cosmos DB (SQL API)
description: Informazioni su come copiare dati da e verso il database Cosmos di Azure (API SQL) e trasformare i dati in Azure Cosmos DB (SQL API) usando Data Factory.Learn how to and from Azure Cosmos DB (SQL API) and transform data in Azure Cosmos DB (SQL API) by using Data Factory.
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/11/2019
ms.openlocfilehash: f0aa70333454b327a0ca76beef2985062ce56715
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415376"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Copiare e trasformare i dati in Azure Cosmos DB (SQL API) tramite Azure Data FactoryCopy and transform data in Azure Cosmos DB (SQL API) by using Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-azure-documentdb-connector.md)
> * [Versione corrente](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In questo articolo viene illustrato come usare l'attività di copia in Azure Data Factory per copiare dati da e verso Azure Cosmos DB (API SQL) e usare Flusso di dati per trasformare i dati in Azure Cosmos DB (API SQL). Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).



>[!NOTE]
>Questo connettore supporta solo cosmos DB SQL API. Per l'API MongoDB, fare riferimento al [connettore per l'API di Azure Cosmos DB per MongoDB](connector-azure-cosmos-db-mongodb-api.md). Al momento non sono supportati altri tipi di API.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Azure Cosmos DB (SQL API) è supportato per le attività seguenti:This Azure Cosmos DB (SQL API) connector is supported for the following activities:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Mapping del flusso di dati](concepts-data-flow-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)

Per l'attività di copia, questo connettore Azure Cosmos DB (SQL API) supporta:For Copy activity, this Azure Cosmos DB (SQL API) connector supports:

- Copiare dati da e verso l'[API SQL](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction) di Azure Cosmos DB.
- Scrivere in Azure Cosmos DB tramite **insert** o **upsert**.
- Importare ed esportare documenti JSON come sono o copiare dati da o in un set di dati tabulari, ad esempio un database SQL e un file CSV. Per copiare documenti così come sono da o verso file JSON o da o verso un'altra raccolta di database Cosmos di Azure, vedere [Importare ed esportare documenti JSON.](#import-and-export-json-documents)

Data Factory si integra con la [libreria dell'executor bulk di Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) per offrire le migliori prestazioni durante la scrittura in Azure Cosmos DB.

> [!TIP]
> Il [video sulla migrazione dei dati](https://youtu.be/5-SRNiC_qOU) illustra la procedura di copia dei dati da Archiviazione BLOB di Azure ad Azure Cosmos DB. Il video presenta inoltre alcune considerazioni generali sull'ottimizzazione delle prestazioni per l'inserimento di dati in Azure Cosmos DB.

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti presentano informazioni dettagliate sulle proprietà che è possibile usare per definire entità di Data Factory specifiche per Azure Cosmos DB (API SQL).

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Azure Cosmos DB (API SQL) sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** deve essere impostata su **CosmosDb**. | Sì |
| connectionString |Specificare le informazioni richieste per connettersi al database di Azure Cosmos DB.<br />**Nota**: è necessario specificare le informazioni sul database nella stringa di connessione come illustrato negli esempi seguenti. <br/> È anche possibile inserire la chiave dell'account in Azure Key Vault e rimuovere la configurazione di `accountKey` dalla stringa di connessione. Vedere gli esempi seguenti e l'articolo [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) per altri dettagli. |Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare Azure Integration Runtime o un runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se questa proprietà non è specificata, come valore predefinito viene usato Azure Integration Runtime. |No |

**Esempio**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio: archiviare la chiave dell'account in Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere [Set di dati e servizi collegati](concepts-datasets-linked-services.md).

Le proprietà seguenti sono supportate per il set di dati di Azure Cosmos DB (SQL API):The following properties are supported for Azure Cosmos DB (SQL API) dataset: 

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** del dataset deve essere impostata su **CosmosDbSqlApiCollection**. |Sì |
| collectionName |Nome della raccolta documenti di Azure Cosmos DB. |Sì |

Se si utilizza il set di dati di tipo "DocumentDbCollection", è ancora supportato come-è per la compatibilità con le versioni precedenti per l'attività copia e ricerca, non è supportato per il flusso di dati. Si consiglia di utilizzare il nuovo modello in futuro.

**Esempio**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink di Azure Cosmos DB (API SQL). Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [Pipeline](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (API SQL) come origine

Per copiare dati da Azure Cosmos DB (API SQL), impostare il tipo **source** nell'attività di copia su **DocumentDbCollectionSource**. 

Nella sezione Copia origine attività sono supportate le proprietà seguenti:The following properties are supported in the Copy Activity **source** section:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** dell'origine dell'attività di copia deve essere impostata **su CosmosDbSqlApiSource**. |Sì |
| query |Specificare la query Azure Cosmos DB per leggere i dati.<br/><br/>Esempio:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |No <br/><br/>Se non specificato, viene eseguita questa istruzione SQL: `select <columns defined in structure> from mycollection` |
| preferredRegioni | Elenco preferito di aree a cui connettersi durante il recupero di dati da Cosmos DB. | No |
| Pagesize | Numero di documenti per pagina del risultato della query. Il valore predefinito è "-1", ovvero utilizza la dimensione della pagina dinamica lato servizio fino a 1000. | No |

Se si utilizza l'origine del tipo "DocumentDbCollectionSource", è comunque supportata come-è per la compatibilità con le versioni precedenti. Si consiglia di utilizzare il nuovo modello in futuro che forniscono funzionalità più avanzate per copiare i dati da Cosmos DB.

**Esempio**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Quando si copiano dati da Cosmos DB, a meno che non si desideri [esportare documenti JSON così come sono](#import-and-export-json-documents), la procedura consigliata consiste nello specificare il mapping nell'attività di copia. Data Factory rispetta il mapping specificato nell'attività: se una riga non contiene un valore per una colonna, viene fornito un valore null per il valore della colonna. Se non si specifica un mapping, Data Factory deduce lo schema utilizzando la prima riga nei dati. Se la prima riga non contiene lo schema completo, alcune colonne saranno mancanti nel risultato dell'operazione di attività.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (API SQL) come sink

Per copiare dati in Azure Cosmos DB (API SQL), impostare il tipo **sink** nell'attività di copia su **DocumentDbCollectionSink**. 

Nella sezione Copia origine attività sono supportate le proprietà seguenti:The following properties are supported in the Copy Activity **source** section:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** del sink attività Copy deve essere impostata su **CosmosDbSqlApiSink**. |Sì |
| writeBehavior |Descrive come scrivere i dati in Azure Cosmos DB. Valori consentiti: **insert** e **upsert**.<br/><br/>Il comportamento di **upsert** consiste nella sostituzione del documento se esiste già un documento con lo stesso ID. In caso contrario, il documento viene inserito.<br /><br />**Nota**: Data Factory genera automaticamente un ID per un documento se non è specificato nel documento originale o tramite il mapping di colonna. È quindi necessario assicurarsi che il documento contenga un ID in modo che **upsert** funzioni come previsto. |No<br />(il valore predefinito è **insert**) |
| writeBatchSize | Data Factory usa la [libreria dell'executor bulk di Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) per scrivere dati in Azure Cosmos DB. La proprietà **writeBatchSize** controlla le dimensioni dei documenti forniti da ADF alla libreria. È possibile provare ad aumentare il valore per **writeBatchSize** per migliorare le prestazioni e a ridurre il valore se le dimensioni dei documenti diventano grandi. Vedere i suggerimenti di seguito. |No<br />(il valore predefinito è **10.000**) |
| disableMetricsCollection (insieme di disableMetricsCollection) | Data Factory raccoglie metriche come Cosmos DB RUs per l'ottimizzazione delle prestazioni di copia e consigli. Se si è interessati `true` a questo comportamento, specificare di disattivarlo. | No (il valore predefinito è `false`) |

>[!TIP]
>Per importare documenti JSON così come sono, fare riferimento alla sezione [Importare o esportare documenti JSON.](#import-and-export-json-documents) per copiare da dati di forma tabulare, fare riferimento a [Eseguire la migrazione da un database relazionale a Cosmos DB](#migrate-from-relational-database-to-cosmos-db).

>[!TIP]
>Cosmos DB limita le dimensioni per una singola richiesta a 2 MB. La formula è Dimensioni richiesta = Dimensioni singolo documento * Dimensioni batch di scrittura. Se viene generato l'errore **"Dimensioni della richiesta troppo grandi."**, **ridurre il valore `writeBatchSize`** nella configurazione del sink di copia.

Se si utilizza l'origine del tipo "DocumentDbCollectionSink", è comunque supportata come-è per la compatibilità con le versioni precedenti. Si consiglia di utilizzare il nuovo modello in futuro che forniscono funzionalità più avanzate per copiare i dati da Cosmos DB.

**Esempio**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

### <a name="schema-mapping"></a>Mapping dello schema

Per copiare i dati dal database Cosmos di Azure al sink tabulare o al sink inverso, vedere [Mapping dello schema](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

Quando si trasformano i dati nel mapping del flusso di dati, è possibile leggere e scrivere nelle raccolte in Cosmos DB. Per altre informazioni, vedere la [trasformazione dell'origine](data-flow-source.md) e la [trasformazione sink](data-flow-sink.md) nel mapping dei flussi di dati.

### <a name="source-transformation"></a>Trasformazione della fonte

Le impostazioni specifiche di Azure Cosmos DB sono disponibili nella scheda **Opzioni di origine** della trasformazione di origine. 

**Includi colonne di sistema:** Se ```id```true, ```_ts```, e altre colonne di sistema verranno incluse nei metadati del flusso di dati di CosmosDB. Quando si aggiornano le raccolte, è importante includere questo in modo da poter acquisire l'ID di riga esistente.

**Dimensioni pagina:** Numero di documenti per pagina del risultato della query. Il valore predefinito è "-1" che utilizza la pagina dinamica del servizio fino a 1000.

**Velocità effettiva:Throughput:** Impostare un valore facoltativo per il numero di RU che si desidera applicare alla raccolta CosmosDB per ogni esecuzione di questo flusso di dati durante l'operazione di lettura. Il valore minimo è 400.

**Regioni preferite:** Scegliere le aree di lettura preferite per questo processo.

#### <a name="json-settings"></a>Impostazioni JSON

**Documento singolo:** Selezionare questa opzione se ADF deve considerare l'intero file come un singolo documento JSON.

Nomi di **colonna senza virgolette:** Selezionare questa opzione se i nomi di colonna in JSON come non tra virgolette.

**Ha commenti:** Usare questa selezione se i documenti JSON contano commenti nei dati.

**Singolo citato:** Questa opzione deve essere selezionata se le colonne e i valori del documento sono racchiusi tra virgolette singole.

**Barra rovesciata escape:** Se si usano le barre rovesciate per eseguire l'escape dei caratteri in JSON, scegliere questa opzione.

### <a name="sink-transformation"></a>Trasformazione del lanondo

Le impostazioni specifiche di Azure Cosmos DB sono disponibili nella scheda **Impostazioni** della trasformazione sink.

**Metodo di aggiornamento:** Determina quali operazioni sono consentite nella destinazione del database. L'impostazione predefinita è consentire solo gli inserimenti. Per aggiornare, eseguire l'upsert o eliminare le righe, è necessaria una trasformazione di modifica della riga per contrassegnare le righe per tali azioni. Per gli aggiornamenti, gli upsert e le eliminazioni, è necessario impostare una o più colonne chiave per determinare quale riga modificare.

**Azione di ritiro:** Determina se ricreare la raccolta di destinazione prima della scrittura.
* Nessuno: non verrà eseguita alcuna azione per la raccolta.
* Ricrea: la raccolta verrà eliminata e ricreata

**Dimensioni batch**: Controlla il numero di righe scritte in ogni bucket. Dimensioni dei batch maggiori migliorano la compressione e l'ottimizzazione della memoria, ma rischiano di ridurre le eccezioni di memoria durante la memorizzazione dei dati nella cache.

**Chiave di partizione:** Immettere una stringa che rappresenta la chiave di partizione per la raccolta. Esempio: ```/movies/title```

**Velocità effettiva:Throughput:** Impostare un valore facoltativo per il numero di RU che si desidera applicare alla raccolta CosmosDB per ogni esecuzione di questo flusso di dati. Il valore minimo è 400.

**Scriva budget per la velocità effettiva:** Numero intero che rappresenta il numero di RU che si desidera allocare al processo Spark di inserimento in blocco. Questo numero non è compreso nella velocità effettiva totale allocata alla raccolta.

## <a name="lookup-activity-properties"></a>Proprietà dell'attività di ricerca

Per informazioni dettagliate sulle proprietà, selezionare [Attività di ricerca](control-flow-lookup-activity.md).

## <a name="import-and-export-json-documents"></a>Importare ed esportare documenti JSON

È possibile usare questo connettore di Azure Cosmos DB (API SQL) per eseguire facilmente le operazioni seguenti:

* Copiare documenti come sono da una raccolta di Azure Cosmos DB a un'altra.
* Importare in Azure Cosmos DB documenti JSON da varie origini, tra cui Archiviazione BLOB di Azure, Azure Data Lake Storage e altri archivi basati su file supportati da Azure Data Factory.
* Esportare documenti JSON da una raccolta di Azure Cosmos DB in diversi archivi basati su file.

Per ottenere la copia senza schema:

* Quando si usa lo strumento Copia dati, selezionare l'opzione **Export as-is to JSON files or Cosmos DB collection** (Esportare così come sono nel file JSON o in una raccolta di Cosmos DB).
* Quando si usa la creazione di attività, scegliere formato JSON con l'archivio file corrispondente per l'origine o il sink.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>Eseguire la migrazione da un database relazionale a Cosmos DB

Quando si esegue la migrazione da un database relazionale, ad esempio SQL Server a Azure Cosmos DB, l'attività di copia può facilmente eseguire il mapping di dati tabulari dall'origine per appiattire i documenti JSON in Cosmos DB. In alcuni casi, è possibile riprogettare il modello di dati per ottimizzarlo per i casi d'uso NoSQL in base alla modellazione dei dati [in Azure Cosmos DB](../cosmos-db/modeling-data.md), ad esempio per denormalizzare i dati incorporando tutti i sottoelementi correlati all'interno di un documento JSON. In questo caso, fare riferimento a questo articolo con una procedura dettagliata su come ottenerlo usando l'attività di copia di Azure Data Factory.For this case, refer to [this article](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md) with a walkthrough on how to achieve it using Azure Data Factory copy activity.

## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
