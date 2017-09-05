---
title: Spostare dati da Cassandra con Data Factory | Documentazione Microsoft
description: Informazioni su come spostare dati da un database Cassandra locale mediante Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jingwang
ms.translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 6a48c11508bdc7f6f6fbfe4a504172f9944c93c0
ms.contentlocale: it-it
ms.lasthandoff: 03/29/2017

---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Spostare dati da un database Cassandra locale mediante Azure Data Factory
Questo articolo illustra come usare l'attività di copia in Azure Data Factory per spostare i dati da un database Cassandra locale. Si basa sull'articolo relativo alle [attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con l'attività di copia.

È possibile copiare dati da un archivio dati Cassandra locale a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data Factory supporta attualmente solo lo spostamento di dati da un archivio dati Cassandra ad altri archivi dati, ma non da altri archivi dati a un archivio dati Cassandra. 

## <a name="supported-versions"></a>Versioni supportate
Il connettore Cassandra supporta le versioni seguenti di Cassandra: 2.X.

## <a name="prerequisites"></a>Prerequisiti
Perché il servizio Azure Data Factory possa connettersi al database Cassandra locale, è necessario installare un gateway di gestione dati nello stesso computer che ospita il database o in un computer separato per evitare che competa per le risorse con il database. Il gateway di gestione dati è un componente che connette le origini dati locali ai servizi cloud in modo sicuro e gestito. Leggere l'articolo [Gateway di gestione dati](data-factory-data-management-gateway.md) per i dettagli sul Gateway di gestione dati. Per istruzioni passo per passo su come configurare il gateway di una pipeline di dati per spostare i dati, vedere [Spostare dati tra origini locali e il cloud](data-factory-move-data-between-onprem-and-cloud.md).

È necessario usare il gateway per connettersi a un database Cassandra anche se il database è ospitato nel cloud, ad esempio, in una VM IaaS di Azure. È possibile avere il gateway nella stessa macchina virtuale che ospita il database o in una macchina virtuale diversa, purché il gateway possa connettersi al database.  

Quando si installa il gateway, viene installato automaticamente un driver Microsoft ODBC Cassandra che viene usato per la connessione al database Cassandra. Pertanto, non è necessario installare manualmente i driver nel computer del gateway quando si copiano dati dal database Cassandra. 

> [!NOTE]
> Per suggerimenti sulla risoluzione di problemi correlati alla connessione o al gateway, vedere [Risoluzione dei problemi del gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .

## <a name="getting-started"></a>Introduzione
È possibile creare una pipeline con l'attività di copia che sposta i dati da e verso un archio dati Cassandra usando diversi strumenti/API. 

- Il modo più semplice per creare una pipeline è usare la **Copia guidata**. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati. 
- È possibile anche usare gli strumenti seguenti per creare una pipeline: **portale di Azure**, **Visual Studio**, **Azure PowerShell**, **modello di Azure Resource Manager**, **API .NET** e **API REST**. Vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia. 

Se si usano gli strumenti o le API, eseguire la procedura seguente per creare una pipeline che sposta i dati da un archivio dati di origine a un archivio dati sink:

1. Creare i **servizi collegati** per collegare gli archivi di dati di input e output alla data factory.
2. Creare i **set di dati** per rappresentare i dati di input e di output per le operazioni di copia. 
3. Creare una **pipeline** con un'attività di copia che accetti un set di dati come input e un set di dati come output. 

Quando si usa la procedura guidata, le definizioni JSON per queste entità di data factory (servizi, set di dati e pipeline collegati) vengono create automaticamente. Quando si usano gli strumenti o le API, ad eccezione delle API .NET, usare il formato JSON per definire le entità di data factory.  Per un esempio con le definizioni JSON per le entità di Data Factory utilizzate per copiare dati da un archivio dati Cassandra locale, vedere la sezione [Esempio JSON: Copiare dati da Cassandra a BLOB di Azure](#json-example-copy-data-from-cassandra-to-azure-blob) di questo articolo. 

Nelle sezioni seguenti sono disponibili le informazioni dettagliate sulle proprietà JSON che vengono usate per definire entità della Data Factory specifiche di un archivio dati Cassandra:

## <a name="linked-service-properties"></a>Proprietà del servizio collegato
La tabella seguente contiene le descrizioni degli elementi JSON specifici del servizio collegato Cassandra.

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| type |La proprietà type deve essere impostata su: **OnPremisesCassandra** |Sì |
| host |Uno o più indirizzi IP o nomi host di server Cassandra.<br/><br/>Specificare un elenco delimitato da virgole degli indirizzi IP o nomi host per la connessione a tutti i server contemporaneamente. |Sì |
| port |La porta TCP che il server Cassandra usa per ascoltare le connessioni client. |No, valore predefinito: 9042 |
| authenticationType |Di base o anonima |Sì |
| username |Specificare il nome utente per l'account utente. |Sì, se authenticationType è impostato su Basic. |
| password |Specifica la password per l'account utente. |Sì, se authenticationType è impostato su Basic. |
| gatewayName |Il nome del gateway che viene usato per connettersi al database Cassandra locale. |Sì |
| encryptedCredential |Credenziali crittografate dal gateway. |No |

## <a name="dataset-properties"></a>Proprietà dei set di dati
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo **CassandraTable** presenta le proprietà seguenti

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| keyspace |Nome del keyspace o schema nel database Cassandra. |Sì, se **query** per **CassandraSource** non è definito. |
| tableName |Nome della tabella in un database Cassandra. |Sì, se **query** per **CassandraSource** non è definito. |

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo [Creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

In caso di origine di tipo **CassandraSource**, nella sezione typeProperties sono disponibili le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| query |Usare la query personalizzata per leggere i dati. |Query SQL-92 o query CQL. Vedere il [riferimento a CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Quando si usa una query SQL, specificare **nome keyspace.nome tabella** per indicare la tabella su cui eseguire la query. |No (se tableName e keyspace sul set di dati sono definiti). |
| consistencyLevel |Il livello di coerenza specifica quante repliche devono rispondere a una richiesta di lettura prima della restituzione dei dati all'applicazione client. Cassandra controlla il numero di repliche specificato perché i dati soddisfino la richiesta di lettura. |ONE, TWO, THREE, QUORUM, ALL, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Per informazioni dettagliate, vedere [Configuring data consistency](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) (Configurazione della coerenza dei dati). |No. Il valore predefinito è ONE. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>Esempio JSON: Copiare i dati da Cassandra a BLOB di Azure
Questo esempio fornisce le definizioni JSON campione da usare per creare una pipeline con il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Illustra come copiare dati da un database Cassandra locale a un archivio BLOB di Azure. Tuttavia, i dati possono essere copiati in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Azure Data Factory.

> [!IMPORTANT]
> Questo esempio fornisce frammenti di codice JSON. Non include istruzioni dettagliate per la creazione della data factory. Le istruzioni dettagliate sono disponibili nell'articolo [Spostare dati tra origini locali e il cloud](data-factory-move-data-between-onprem-and-cloud.md) .

L'esempio include le entità di Data factory seguenti:

* Un servizio collegato di tipo [OnPremisesCassandra](#linked-service-properties).
* Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Un [set di dati](data-factory-create-datasets.md) di input di tipo [CassandraTable](#dataset-properties).
* Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [CassandraSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

**Servizio collegato Cassandra:**

Questo esempio usa il servizio collegato **Cassandra** . Per informazioni sulle proprietà supportate da questo servizio collegato, vedere la sezione dedicata al [servizio collegato Cassandra](#linked-service-properties) .  

```json
{
    "name": "CassandraLinkedService",
    "properties":
    {
        "type": "OnPremisesCassandra",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "host": "mycassandraserver",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Servizio collegato Archiviazione di Azure:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

**Set di dati di input Cassandra:**

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "mykeyspace"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
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

Impostando **external** su **true** si comunica al servizio Data Factory che il set di dati è esterno alla data factory e non è prodotto da un'attività al suo interno.

**Set di dati di output del BLOB di Azure:**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/fromcassandra"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Attività di copia in una pipeline con un'origine Cassandra e un sink BLOB:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **CassandraSource** e il tipo di **sink** è impostato su **BlobSink**.

Per l'elenco delle proprietà supportate da RelationalSource, vedere le [proprietà del tipo RelationalSource](#copy-activity-properties) .

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
        {
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "CassandraInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"

                },
                "sink": {
                    "type": "BlobSink"
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

### <a name="type-mapping-for-cassandra"></a>Mapping dei tipi per Cassandra
| Tipo Cassandra | Tipo basato su .Net |
| --- | --- |
| ASCII |String |
| BIGINT |Int64 |
| BLOB |Byte[] |
| BOOLEAN |BOOLEAN |
| DECIMAL |DECIMAL |
| DOUBLE |DOUBLE |
| FLOAT |Single |
| INET |String |
| INT |Int32 |
| TEXT |String |
| TIMESTAMP |DateTime |
| TIMEUUID |Guid |
| UUID |Guid |
| VARCHAR |String |
| VARINT |DECIMAL |

> [!NOTE]
> Per i tipi di raccolta (mappa, set, elenco e così via), vedere la sezione [Uso delle raccolte con una tabella virtuale](#work-with-collections-using-virtual-table) .
>
> I tipi definiti dall'utente non sono supportati.
>
> La lunghezza di una colonna Binary o String non può essere maggiore di 4000.
>
>

## <a name="work-with-collections-using-virtual-table"></a>Uso delle raccolte con una tabella virtuale
Azure Data Factory usa un driver ODBC integrato per connettersi ai dati di un database Cassandra e copiarli. Per i tipi di raccolta, fra cui mappa, set ed elenco, il driver normalizza di nuovo i dati in tabelle virtuali corrispondenti. In particolare, se una tabella contiene colonne della raccolta, il driver genera le tabelle virtuali seguenti:

* Una **tabella di base**che contiene gli stessi dati della tabella reale eccetto le colonne della raccolta. La tabella di base ha lo stesso nome della tabella reale che rappresenta.
* Una **tabella virtuale** per ogni colonna della raccolta che espande i dati nidificati. Alle tabelle virtuali che rappresentano le raccolte vengono assegnati nomi composti dal nome della tabella reale seguito dal separatore "*vt*" e dal nome della colonna.

Le tabelle virtuali fanno riferimento ai dati nella tabella reale, consentendo al driver di accedere ai dati denormalizzati. Per i dettagli vedere la sezione Esempio. È possibile accedere al contenuto delle raccolte Cassandra eseguendo query e join sulle tabelle virtuali.

È possibile usare la [Copia guidata](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) per visualizzare in modo intuitivo l'elenco delle tabelle nel database Cassandra, comprese le tabelle virtuali, e visualizzare in anteprima i dati all'interno. È inoltre possibile costruire una query nella Copia guidata ed eseguire la convalida per visualizzare il risultato.

### <a name="example"></a>Esempio
Ad esempio, "ExampleTable" è una tabella di un database Cassandra contenente una colonna chiave primaria integer denominata "pk_int", una colonna testo denominata value, una colonna elenco, una colonna mappa e una colonna set (denominata "StringSet").

| pk_int | Valore | Elenco | Mappa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"valore di esempio 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"valore di esempio 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Il driver genera più tabelle virtuali per rappresentare questa singola tabella. Le colonne chiave esterna nelle tabelle virtuali fanno riferimento alle colonne chiave primaria nella tabella reale e indicano a quale riga della tabella reale corrisponde la riga della tabella virtuale.

La prima tabella virtuale è la tabella di base denominata "ExampleTable", illustrata nella tabella di seguito. La tabella di base contiene gli stessi dati della tabella di database originale, tranne le raccolte, che vengono omesse da questa tabella ed espanse in altre tabelle virtuali.

| pk_int | Valore |
| --- | --- |
| 1 |"valore di esempio 1" |
| 3 |"valore di esempio 3" |

Le tabelle seguenti illustrano le tabelle virtuali che normalizzano di nuovo i dati dalle colonne elenco, mappa e StringSet. Le colonne con nomi che terminano con “_index” o “_key” indicano la posizione dei dati all'interno dell'elenco o della mappa originale. Le colonne con nomi che terminano con “_value” contengono i dati espansi dalla raccolta.

#### <a name="table-exampletablevtlist"></a>Tabella “ExampleTable_vt_List”:
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletablevtmap"></a>Tabella “ExampleTable_vt_Map”:
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |Una  |
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletablevtstringset"></a>Tabella “ExampleTable_vt_StringSet”:
| pk_int | StringSet_value |
| --- | --- |
| 1 |Una  |
| 1 |b |
| 1 |C |
| 3 |Una  |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>Eseguire il mapping delle colonne dell'origine alle colonne del sink
Per informazioni sul mapping delle colonne del set di dati di origine alle colonne del set di dati del sink, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Lettura ripetibile da origini relazionali
Quando si copiano dati da archivi dati relazionali, è necessario tenere presente la ripetibilità per evitare risultati imprevisti. In Azure Data Factory è possibile rieseguire una sezione manualmente. È anche possibile configurare i criteri di ripetizione per un set di dati in modo da rieseguire una sezione in caso di errore. Quando una sezione viene rieseguita in uno dei due modi, è necessario assicurarsi che non vengano letti gli stessi dati, indipendentemente da quante volte viene eseguita la sezione. Vedere [Lettura ripetibile da origini relazionali](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Ottimizzazione delle prestazioni
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

