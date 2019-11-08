---
title: Eseguire il push dei dati nell'indice di ricerca usando Data Factory
description: Informazioni su come eseguire il push dei dati in Azure ricerca cognitiva index usando Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: da867ae62ce4480c5d5854ae3f28ad258421905d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73809184"
---
# <a name="push-data-to-an-azure-cognitive-search-index-by-using-azure-data-factory"></a>Eseguire il push dei dati in un indice di ricerca cognitiva di Azure usando Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](data-factory-azure-search-connector.md)
> * [Versione 2 (corrente)](../connector-azure-search.md)

> [!NOTE]
> Le informazioni di questo articolo sono valide per la versione 1 di Data Factory. Se si usa la versione corrente del servizio Data Factory, vedere il [connettore Azure ricerca cognitiva nella versione V2](../connector-azure-search.md).

Questo articolo descrive come usare l'attività di copia per eseguire il push dei dati da un archivio dati di origine supportato a un indice di ricerca cognitiva di Azure. Gli archivi dati di origine supportati sono elencati nella colonna Origine della tabella [Origini e sink supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Questo articolo si basa sull'articolo [Spostamento di dati e attività di copia](data-factory-data-movement-activities.md) , che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivi dati supportati.

## <a name="enabling-connectivity"></a>Abilitazione della connettività
Per consentire la connessione del servizio Data Factory a un archivio dati locale, installare Gateway di gestione dati nell'ambiente locale. È possibile installare il gateway nello stesso computer che ospita l'archivio dati di origine oppure in un computer diverso per evitare che si verifichi un conflitto con l'archivio dati per le risorse.

Gateway di gestione dati consente di connettere le origini dati locali ai servizi cloud in modo sicuro e gestito. Vedere l’articolo [Spostare dati tra cloud e locale](data-factory-move-data-between-onprem-and-cloud.md) per informazioni dettagliate sui Gateway di Gestione dati.

## <a name="getting-started"></a>Introduzione
È possibile creare una pipeline con un'attività di copia che esegue il push dei dati da un archivio dati di origine a un indice di ricerca usando diversi strumenti o API.

Il modo più semplice per creare una pipeline è usare la **Copia guidata**. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

È anche possibile usare gli strumenti seguenti per creare una pipeline: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager modello**, **API .NET**e **API REST**. Vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Se si usano gli strumenti o le API, eseguire la procedura seguente per creare una pipeline che sposta i dati da un archivio dati di origine a un archivio dati sink:

1. Creare i **servizi collegati** per collegare gli archivi di dati di input e output alla data factory.
2. Creare i **set di dati** per rappresentare i dati di input e di output per le operazioni di copia.
3. Creare una **pipeline** con un'attività di copia che accetti un set di dati come input e un set di dati come output.

Quando si usa la procedura guidata, le definizioni JSON per queste entità di data factory (servizi, set di dati e pipeline collegati) vengono create automaticamente. Quando si usano gli strumenti o le API, ad eccezione delle API .NET, usare il formato JSON per definire le entità di Data Factory.  Per un esempio con definizioni JSON per Data Factory entità usate per copiare dati nell'indice di ricerca, vedere [esempio JSON: copiare dati da un SQL Server locale a una sezione di Azure ricerca cognitiva index](#json-example-copy-data-from-on-premises-sql-server-to-azure-cognitive-search-index) di questo articolo.

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà JSON che vengono usate per definire Data Factory entità specifiche di un indice di ricerca:

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

La tabella seguente fornisce le descrizioni degli elementi JSON specifici del servizio collegato di Azure ricerca cognitiva.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| type | La proprietà type deve essere impostata su **AzureSearch**. | Sì |
| URL | URL per il servizio di ricerca. | Sì |
| key | Chiave di amministrazione per il servizio di ricerca. | Sì |

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo [Set di dati in Azure Data Factory](data-factory-create-datasets.md) . Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati. La sezione **typeProperties** è diversa per ogni tipo di set di dati. Le proprietà della sezione typeProperties per un set di dati di tipo **AzureSearchIndex** sono le seguenti:

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| type | La proprietà type deve essere impostata su **AzureSearchIndex**.| Sì |
| indexName | Nome dell'indice di ricerca. Il servizio Data Factory non crea l'indice. L'indice deve esistere in ricerca cognitiva di Azure. | Sì |


## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo relativo alla [creazione di pipeline](data-factory-create-pipelines.md) . Proprietà come nome, descrizione, tabelle di input e output e criteri diversi sono disponibili per tutti i tipi di attività. Le proprietà disponibili nella sezione typeProperties variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

Per l'attività di copia, quando il sink è del tipo **AzureSearchIndexSink**, nella sezione typeProperties sono disponibili le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Specifica se eseguire un'unione o una sostituzione quando nell'indice esiste già un documento. Vedere la [proprietà WriteBehavior](#writebehavior-property).| Merge (impostazione predefinita)<br/>Carica| No |
| WriteBatchSize | Carica i dati nell'indice di ricerca quando la dimensione del buffer raggiunge writeBatchSize. Per informazioni dettagliate, vedere la [proprietà WriteBatchSize](#writebatchsize-property). | Da 1 a 1000. Il valore predefinito è 1000. | No |

### <a name="writebehavior-property"></a>Proprietà WriteBehavior
Durante la scrittura di dati, AzureSearchSink esegue operazioni di upsert. In altre parole, quando si scrive un documento, se la chiave del documento esiste già nell'indice di ricerca, Azure ricerca cognitiva aggiorna il documento esistente anziché generare un'eccezione di conflitto.

Le operazioni di upsert eseguite da AzureSearchSink sono le seguenti (con AzureSearch SDK):

- **Merge**: le colonne del nuovo documento vengono unite con quelle del documento esistente. Per le colonne del nuovo documento con valore Null, viene mantenuto il valore del documento esistente.
- **Upload**: il nuovo documento sostituisce quello esistente. Per le colonne del nuovo documento non specificate, il valore è impostato su Null indipendentemente dalla presenza o meno di un valore diverso da Null nel documento esistente.

L'operazione predefinita è **Merge**.

### <a name="writebatchsize-property"></a>Proprietà WriteBatchSize
Il servizio ricerca cognitiva di Azure supporta la scrittura di documenti come batch. Un batch può contenere da 1 a 1000 azioni e un'azione gestisce un documento per eseguire l'operazione di caricamento/unione.

### <a name="data-type-support"></a>Supporto dei tipi di dati
La tabella seguente specifica se un tipo di dati di ricerca cognitiva di Azure è supportato o meno.

| Tipo di dati ricerca cognitiva di Azure | Supportato in Azure ricerca cognitiva sink |
| ---------------------- | ------------------------------ |
| String | S |
| Int32 | S |
| Int64 | S |
| Double | S |
| Booleano | S |
| DataTimeOffset | S |
| String Array | N |
| GeographyPoint | N |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-cognitive-search-index"></a>Esempio JSON: copiare dati da SQL Server locali ad Azure ricerca cognitiva index

L'esempio seguente mostra:

1. Un servizio collegato di tipo [AzureSearch](#linked-service-properties).
2. Un servizio collegato di tipo [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di tipo [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureSearchIndex](#dataset-properties).
4. Una [pipeline](data-factory-create-pipelines.md) con un'attività di copia che utilizza [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) e [AzureSearchIndexSink](#copy-activity-properties).

L'esempio copia i dati di una serie temporale da un database di SQL Server locale per eseguire la ricerca nell'indice ogni ora. Le proprietà JSON usate in questo esempio sono descritte nelle sezioni riportate dopo gli esempi.

Come primo passaggio è necessario configurare Gateway di gestione dati nel computer locale. Le istruzioni sono disponibili nell'articolo [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) .

**Servizio collegato di Azure ricerca cognitiva:**

```JSON
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

**Servizio collegato di SQL Server**

```JSON
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```

**Set di dati input di SQL Server**

L'esempio presuppone che sia stata creata una tabella "MyTable" in SQL Server e che contenga una colonna denominata "timestampcolumn" per i dati di una serie temporale. È possibile eseguire query su più tabelle all'interno dello stesso database usando un singolo set di dati, ma come typeProperty tableName del set di dati deve essere usata una sola tabella.

Impostando "external" su "true" si comunica al servizio Data Factory che il set di dati è esterno a Data Factory e non è prodotto da un'attività al suo interno.

```JSON
{
  "name": "SqlServerDataset",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Set di dati di output di Azure ricerca cognitiva:**

L'esempio copia i dati in un indice di ricerca cognitiva di Azure denominato **Products**. Il servizio Data Factory non crea l'indice. Per eseguire il test dell'esempio, creare un indice con questo nome. Creare l'indice di ricerca con lo stesso numero di colonne del set di dati di input. Le nuove voci vengono aggiunte all'indice di ricerca ogni ora.

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

**Attività di copia in una pipeline con origine SQL e sink di indice di ricerca cognitiva di Azure:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo **source** è impostato su **SqlSource** e il tipo **sink** è impostato su **AzureSearchIndexSink**. La query SQL specificata per la proprietà **SqlReaderQuery** consente di selezionare i dati da copiare nell'ultima ora.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

Se si copiano dati da un archivio dati cloud in Azure ricerca cognitiva, `executionLocation` proprietà è obbligatoria. Il frammento JSON seguente mostra la modifica necessaria nell'attività di copia `typeProperties` come esempio. Per informazioni sui valori supportati e altri dettagli, vedere la sezione [Copiare dati tra archivi dati cloud](data-factory-data-movement-activities.md#global).

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```


## <a name="copy-from-a-cloud-source"></a>Copiare da un'origine cloud
Se si copiano dati da un archivio dati cloud in Azure ricerca cognitiva, `executionLocation` proprietà è obbligatoria. Il frammento JSON seguente mostra la modifica necessaria nell'attività di copia `typeProperties` come esempio. Per informazioni sui valori supportati e altri dettagli, vedere la sezione [Copiare dati tra archivi dati cloud](data-factory-data-movement-activities.md#global).

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

È anche possibile eseguire il mapping delle colonne del set di dati di origine alle colonne del set di dati sink nella definizione dell'attività di copia. Per altre informazioni, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestazioni e ottimizzazione
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati (attività di copia) e sui vari modi per ottimizzarle, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

* [Esercitazione dell'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate della creazione di una pipeline con un'attività di copia.
