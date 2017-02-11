---
title: Push dei dati nell&quot;indice di Ricerca con Azure Data Factory | Documentazione Microsoft
description: Informazioni su come eseguire il push dei dati nell&quot;indice di Ricerca di Azure con Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 55c988bf74ff0f2e519e895a735dc68f3dc99855
ms.openlocfilehash: e2deed13106db9467eef181f25a0a226034df5a2

---

# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>Push dei dati in un indice di Ricerca di Azure con Azure Data Factory
Questo articolo descrive come usare l'attività di copia per eseguire il push dei dati da un archivio dati locale supportato dal servizio Data Factory nell'indice di Ricerca di Azure. Gli archivi dati di origine supportati sono elencati nella colonna Origine della tabella [Origini e sink supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Questo articolo si basa sull'articolo [Spostamento di dati e attività di copia](data-factory-data-movement-activities.md) , che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivi dati supportati.

## <a name="enabling-connectivity"></a>Abilitazione della connettività
Per consentire la connessione del servizio Data Factory a un archivio dati locale, installare Gateway di gestione dati nell'ambiente locale. È possibile installare il gateway nello stesso computer che ospita l'archivio dati di origine oppure in un computer diverso per evitare che si verifichi un conflitto con l'archivio dati per le risorse.

Gateway di gestione dati consente di connettere le origini dati locali ai servizi cloud in modo sicuro e gestito. Vedere l’articolo [Spostare dati tra cloud e locale](data-factory-move-data-between-onprem-and-cloud.md) per informazioni dettagliate sui Gateway di Gestione dati.

## <a name="copy-data-wizard"></a>Copia dati guidata
Il modo più semplice per creare una pipeline che copia i dati in Ricerca di Azure da uno qualsiasi degli archivi dati di origine supportati consiste nell'usare la Copia dati guidata. Per una procedura dettagliata, vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md).

L'esempio seguente fornisce le definizioni JSON si esempio da usare per creare una pipeline con il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Viene illustrato come copiare dati da un'istanza di Server SQL locale a un indice di Ricerca di Azure. I dati possono essere copiati, tuttavia, da uno qualsiasi degli archivi dati locali indicati [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) con l'attività di copia dati disponibile in Data Factory di Azure.   

## <a name="sample-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>Esempio - Copia di dati da un'istanza di SQL Server locale nell'indice di Ricerca di Azure

L'esempio seguente mostra:

1.    Un servizio collegato di tipo [AzureSearch](#azure-search-linked-service-properties).
2.    Un servizio collegato di tipo [OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties).
3.    Un [set di dati](data-factory-create-datasets.md) di tipo [SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties).
4.    Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureSearchIndex](#azure-search-index-dataset-properties).
4.    Una [pipeline](data-factory-create-pipelines.md) con un'attività di copia che usa [SqlSource](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties) e [AzureSearchIndexSink](#azure-search-index-sink-properties).

Nell'esempio i dati della serie temporale vengono copiati ogni ora da un database di SQL Server locale in un indice di Ricerca di Azure. Le proprietà JSON usate in questo esempio sono descritte nelle sezioni riportate dopo gli esempi.

Come primo passaggio è necessario configurare Gateway di gestione dati nel computer locale. Le istruzioni sono disponibili nell'articolo [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) .

**Servizio collegato Ricerca di Azure**

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

**Set di dati di output di Ricerca di Azure**

Nell'esempio i dati vengono copiati in un indice di Ricerca di Azure denominato **products**. Il servizio Data Factory non crea l'indice. Per eseguire il test dell'esempio, creare un indice con questo nome. Creare l'indice di Ricerca di Azure con lo stesso numero di colonne presente nel set di dati di input. Le nuove voci vengono aggiunte all'indice di Ricerca di Azure ogni ora.

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

**Pipeline con un’attività di copia:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline il tipo **source** è impostato su **SqlSource** e il tipo **sink** è impostato su **AzureSearchIndexSink**. La query SQL specificata per la proprietà **SqlReaderQuery** consente di selezionare i dati da copiare nell'ultima ora.

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

Se si copiano dati da un archivio dati cloud a Ricerca di Azure, la proprietà `executionLocation` è obbligatoria. Di seguito è riportata come esempio la modifica necessaria in `typeProperties` per l'attività di copia. Per informazioni sui valori supportati e altri dettagli, vedere la sezione [Copiare dati tra archivi dati cloud](data-factory-data-movement-activities.md#global).

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

## <a name="azure-search-linked-service-properties"></a>Proprietà del servizio collegato Ricerca di Azure

La tabella seguente include le descrizioni degli elementi JSON specifici del servizio collegato Ricerca di Azure.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| type | La proprietà type deve essere impostata su **AzureSearch**. | Sì |
| URL | URL del servizio Ricerca di Azure. | sì |
| key | Chiave amministratore del servizio Ricerca di Azure. | Sì |

## <a name="azure-search-index-dataset-properties"></a>Proprietà del set di dati dell'indice Ricerca di Azure

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo [Set di dati in Azure Data Factory](data-factory-create-datasets.md) . Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati. La sezione **typeProperties** è diversa per ogni tipo di set di dati. Le proprietà della sezione typeProperties per un set di dati di tipo **AzureSearchIndex** sono le seguenti:

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| type | La proprietà type deve essere impostata su **AzureSearchIndex**.| Sì |
| indexName | Nome dell'indice di Ricerca di Azure. Il servizio Data Factory non crea l'indice. L'indice deve essere presente in Ricerca di Azure. | Sì |


## <a name="azure-search-index-sink-properties"></a>Proprietà dei sink dell'indice di Ricerca di Azure
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo relativo alla [creazione di pipeline](data-factory-create-pipelines.md) . Proprietà come nome, descrizione, tabelle di input e output e criteri diversi sono disponibili per tutti i tipi di attività. Le proprietà disponibili nella sezione typeProperties dell'attività variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

Per l'attività di copia, quando l'origine è del tipo **AzureSearchIndexSink**, nella sezione typeProperties sono disponibili le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Specifica se eseguire un'unione o una sostituzione quando nell'indice esiste già un documento. Vedere la [proprietà WriteBehavior](#writebehavior-property).| Merge (impostazione predefinita)<br/>Carica| No |
| WriteBatchSize | Consente di caricare dati nell'indice di Ricerca di Azure quando le dimensioni del buffer raggiungono il valore indicato da writeBatchSize. Per informazioni dettagliate, vedere la [proprietà WriteBatchSize](#writebatchsize-property). | Da 1 a 1000. Il valore predefinito è 1000. | No |

### <a name="writebehavior-property"></a>Proprietà WriteBehavior
Durante la scrittura di dati, AzureSearchSink esegue operazioni di upsert. In altre parole, quando si scrive un documento il servizio Ricerca di Azure aggiorna il documento esistente anziché generare un'eccezione di conflitto se la chiave relativa esiste già nell'indice di Ricerca di Azure.

Le operazioni di upsert eseguite da AzureSearchSink sono le seguenti (con AzureSearch SDK):

- **Merge**: le colonne del nuovo documento vengono unite con quelle del documento esistente. Per le colonne del nuovo documento con valore Null, viene mantenuto il valore del documento esistente.
- **Upload**: il nuovo documento sostituisce quello esistente. Per le colonne del nuovo documento non specificate, il valore è impostato su Null indipendentemente dalla presenza o meno di un valore diverso da Null nel documento esistente.

L'operazione predefinita è **Merge**.

### <a name="writebatchsize-property"></a>Proprietà WriteBatchSize
Il servizio Ricerca di Azure supporta la scrittura di documenti come batch. Un batch può contenere da 1 a 1000 azioni e un'azione gestisce un documento per eseguire l'operazione di caricamento/unione.

### <a name="data-type-support"></a>Supporto dei tipi di dati
La tabella seguente indica se un tipo di dati di Ricerca di Azure è supportato o meno.

| Tipo di dati di Ricerca di Azure | Supportato nel sink di Ricerca di Azure |
| ---------------------- | ------------------------------ |
| String | S |
| Int32 | S |
| Int64 | S |
| Double | S |
| Boolean | S |
| DataTimeOffset | S |
| String Array | N |
| GeographyPoint | N |

## <a name="copy-from-a-cloud-source"></a>Copiare da un'origine cloud
Se si copiano dati da un archivio dati cloud a Ricerca di Azure, la proprietà `executionLocation` è obbligatoria. Di seguito è riportata come esempio la modifica necessaria in `typeProperties` per l'attività di copia. Per informazioni sui valori supportati e altri dettagli, vedere la sezione [Copiare dati tra archivi dati cloud](data-factory-data-movement-activities.md#global).

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

## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Definizione della struttura per i set di dati rettangolari
La sezione della struttura nei set di dati JSON è una sezione **facoltativa** per le tabelle rettangolari (con righe e colonne) e contiene una raccolta di colonne per la tabella. Usare la sezione della struttura per fornire informazioni sul tipo per le conversioni di tipi o per eseguire il mapping di colonne. Le sezioni seguenti descrivono queste funzionalità in modo più dettagliato.

Ogni colonna contiene le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| name | Nome della colonna. | Sì |
| type | Tipo di dati della colonna. Per altre informazioni sul momento in cui specificare informazioni sul tipo, vedere la sezione sulle [conversioni di tipo](#type-conversion-sample). | No |
| culture | Impostazioni cultura basate su .NET da usare quando il tipo è specificato ed un tipo .NET `Datetime` o `Datetimeoffset`. Il valore predefinito è `en-us`.  | No |
| format | Stringa di formato da usare quando il tipo è specificato ed è un tipo .NET `Datetime` o `Datetimeoffset`. | No |

L'esempio seguente illustra la sezione di struttura JSON per una tabella con le tre colonne `userid`, `name` e `lastlogindate`.

```JSON
"structure":
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```
Attenersi alle linee guida seguenti per decidere il momento in cui includere informazioni su "structure" e gli elementi da inserire nella sezione **structure** .

- **Per le origini di dati strutturate** in cui, oltre a informazioni sullo schema e sul tipo di dati, sono archiviati i dati stessi, ad esempio tabelle di SQL Server, Oracle, Azure e così via, specificare la sezione "structure" solo se si esegue il mapping di colonne di origine specifiche a colonne specifiche nel sink e i relativi nomi non sono uguali. Per informazioni dettagliate, vedere la sezioni sul mapping di colonne.

    Come indicato in precedenza, nella sezione "structure" le informazioni sul tipo sono facoltative. Per le origini strutturate, le informazioni sul tipo sono disponibili come parte della definizione del set di dati nell'archivio dati, pertanto non è necessario inserirle quando si include la sezione "structure".
- **Per lo schema delle origini dati di lettura, in particolare BLOB di Azure**, è possibile scegliere di archiviare i dati senza aggiungere alcuna informazione sullo schema o sul tipo. Per questi tipi di origini dati è necessario includere la sezione "structure" nei due casi seguenti:
    - Esecuzione del mapping di colonne di origine a colonne del sink.
    - Set di dati definito come origine in un'attività di copia. In questo caso nella sezione "structure" è possibile fornire informazioni sul tipo e Data Factory usa questo tipo di informazioni per la conversione in tipi nativi per il sink. Per altre informazioni, vedere l'articolo sullo [spostamento di dati da e verso BLOB di Azure](data-factory-azure-blob-connector.md).

### <a name="supported-net-based-types"></a>Tipi supportati basati su .NET
Data Factory supporta i valori di tipo basati su .NET conformi a CLS per specificare informazioni sul tipo nella sezione "structure" in relazione allo schema su origini dati di lettura come BLOB di Azure.

- Int16
- Int32
- Int64
- Single
- Double
- Decimale
- Booleano
- String
- Datetime
- Datetimeoffset
- TimeSpan

Per Datetime e Datetimeoffset, è anche possibile specificare la stringa "culture" e "format" per facilitare l'analisi della stringa Datetime personalizzata. Vedere l'esempio di conversione di tipo nella sezione seguente:


[!INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


## <a name="performance-and-tuning"></a>Prestazioni e ottimizzazione  
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati (attività di copia) e sui vari modi per ottimizzarle, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

* [Esercitazione dell'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate della creazione di una pipeline con un'attività di copia.



<!--HONumber=Feb17_HO2-->


