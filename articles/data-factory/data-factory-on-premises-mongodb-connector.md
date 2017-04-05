---
title: Spostare dati da MongoDB con Data Factory | Documentazione Microsoft
description: Informazioni su come spostare i dati dal database di MongoDB con Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 10ca7d9a-7715-4446-bf59-2d2876584550
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 3540e9c151890468be028af7224a6d11045aec6b
ms.lasthandoff: 03/29/2017


---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Spostare i dati da MongoDB con Azure Data Factory
Questo articolo illustra come usare l'attività di copia in Azure Data Factory per spostare i dati da un database MongoDB locale. Si basa sull'articolo relativo alle [attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con l'attività di copia.

È possibile copiare dati da un archivio dati MongoDB locale a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data Factory supporta attualmente solo lo spostamento dei dati da un archivio dati MongoDB ad altri archivi dati, ma non da altri archivi dati a un archivio dati MongoDB. 

## <a name="prerequisites"></a>Prerequisiti
Per consentire al servizio Azure Data Factory di connettersi al database MongoDB in locale, è necessario installare i componenti seguenti:

- Le versioni MongoDB supportate sono 2.4, 2.6, 3.0 e 3.2.
- Gateway di gestione dati nello stesso computer che ospita il database o in un computer separato per evitare che competa per le risorse con il database. Il Gateway di gestione dati è un software che connette le origini dati locali ai servizi cloud in modo sicuro e gestito. Leggere l'articolo [Gateway di gestione dati](data-factory-data-management-gateway.md) per i dettagli sul Gateway di gestione dati. Per istruzioni passo per passo su come configurare il gateway di una pipeline di dati per spostare i dati, vedere [Spostare dati tra origini locali e il cloud](data-factory-move-data-between-onprem-and-cloud.md).

    Quando si installa il gateway, viene installato automaticamente un driver ODBC MongoDB Microsoft che viene usato per la connessione a MongoDB.

    > [!NOTE]
    > È necessario usare il gateway per connettersi a MongoDB anche se è ospitato in VM IaaS di Azure. Se si sta provando a connettersi a un'istanza di MongoDB ospitata nel cloud è anche possibile installare l'istanza del gateway nella macchina virtuale IaaS.

## <a name="getting-started"></a>Introduzione
È possibile creare una pipeline con l'attività di copia che sposta i dati da e verso un archivio dati MongoDB usando diversi strumenti/API.

Il modo più semplice per creare una pipeline è usare la **Copia guidata**. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

È possibile anche usare gli strumenti seguenti per creare una pipeline: **portale di Azure**, **Visual Studio**, **Azure PowerShell**, **modello di Azure Resource Manager**, **API .NET** e **API REST**. Vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia. 

Se si usano gli strumenti o le API, eseguire la procedura seguente per creare una pipeline che sposta i dati da un archivio dati di origine a un archivio dati sink: 

1. Creare i **servizi collegati** per collegare gli archivi di dati di input e output alla data factory.
2. Creare i **set di dati** per rappresentare i dati di input e di output per le operazioni di copia. 
3. Creare una **pipeline** con un'attività di copia che accetti un set di dati come input e un set di dati come output. 

Quando si usa la procedura guidata, le definizioni JSON per queste entità di data factory (servizi, set di dati e pipeline collegati) vengono create automaticamente. Quando si usano gli strumenti o le API, ad eccezione delle API .NET, usare il formato JSON per definire le entità di data factory.  Per un esempio con definizioni JSON per entità di data factory utilizzate per copiare dati da un archivio dati MongoDB locale, vedere la sezione [Esempio JSON: Copiare dati da MongoDB al BLOB di Azure](#json-example-copy-data-from-mongodb-to-azure-blob) di questo articolo. 

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà JSON che vengono usate per definire entità di data factory specifiche dell'origine MongoDB:

## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>Esempio JSON: Copiare dati da MongoDB al BLOB di Azure
Questo esempio fornisce le definizioni JSON di esempio da usare per creare una pipeline con il [Portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Illustra come copiare dati da un MongoDB locale a un'archiviazione BLOB di Azure. Tuttavia, i dati possono essere copiati in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Azure Data Factory.

L'esempio include le entità di Data factory seguenti:

1. Un servizio collegato di tipo [OnPremisesMongoDb](#linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [MongoDbCollection](#dataset-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [MongoDbSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

L'esempio copia i dati dai risultati della query nel database MongoDB in un BLOB ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

Per prima cosa, impostare il Gateway di gestione dati in base alle istruzioni contenute nell'articolo [Gateway di gestione dati](data-factory-data-management-gateway.md) .

**Servizio collegato MongoDB:**

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties":
    {
        "type": "OnPremisesMongoDb",
        "typeProperties":
        {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",  
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
           "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<mygateway>"
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

**Set di dati di input MongoDB:** impostando "external": "true" si comunica al servizio Data Factory che la tabella è esterna alla data factory e non è prodotta da un'attività al suo interno.

```json
{
     "name":  "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"    
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Set di dati di output del BLOB di Azure:**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "%M"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "%d"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "%H"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Attività di copia in una pipeline con un'origine MongoDB e un sink BLOB:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output precedenti. È programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo **source** è impostato su **MongoDbSource** e il tipo **sink** è impostato su **BlobSink**. La query SQL specificata per la proprietà **query** consente di selezionare i dati da copiare nell'ultima ora.

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "MongoDbSource",
                        "query": "$$Text.Format('select * from  MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "MongoDbInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MongoDBToAzureBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```

## <a name="linked-service-properties"></a>Proprietà del servizio collegato
La tabella seguente fornisce la descrizione degli elementi JSON specifici del servizio collegato **OnPremisesMongoDB** .

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| type |La proprietà type deve essere impostata su: **OnPremisesMongoDb** |Sì |
| server |Indirizzo IP o nome host del server MongoDB. |Sì |
| port |Porta TCP che il server MongoDB usa per ascoltare le connessioni client. |Facoltativo, valore predefinito: 27017 |
| authenticationType |Di base o anonima. |Sì |
| username |Account utente per accedere a MongoDB. |Sì (se si usa l'autenticazione di base). |
| password |Password per l'utente. |Sì (se si usa l'autenticazione di base). |
| authSource |Nome del database MongoDB che si vuole usare per controllare le credenziali di autenticazione. |Facoltativo (se si usa l'autenticazione di base). Predefinito: usa l'account di amministrazione e il database specificato usando la proprietà databaseName. |
| databaseName |Nome del database MongoDB a cui si vuole accedere. |Sì |
| gatewayName |Nome del gateway che accede all'archivio dati. |Sì |
| encryptedCredential |Credenziali crittografate in base al gateway. |Facoltativo |

## <a name="dataset-properties"></a>Proprietà dei set di dati
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo **MongoDbCollection** presenta le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| collectionName |Nome della raccolta nel database MongoDB. |Sì |

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo [Creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

D'altra parte, le proprietà disponibili nella sezione **typeProperties** dell'attività variano in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

In caso di origine di tipo **MongoDbSource** , nella sezione typeProperties sono disponibili le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| query |Usare la query personalizzata per leggere i dati. |Stringa di query SQL-92. Ad esempio: selezionare * da MyTable. |No, se **collectionName** di **set di dati** è specificato |

## <a name="schema-by-data-factory"></a>Schema da Data Factory
Il servizio Azure Data Factory deduce lo schema da una raccolta MongoDB usando gli ultimi 100 documenti nella raccolta. Se questi 100 documenti non contengono lo schema completo, alcune colonne possono essere ignorate durante l'operazione di copia.

## <a name="type-mapping-for-mongodb"></a>Mapping dei tipi per MongoDB
Come accennato nell'articolo sulle [attività di spostamento dei dati](data-factory-data-movement-activities.md) , l'attività di copia esegue conversioni automatiche da tipi di origine a tipi di sink con l'approccio seguente in 2 passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Quando si spostano i dati in MongoDB vengono usati i mapping seguenti dai tipi MongoDB ai tipi .NET.

| Tipo di MongoDB | Tipo di .NET Framework |
| --- | --- |
| Binary |Byte[] |
| Boolean |Boolean |
| Date |DateTime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |String |
| String |String |
| UUID |Guid |
| Oggetto |Rinormalizzato in colonne rese flat con "_" come separatore annidato |

> [!NOTE]
> Per informazioni sul supporto di matrici che usano tabelle virtuali, vedere la sezione [Supporto per tipi complessi con tabelle virtuali](#support-for-complex-types-using-virtual-tables) qui di seguito.

I tipi di dati MongoDB seguenti non sono attualmente supportati: DBPointer, JavaScript, chiave Max/Min, Espressione regolare, Simbolo, Timestamp, Undefined

## <a name="support-for-complex-types-using-virtual-tables"></a>Supporto per tipi complessi con tabelle virtuali
Azure Data Factory usa un driver ODBC integrato per connettersi ai dati di un database MongoDB e copiarli. Per i tipi complessi come le matrici o gli oggetti con tipi diversi tra i documenti, il driver rinormalizza i dati nelle tabelle virtuali corrispondenti. In particolare, se una tabella contiene tali colonne, il driver genera le tabelle virtuali seguenti:

* Una **tabella di base**che contiene gli stessi dati della tabella reale eccetto le colonne di tipo complesso. La tabella di base ha lo stesso nome della tabella reale che rappresenta.
* Una **tabella virtuale** per ogni colonna di tipo complesso che espande i dati annidati. Alle tabelle virtuali vengono assegnati nomi composti dal nome della tabella reale seguito dal separatore "_" e dal nome della matrice o dell'oggetto.

Le tabelle virtuali fanno riferimento ai dati nella tabella reale, consentendo al driver di accedere ai dati denormalizzati. Per informazioni dettagliate, vedere la sezione riportata di seguito. È possibile accedere al contenuto delle matrici MongoDB eseguendo query e join sulle tabelle virtuali.

È possibile usare la [Copia guidata](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) per visualizzare intuitivamente l'elenco delle tabelle nel database MongoDB che includono tabelle virtuali e visualizzare in anteprima i dati all'interno. È inoltre possibile costruire una query nella Copia guidata ed eseguire la convalida per visualizzare il risultato.

### <a name="example"></a>Esempio
Ad esempio, "ExampleTable" di seguito è una tabella MongoDB che contiene una colonna con una matrice di oggetti in ogni cella (Fatture) e una colonna con una matrice di tipi scalari (Classificazioni).

| _id | Nome del cliente | Fatture | Contratto | Classificazioni |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", item:"toaster", price:"456", discount:"0.2"}, {invoice_id:"124", item:"oven", price: "1235", discount: "0.2"}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", item:"fridge", price: "12543", discount: "0.0"}] |Gold |[1,2] |

Il driver genera più tabelle virtuali per rappresentare questa singola tabella. La prima tabella virtuale è la tabella di base denominata "ExampleTable", illustrata di seguito. La tabella di base contiene tutti i dati della tabella originale, ma i dati dalle matrici sono stati omessi e vengono espansi nelle tabelle virtuali.

| _id | Nome del cliente | Contratto |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Gold |

Le tabelle seguenti illustrano le tabelle virtuali che rappresentano le matrici originali nell'esempio. In queste tabelle è possibile vedere:

* Un riferimento alla colonna della chiave primaria originale corrispondente alla riga della matrice originale (con la colonna _id)
* Un'indicazione della posizione dei dati all'interno della matrice originale
* I dati espansi per ogni elemento all'interno della matrice

Tabella "ExampleTable_Invoices":

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | price | Discount |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |toaster |456 |0,2 |
| 1111 |1 |124 |oven |1235 |0,2 |
| 2222 |0 |135 |fridge |12543 |0.0 |

Tabella "ExampleTable_Ratings":

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Eseguire il mapping delle colonne dell'origine alle colonne del sink
Per informazioni sul mapping delle colonne del set di dati di origine alle colonne del set di dati del sink, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Lettura ripetibile da origini relazionali
Quando si copiano dati da archivi dati relazionali, è necessario tenere presente la ripetibilità per evitare risultati imprevisti. In Azure Data Factory è possibile rieseguire una sezione manualmente. È anche possibile configurare i criteri di ripetizione per un set di dati in modo da rieseguire una sezione in caso di errore. Quando una sezione viene rieseguita in uno dei due modi, è necessario assicurarsi che non vengano letti gli stessi dati, indipendentemente da quante volte viene eseguita la sezione. Vedere [Lettura ripetibile da origini relazionali](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Ottimizzazione delle prestazioni
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) per istruzioni dettagliate sulla creazione di una pipeline di dati che sposta i dati da un archivio dati locale a un archivio dati di Azure.

