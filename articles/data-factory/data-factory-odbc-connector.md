---
title: Spostare dati da archivi dati ODBC | Documentazione Microsoft
description: Informazioni su come spostare dati da archivi dati ODBC con Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: ad70a598-c031-4339-a883-c6125403cb76
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: jingwang
ms.translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 8b7e60201fdb67a00bbdce03fbed00679de45a2c
ms.contentlocale: it-it
ms.lasthandoff: 03/29/2017


---
# <a name="move-data-from-odbc-data-stores-using-azure-data-factory"></a>Spostare dati da archivi dati ODBC con Azure Data Factory
Questo articolo illustra come usare l'attività di copia in Azure Data Factory per spostare i dati da un archivio dati ODBC locale. Si basa sull'articolo relativo alle [attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con l'attività di copia.

È possibile copiare dati da un archivio dati ODBC a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data Factory supporta attualmente solo lo spostamento dei dati da un archivio dati ODBC ad altri archivi dati, ma non da altri archivi dati a un archivio dati ODBC. 

## <a name="enabling-connectivity"></a>Abilitazione della connettività
Il servizio Data Factory supporta la connessione a origini ODBC locali tramite il Gateway di gestione dati. Vedere l'articolo sullo [spostamento di dati tra sedi locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) per informazioni sul Gateway di gestione dati e per istruzioni dettagliate sulla configurazione del gateway. Usare il gateway per connettersi a un archivio dati ODBC anche se è ospitato in una macchina virtuale IaaS di Azure.

È possibile installare il gateway nello stesso computer locale o macchina virtuale di Azure come archivio dati ODBC. Tuttavia, è consigliabile installare il gateway in un computer/macchina virtuale Iaas di Azure separati per evitare conflitti tra le risorse e ottenere prestazioni migliori. Quando si installa il gateway in un computer separato, questo deve poter accedere al computer l'archivio dati ODBC.

Oltre a Gateway di gestione dati, è necessario installare anche il driver ODBC per l'archivio dati nel computer del gateway.

> [!NOTE]
> Per suggerimenti sulla risoluzione di problemi correlati alla connessione o al gateway, vedere [Risoluzione dei problemi del gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .

## <a name="getting-started"></a>Introduzione
È possibile creare una pipeline con l'attività di copia che sposta i dati da un archivio dati ODBC usando diversi strumenti/API.

Il modo più semplice per creare una pipeline è usare la **Copia guidata**. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

È possibile anche usare gli strumenti seguenti per creare una pipeline: **portale di Azure**, **Visual Studio**, **Azure PowerShell**, **modello di Azure Resource Manager**, **API .NET** e **API REST**. Vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia. 

Se si usano gli strumenti o le API, eseguire la procedura seguente per creare una pipeline che sposta i dati da un archivio dati di origine a un archivio dati sink: 

1. Creare i **servizi collegati** per collegare gli archivi di dati di input e output alla data factory.
2. Creare i **set di dati** per rappresentare i dati di input e di output per le operazioni di copia. 
3. Creare una **pipeline** con un'attività di copia che accetti un set di dati come input e un set di dati come output. 

Quando si usa la procedura guidata, le definizioni JSON per queste entità di data factory (servizi, set di dati e pipeline collegati) vengono create automaticamente. Quando si usano gli strumenti o le API, ad eccezione delle API .NET, usare il formato JSON per definire le entità di data factory.  Per un esempio con definizioni JSON per entità di data factory utilizzate per copiare dati da un archivio dati ODBC, vedere la sezione [Esempio JSON: Copiare dati da un archivio dati ODBC al BLOB di Azure](#json-example-copy-data-from-odbc-data-store-to-azure-blob) di questo articolo. 

Nelle sezioni seguenti sono riportate le informazioni dettagliate sulle proprietà JSON che vengono usate per definire entità di data factory specifiche di un archivio dati ODBC:

## <a name="linked-service-properties"></a>Proprietà del servizio collegato
La tabella seguente contiene le descrizioni degli elementi JSON specifici del servizio collegato ODBC.

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| type |La proprietà type deve essere impostata su: **OnPremisesOdbc** |Sì |
| connectionString |La parte delle credenziali non di accesso della stringa di connessione e una credenziale crittografata facoltativa. Vedere gli esempi nelle sezioni seguenti. |Sì |
| credential |La parte delle credenziali di accesso della stringa di connessione specificata nel formato di valore della proprietà specifico del driver. Esempio: "Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;". |No |
| authenticationType |Tipo di autenticazione usato per connettersi all'archivio dati ODBC. I valori possibili sono: anonima e di base. |Sì |
| username |Specificare il nome utente se si usa l'autenticazione di base. |No |
| password |Specificare la password per l'account utente specificato per il nome utente. |No |
| gatewayName |Nome del gateway che il servizio Data Factory deve usare per connettersi all'archivio dati ODBC. |Sì |

### <a name="using-basic-authentication"></a>Uso dell'autenticazione di base

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```
### <a name="using-basic-authentication-with-encrypted-credentials"></a>Uso dell'autenticazione di base con credenziali crittografate
È possibile crittografare le credenziali usando il cmdlet [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx), in Azure PowerShell versione 1.0, oppure [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx), in Azure PowerShell versione 0.9 o precedente.  

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>Uso dell'autenticazione anonima

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "mygateway"
        }
    }
}
```


## <a name="dataset-properties"></a>Proprietà dei set di dati
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo **RelationalTable** , che include il set di dati ODBC, presenta le proprietà seguenti

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| tableName |Nome della tabella nell'archivio dati ODBC. |Sì |

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo [Creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

D'altra parte, le proprietà disponibili nella sezione **typeProperties** dell'attività variano in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

Nell'attività di copia con origine di tipo **RelationalSource** , incluso ODBC, sono disponibili le proprietà seguenti nella sezione typeProperties:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| query |Usare la query personalizzata per leggere i dati. |Stringa di query SQL. Ad esempio: selezionare * da MyTable. |Sì |


## <a name="json-example-copy-data-from-odbc-data-store-to-azure-blob"></a>Esempio JSON: Copiare dati da un archivio dati ODBC al BLOB di Azure
Questo esempio fornisce le definizioni JSON da usare per creare una pipeline con il [Portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Illustra come copiare dati da un'origine ODBC in un archivio BLOB di Azure. Tuttavia, i dati possono essere copiati in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Azure Data Factory.

L'esempio include le entità di Data factory seguenti:

1. Un servizio collegato di tipo [OnPremisesOdbc](#linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [RelationalTable](#dataset-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

L'esempio copia i dati dai risultati della query in un archivio dati ODBC a un BLOB ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

Come primo passaggio, impostare il gateway di gestione dati. Le istruzioni sono disponibili nell'articolo [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) .

**Servizio collegato ODBC** : questo esempio usa l'autenticazione di base. Per i diversi tipi di autenticazione disponibili, vedere la sezione [Servizio collegato ODBC](#linked-service-properties) .

```json
{
    "name": "OnPremOdbcLinkedService",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Servizio collegato Archiviazione di Azure**

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

**Set di dati di input ODBC**

L'esempio presuppone che sia stata creata una tabella "MyTable" in un archivio dati ODBC e che contenga una colonna denominata "timestampcolumn" per i dati di una serie temporale.

Impostando "external" su "true" si comunica al servizio Data Factory che il set di dati è esterno alla data factory e non è prodotto da un'attività al suo interno.

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremOdbcLinkedService",
        "typeProperties": {},
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

**Set di dati di output del BLOB di Azure**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

```json
{
    "name": "AzureBlobOdbcDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
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


**Attività di copia in una pipeline con origine ODBC (RelationalSource) e sink BLOB (BlobSink)**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **RelationalSource** e il tipo di **sink** è impostato su **BlobSink**. La query SQL specificata per la proprietà **query** consente di selezionare i dati da copiare nell'ultima ora.

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "OdbcDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOdbcDataSet"
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
                "name": "OdbcToBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-odbc"></a>Mapping dei tipi per ODBC
Come accennato nell'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md) , l'attività di copia esegue conversioni di tipi automatiche da tipi di origine a tipi di sink con l'approccio seguente in due passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Quando si spostano dati da archivi dati ODBC, viene eseguito il mapping dei tipi di dati ODBC ai tipi .NET, come riportato nell'argomento [Mapping dei tipi di dati ODBC](https://msdn.microsoft.com/library/cc668763.aspx) .

## <a name="map-source-to-sink-columns"></a>Eseguire il mapping delle colonne dell'origine alle colonne del sink
Per informazioni sul mapping delle colonne del set di dati di origine alle colonne del set di dati del sink, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Lettura ripetibile da origini relazionali
Quando si copiano dati da archivi dati relazionali, è necessario tenere presente la ripetibilità per evitare risultati imprevisti. In Azure Data Factory è possibile rieseguire una sezione manualmente. È anche possibile configurare i criteri di ripetizione per un set di dati in modo da rieseguire una sezione in caso di errore. Quando una sezione viene rieseguita in uno dei due modi, è necessario assicurarsi che non vengano letti gli stessi dati, indipendentemente da quante volte viene eseguita la sezione. Vedere [Lettura ripetibile da origini relazionali](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="ge-historian-store"></a>Archivio GE Historian
Creare un servizio collegato ODBC per collegare un archivio dati [GE Proficy Historian (ora GE Historian)](http://www.geautomation.com/products/proficy-historian) a un'istanza di Azure Data Factory, come illustrato nell'esempio seguente:

```json
{
    "name": "HistorianLinkedService",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "connectionString": "DSN=<name of the GE Historian store>",
            "gatewayName": "<gateway name>",
            "authenticationType": "Basic",
            "userName": "<user name>",
            "password": "<password>"
        }
    }
}
```

Installare il Gateway di gestione dati in un computer locale e registrare il gateway con il portale. Il gateway installato nel computer locale usa il driver ODBC per GE Historian per la connessione all'archivio dati GE Historian. Installare quindi il driver se non è già installato nel computer del gateway. Per i dettagli, vedere [Abilitazione della connettività](#enabling-connectivity) .

Prima di usare l'archivio GE Historian in una soluzione Data Factory, verificare se il gateway è in grado di connettersi all'archivio dati usando le istruzioni indicate nella sezione successiva.

Leggere l'articolo dall'inizio per una panoramica dettagliata dell'uso degli archivi dati ODBC come archivi dati di origine in un'operazione di copia.  

## <a name="troubleshoot-connectivity-issues"></a>Risoluzione dei problemi di connettività
Per risolvere i problemi di connessione, usare la scheda **Diagnostica** di **Gestione configurazione di Gateway di gestione dati**.

1. Avviare **Gestione configurazione di Gateway di gestione dati**. È possibile eseguire direttamente "C:\Programmi\Microsoft Data Management Gateway\1.0\Shared\ConfigManager.exe" o eseguire una ricerca di **Gateway** per trovare un collegamento all'applicazione **Gateway di gestione dati di Microsoft**, come mostrato nell'immagine seguente.

    ![Ricerca nel gateway](./media/data-factory-odbc-connector/search-gateway.png)
2. Passare alla scheda **Diagnostica** .

    ![Diagnostica del gateway](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png)
3. Selezionare il **tipo** di dati archiviati (servizio collegato).
4. Specificare l'**autenticazione** e immettere le **credenziali** o la **stringa di connessione** usati per la connessione all'archivio dati.
5. Fare clic su **Test connessione** per testare la connessione all'archivio dati.

## <a name="performance-and-tuning"></a>Ottimizzazione delle prestazioni
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

