<properties 
    pageTitle="Spostare dati da MongoDB con Data Factory | Microsoft Azure" 
    description="Informazioni su come spostare i dati dal database di MongoDB con Azure Data Factory." 
    services="data-factory" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/04/2016" 
    ms.author="jingwang"/>


# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Spostare i dati da MongoDB con Azure Data Factory

Questo articolo illustra come usare l'attività di copia in un'istanza di Azure Data Factory per spostare dati da un database MongoDB locale a un altro archivio dati. Questo articolo si basa sull'articolo relativo alle [attività di spostamento dati](data-factory-data-movement-activities.md) , che offre una panoramica generale dello spostamento dei dati con l'attività di copia e le combinazioni di archivio dati di origine/sink supportate dall'attività di copia.

Data factory supporta la connessione a origini MongoDB locali con il Gateway di gestione dati. Vedere l'articolo [Gateway di gestione dati](data-factory-data-management-gateway.md) per informazioni sul Gateway di gestione dati e l'articolo [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) per istruzioni dettagliate su come configurare un gateway e una pipeline di dati per spostare i dati. 

> [AZURE.NOTE] È necessario usare il gateway per connettersi a MongoDB anche se è ospitato in VM IaaS di Azure. Se si sta provando a connettersi a un'istanza di MongoDB ospitata nel cloud è anche possibile installare l'istanza del gateway nella macchina virtuale IaaS.

Data Factory supporta attualmente solo lo spostamento di dati da MongoDB ad altri archivi dati, non da altri archivi dati a MongoDB.

## <a name="prerequisites"></a>Prerequisiti
Per consentire al servizio Azure Data Factory di connettersi al database MongoDB in locale, è necessario installare i componenti seguenti: 

- Gateway di gestione dati 2.0 o versione successiva nello stesso computer che ospita il database o in un computer separato per evitare che competa per le risorse con il database. Il Gateway di gestione dati è un software che connette le origini dati locali ai servizi cloud in modo sicuro e gestito. Leggere l'articolo [Gateway di gestione dati](data-factory-data-management-gateway.md) per i dettagli sul Gateway di gestione dati.
  
    Quando si installa il gateway, viene installato automaticamente un driver ODBC MongoDB Microsoft che viene usato per la connessione a MongoDB. 

## <a name="copy-data-wizard"></a>Copia dati guidata
Il modo più semplice per creare una pipeline che copia i dati da un database MongoDB in uno degli archivi dati sink supportati è la procedura guidata per la copia dei dati. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati. 

L'esempio seguente fornisce le definizioni JSON campione da usare per creare una pipeline con il [Portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Illustrano come copiare dati da un database MongoDB in un archivio BLOB di Azure. Tuttavia, i dati possono essere copiati in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores) usando l'attività di copia in Azure Data Factory.

## <a name="sample:-copy-data-from-mongodb-to-azure-blob"></a>Esempio: Copiare i dati da MongoDB a BLOB di Azure
Questo esempio illustra come copiare dati da un database MongoDB locale a un'archiviazione BLOB di Azure. Tuttavia, i dati possono essere copiati **direttamente** in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores) usando l'attività di copia in Azure Data Factory.  
 
L'esempio include le entità di Data Factory seguenti:

1.  Un servizio collegato di tipo [OnPremisesMongoDb](#linked-service-properties).
2.  Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  Un [set di dati](data-factory-create-datasets.md) di input di tipo [MongoDbCollection](#dataset-type-properties).
4.  Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [MongoDbSource](#copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L'esempio copia i dati dai risultati della query nel database MongoDB in un BLOB ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi. 

Per prima cosa, impostare il Gateway di gestione dati in base alle istruzioni contenute nell'articolo [Gateway di gestione dati](data-factory-data-management-gateway.md) . 

**Servizio collegato MongoDB**

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


**Servizio collegato Archiviazione di Azure**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Set di dati di input MongoDB** Impostando "external": "true" si comunica al servizio Data Factory che la tabella è esterna a Data Factory e non è prodotta da un'attività al suo interno.
    
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



**Set di dati di output del BLOB di Azure**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

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



**Pipeline con attività di copia**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output precedenti. È programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo **source** è impostato su **MongoDbSource** e il tipo **sink** è impostato su **BlobSink**. La query SQL specificata per la proprietà **query** consente di selezionare i dati da copiare nell'ultima ora.
    
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


## <a name="linked-service-properties"></a>Proprietà del servizio collegato

La tabella seguente fornisce la descrizione degli elementi JSON specifici del servizio collegato **OnPremisesMongoDB** .

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- | 
| type | La proprietà type deve essere impostata su: **OnPremisesMongoDb** | Sì |
| server | Indirizzo IP o nome host del server MongoDB. | Sì | 
| port | Porta TCP che il server MongoDB usa per ascoltare le connessioni client. | Facoltativo, valore predefinito: 27017 |
| authenticationType | Di base o anonima. | Sì | 
| username | Account utente per accedere a MongoDB. | Sì (se si usa l'autenticazione di base).|
| password | Password per l'utente. |   Sì (se si usa l'autenticazione di base). | 
| authSource | Nome del database MongoDB che si vuole usare per controllare le credenziali di autenticazione. | Facoltativo (se si usa l'autenticazione di base). Predefinito: usa l'account di amministrazione e il database specificato usando la proprietà databaseName. |  
| databaseName | Nome del database MongoDB a cui si vuole accedere. | Sì |
| gatewayName | Nome del gateway che accede all'archivio dati. | Sì | 
| encryptedCredential | Credenziali crittografate in base al gateway. | Facoltativo |


Per informazioni dettagliate sull'impostazione delle credenziali per un'origine dati MongoDB locale, vedere [Impostazione delle credenziali e della sicurezza](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

## <a name="dataset-type-properties"></a>Proprietà del tipo di set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo **MongoDbCollection** presenta le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| collectionName | Nome della raccolta nel database MongoDB. | Sì | 

## <a name="copy-activity-type-properties"></a>Proprietà del tipo di attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo [Creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri. 

D'altra parte, le proprietà disponibili nella sezione **typeProperties** dell'attività variano in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

In caso di origine di tipo **MongoDbSource** , nella sezione typeProperties sono disponibili le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| query | Usare la query personalizzata per leggere i dati. | Stringa di query SQL-92. Ad esempio: selezionare * da MyTable. | No, se **collectionName** di **set di dati** è specificato | 

## <a name="schema-by-data-factory"></a>Schema da Data Factory
Il servizio Azure Data Factory deduce lo schema da una raccolta MongoDB usando gli ultimi 100 documenti nella raccolta. Se questi 100 documenti non contengono lo schema completo, alcune colonne possono essere ignorate durante l'operazione di copia. 

## <a name="type-mapping-for-mongodb"></a>Mapping dei tipi per MongoDB

Come accennato nell'articolo sulle [attività di spostamento dei dati](data-factory-data-movement-activities.md) , l'attività di copia esegue conversioni automatiche da tipi di origine a tipi di sink con l'approccio seguente in 2 passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Quando si spostano i dati in MongoDB vengono usati i mapping seguenti dai tipi MongoDB ai tipi .NET.

| Tipo di MongoDB | Tipo di .NET Framework |
| ------------------- | ------------------- | 
| Binary | Byte[] |
| Boolean | Boolean |
| Date | DateTime |
| NumberDouble | Double |
| NumberInt | Int32 |
| NumberLong | Int64 |
| ObjectID | String |
| String | String |
| UUID | Guid | 
| Oggetto | Rinormalizzato in colonne rese flat con "_" come separatore annidato |

> [AZURE.NOTE]  
> Per informazioni sul supporto di matrici che usano tabelle virtuali, vedere la sezione [Supporto per tipi complessi con tabelle virtuali](#support-for-complex-types-using-virtual-tables) qui di seguito. 

I tipi di dati MongoDB seguenti non sono attualmente supportati: DBPointer, JavaScript, chiave Max/Min, Espressione regolare, Simbolo, Timestamp, Undefined

## <a name="support-for-complex-types-using-virtual-tables"></a>Supporto per tipi complessi con tabelle virtuali
Azure Data Factory usa un driver ODBC integrato per connettersi ai dati di un database MongoDB e copiarli. Per i tipi complessi come le matrici o gli oggetti con tipi diversi tra i documenti, il driver rinormalizza i dati nelle tabelle virtuali corrispondenti. In particolare, se una tabella contiene tali colonne, il driver genera le tabelle virtuali seguenti:

-   Una **tabella di base**che contiene gli stessi dati della tabella reale eccetto le colonne di tipo complesso. La tabella di base ha lo stesso nome della tabella reale che rappresenta.
-   Una **tabella virtuale** per ogni colonna di tipo complesso che espande i dati annidati. Alle tabelle virtuali vengono assegnati nomi composti dal nome della tabella reale seguito dal separatore "_" e dal nome della matrice o dell'oggetto.

Le tabelle virtuali fanno riferimento ai dati nella tabella reale, consentendo al driver di accedere ai dati denormalizzati. Per informazioni dettagliate, vedere la sezione riportata di seguito. È possibile accedere al contenuto delle matrici MongoDB eseguendo query e join sulle tabelle virtuali.

È possibile usare la [Copia guidata](data-factory-data-movement-activities.md#data-factory-copy-wizard) per visualizzare intuitivamente l'elenco delle tabelle nel database MongoDB che includono tabelle virtuali e visualizzare in anteprima i dati all'interno. È inoltre possibile costruire una query nella Copia guidata ed eseguire la convalida per visualizzare il risultato.

### <a name="example"></a>Esempio

Ad esempio, "ExampleTable" di seguito è una tabella MongoDB che contiene una colonna con una matrice di oggetti in ogni cella (Fatture) e una colonna con una matrice di tipi scalari (Classificazioni). 

_id | Nome del cliente | Fatture | Contratto | Classificazioni
--- | ------------- | -------- | ------------- | -------
1111 | ABC | [{invoice_id:"123", item:"toaster", price:"456", discount:"0.2"}, {invoice_id:"124", item:"oven", price: "1235", discount: "0.2"}] | Silver | [5,6]
2222 | XYZ | [{invoice_id:"135", item:"fridge", price: "12543", discount: "0.0"}] | Gold | [1,2]

Il driver genera più tabelle virtuali per rappresentare questa singola tabella. La prima tabella virtuale è la tabella di base denominata "ExampleTable", illustrata di seguito. La tabella di base contiene tutti i dati della tabella originale, ma i dati dalle matrici sono stati omessi e vengono espansi nelle tabelle virtuali.

_id | Nome del cliente | Contratto
--- | ------------- | -------------
1111 | ABC | Silver
2222 | XYZ | Gold

Le tabelle seguenti illustrano le tabelle virtuali che rappresentano le matrici originali nell'esempio. In queste tabelle è possibile vedere: 

- Un riferimento alla colonna della chiave primaria originale corrispondente alla riga della matrice originale (con la colonna _id)
- Un'indicazione della posizione dei dati all'interno della matrice originale 
- I dati espansi per ogni elemento all'interno della matrice

Tabella "ExampleTable_Invoices":

_id | ExampleTable_Invoices_dim1_idx | invoice_id | item | price | Discount
--- | -------------- | ---------- | ---- | ----- | --------
1111 | 0 | 123 | toaster | 456 | 0,2
1111 | 1 | 124 | oven | 1235 | 0,2
2222 | 0 | 135 | fridge | 12543 | 0.0

Tabella "ExampleTable_Ratings":

_id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings
--- | ------------- | -------------
1111 | 0 | 5
1111 | 1 | 6
2222 | 0 | 1
2222 | 1 | 2

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Ottimizzazione delle prestazioni  
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) per istruzioni dettagliate sulla creazione di una pipeline di dati che sposta i dati da un archivio dati locale a un archivio dati di Azure. 




<!--HONumber=Oct16_HO2-->


