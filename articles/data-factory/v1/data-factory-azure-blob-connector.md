---
title: Copiare dati in e da un archivio BLOB di Azure | Microsoft Docs
description: "Informazioni su come copiare dati BLOB in Azure Data Factory. Usare l'esempio: Come copiare dati da e verso l'archivio BLOB di Azure e il database SQL di Azure."
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fe7b419a2f4bbc3ae4aa69dad1e3b3e0957cc848
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Copiare i dati da e in Archiviazione BLOB di Azure mediante Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](data-factory-azure-blob-connector.md)
> * [Versione 2 - Anteprima](../connector-azure-blob-storage.md)

> [!NOTE]
> Questo articolo si applica alla versione 1 del servizio Data Factory, disponibile a livello generale (GA). Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere [Connettore di archiviazione BLOB di Azure nella versione 2](../connector-azure-blob-storage.md).


Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare i dati da e in Archiviazione BLOB di Azure. Si basa sull'articolo relativo alle [attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con l'attività di copia.

## <a name="overview"></a>Panoramica
È possibile copiare i dati da qualsiasi archivio dati di origine supportato all'archivio BLOB di Azure o dall'archivio BLOB di Azure a qualsiasi archivio dati sink supportato. La tabella seguente contiene un elenco degli archivi dati supportati come origini o sink dall'attività di copia. È ad esempio possibile spostare dati **da** un database di SQL Server o un database SQL di Azure **a** una risorsa di archiviazione BLOB di Azure. È anche possibile copiare dati **da** Archiviazione BLOB di Azure **a** un'istanza di Azure SQL Data Warehouse o una raccolta Azure Cosmos DB. 

## <a name="supported-scenarios"></a>Scenari supportati
È possibile copiare i dati **da un archivio BLOB di Azure** agli archivi di dati seguenti:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

È possibile copiare i dati dagli archivi dati seguenti **a un archivio BLOB di Azure**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]
 
> [!IMPORTANT]
> L'attività di copia supporta la copia dei dati da/in account di archiviazione di Azure per utilizzo generico e servizi di Archiviazione BLOB ad accesso frequente o sporadico. L'attività supporta la **lettura da BLOB in blocchi, di aggiunta o di pagine**, ma supporta la **scrittura solo in BLOB in blocchi**. Archiviazione Premium di Azure non è supportata come sink poiché si basa sui BLOB di pagine.
> 
> L'attività di copia non elimina i dati dall'origine dopo che i dati sono stati correttamente copiati nella destinazione. Se è necessario eliminare i dati di origine dopo una copia con esito positivo, creare un'[attività personalizzata](data-factory-use-custom-activities.md) per eliminare i dati e usare l'attività nella pipeline. Per un esempio, vedere l'[esempio di eliminazione di BLOB o cartella in GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity). 

## <a name="get-started"></a>Attività iniziali
È possibile creare una pipeline con l'attività di copia che sposta i dati da e verso un archivio BLOB di Azure usando diversi strumenti/API.

Il modo più semplice per creare una pipeline è usare la **Copia guidata**. Questo articolo include una [procedura dettagliata](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage)per la creazione di una pipeline per copiare dati da un percorso di un archivio BLOB di Azure al percorso di un altro archivio BLOB di Azure. Per un'esercitazione sulla creazione di una pipeline per copiare dati da un archivio BLOB di Azure a un database SQL Azure, vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md).

È possibile anche usare gli strumenti seguenti per creare una pipeline: **portale di Azure**, **Visual Studio**, **Azure PowerShell**, **modello di Azure Resource Manager**, **API .NET** e **API REST**. Vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Se si usano gli strumenti o le API, eseguire la procedura seguente per creare una pipeline che sposta i dati da un archivio dati di origine a un archivio dati sink:

1. Creare una **data factory**. Una data factory può contenere una o più pipeline. 
2. Creare i **servizi collegati** per collegare gli archivi di dati di input e output alla data factory. Ad esempio, se si copiano i dati da un'archiviazione BLOB di Azure in un database SQL di Azure, si creano due servizi collegati per collegare l'account di archiviazione di Azure e il database SQL di Azure alla data factory. Per le proprietà del servizio collegato specifiche dell'archivio BLOB di Azure, vedere la sezione sulle [proprietà del servizio collegato](#linked-service-properties). 
2. Creare i **set di dati** per rappresentare i dati di input e di output per le operazioni di copia. Nell'esempio citato nel passaggio precedente, si crea un set di dati per specificare un contenitore BLOB e la cartella che contiene i dati di input. Si crea anche un altro set di dati per specificare la tabella SQL nel database SQL di Azure che contiene i dati copiati dall'archiviazione BLOB. Per le proprietà del set di dati specifiche dell'archivio BLOB di Azure, vedere la sezione sulle [proprietà del set di dati](#dataset-properties).
3. Creare una **pipeline** con un'attività di copia che accetti un set di dati come input e un set di dati come output. Nell'esempio indicato in precedenza si usa BlobSource come origine e SqlSink come sink per l'attività di copia. Analogamente, se si effettua la copia dal database SQL di Azure nell'archivio BLOB di Azure, si usa SqlSource e BlobSink nell'attività di copia. Per le proprietà dell'attività di copia specifiche per l'archivio BLOB di Azure, vedere la sezione sulle [proprietà dell'attività di copia](#copy-activity-properties). Per informazioni dettagliate su come usare un archivio dati come origine o come sink, fare clic sul collegamento nella sezione precedente per l'archivio dati.  

Quando si usa la procedura guidata, le definizioni JSON per queste entità di data factory (servizi, set di dati e pipeline collegati) vengono create automaticamente. Quando si usano gli strumenti o le API, ad eccezione delle API .NET, usare il formato JSON per definire le entità di data factory.  Per esempi con definizioni JSON per entità di data factory utilizzate per copiare i dati da e verso un'archiviazione BLOB di Azure, vedere la sezione degli [esempi JSON](#json-examples-for-copying-data-to-and-from-blob-storage  ) in questo articolo.

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà JSON che vengono usate per definire entità di data factory specifiche di un archivio BLOB di Azure.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato
Esistono due tipi di servizi collegati, che consentono di collegare un archivio di Azure a una data factory di Azure. I due tipi di servizi sono il servizio collegato **AzureStorage** e il servizio collegato **AzureStorageSas**. Il servizio collegato Archiviazione di Azure garantisce alla data factory l'accesso globale ad Archiviazione di Azure. Invece il servizio collegato Firma di accesso condiviso di Archiviazione di Azure garantisce alla data factory l'accesso limitato o a scadenza ad Archiviazione di Azure. Non esistono altre differenze tra questi due servizi collegati. Scegliere il servizio collegato più adatto alle proprie esigenze. Le sezioni seguenti forniscono altri dettagli su questi due servizi collegati.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Proprietà dei set di dati
Per specificare un set di dati per rappresentare i dati di input o di output in un'archiviazione BLOB di Azure, impostare la proprietà del tipo del set di dati su **AzureBlob**. Impostare la proprietà **linkedServiceName** del set di dati sul nome del servizio collegato di Archiviazione di Azure o di firma di accesso condiviso Archiviazione di Azure.  Le proprietà del tipo del set di dati specificano il **contenitore BLOB** e la **cartella** nell'archivio BLOB.

Per un elenco completo delle proprietà e delle sezioni JSON disponibili per la definizione dei set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

Data Factory supporta i valori di tipo basati su .NET conformi a CLS per specificare informazioni sul tipo nella sezione "structure" in relazione allo schema su origini dati di lettura come BLOB di Azure: Int16, Int32, Int64, Single, Double, Decimal, Byte[], Bool, String, Guid, Datetime, Datetimeoffset, Timespan. La data factory esegue automaticamente le conversioni quando si spostano dati da un archivio dati di origine a un archivio dati di sink.

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione, il formato dei dati e così via nell'archivio dati. La sezione typeProperties per il set di dati di tipo **AzureBlob** presenta le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
| --- | --- | --- |
| folderPath |Percorso del contenitore e della cartella nell'archivio BLOB. Esempio: myblobcontainer\myblobfolder\ |Sì |
| fileName |Nome del BLOB. fileName è facoltativo e non applica la distinzione tra maiuscole e minuscole.<br/><br/>Se si specifica un filename, l'attività, inclusa la copia, funziona sul BLOB specifico.<br/><br/>Quando fileName non è specificato, la copia include tutti i BLOB in folderPath per il set di dati di input.<br/><br/>Quando non è specificato **fileName** per un set di dati di output e non è specificato **preserveHierarchy** nel sink dell'attività, il nome del file generato avrà il formato seguente: Data.<Guid>.txt (ad esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt). |No  |
| partitionedBy |partitionedBy è una proprietà facoltativa. Può essere utilizzata per specificare una proprietà folderPath dinamica e un nome file per i dati della serie temporale. Ad esempio, è possibile includere parametri per ogni ora di dati in folderPath. Per informazioni dettagliate ed esempi, vedere la sezione [Uso della proprietà partitionedBy](#using-partitionedBy-property) . |No  |
| format | Sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni [TextFormat](data-factory-supported-file-and-compression-formats.md#text-format), [JsonFormat](data-factory-supported-file-and-compression-formats.md#json-format), [AvroFormat](data-factory-supported-file-and-compression-formats.md#avro-format), [OrcFormat](data-factory-supported-file-and-compression-formats.md#orc-format) e [ParquetFormat](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Per **copiare i file così come sono** tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output. |No  |
| compressione | Specificare il tipo e il livello di compressione dei dati. I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. I livelli supportati sono **Ottimale** e **Più veloce**. Per altre informazioni, vedere [File e formati di compressione in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No  |

### <a name="using-partitionedby-property"></a>Uso della proprietà partitionedBy
Come indicato nella sezione precedente, è possibile specificare valori fileName e folderPath dinamici per i dati di una serie temporale con la proprietà **partitionedBy**, le [funzioni di data factory e le variabili di sistema](data-factory-functions-variables.md).

Per maggiori informazioni sui set di dati delle serie temporali, sulla pianificazione e sulle sezioni, leggere gli articoli [Creazione di set di dati](data-factory-create-datasets.md) e [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md).

#### <a name="sample-1"></a>Esempio 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

In questo esempio {Slice} viene sostituito con il valore della variabile di sistema SliceStart di Data Factory nel formato (AAAAMMGGHH) specificato. SliceStart fa riferimento all'ora di inizio della sezione. La proprietà folderPath è diversa per ogni sezione. For example: wikidatagateway/wikisampledataout/2014100103 or wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>Esempio 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

In questo esempio l'anno, il mese, il giorno e l'ora di SliceStart vengono estratti in variabili separate che vengono usate dalle proprietà folderPath e fileName.

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo [Creazione di pipeline](data-factory-create-pipelines.md). Proprietà quali nome, descrizione, criteri e set di dati di input e di output sono disponibili per tutti i tipi di attività. Le proprietà disponibili nella sezione **typeProperties** dell'attività variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink. Se si effettua il trasferimento dei dati da un archivio BLOB di Azure, impostare il tipo di origine nell'attività di copia su **BlobSource**. Analogamente, se si effettua il trasferimento dei dati in un archivio BLOB di Azure, impostare il tipo di sink nell'attività di copia su **BlobSink**. Questa sezione presenta un elenco delle proprietà supportate da BlobSource e BlobSink.

**BlobSource** supporta le seguenti proprietà della sezione **typeProperties**:

| Proprietà | DESCRIZIONE | Valori consentiti | Obbligatoria |
| --- | --- | --- | --- |
| ricorsiva |Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. |True (valore predefinito), False |No  |

**BlobSink** supporta le proprietà della sezione **typeProperties** seguenti:

| Proprietà | DESCRIZIONE | Valori consentiti | Obbligatoria |
| --- | --- | --- | --- |
| copyBehavior |Definisce il comportamento di copia quando l'origine è BlobSource o FileSystem. |<b>PreserveHierarchy:</b> mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><br/><b>FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. Il nome dei file di destinazione viene generato automaticamente. <br/><br/><b>MergeFiles</b>: unisce tutti i file della cartella di origine in un solo file. Se viene specificato il nome file/BLOB, il nome file unito sarà il nome specificato. In caso contrario, sarà il nome file generato automaticamente. |No  |

**BlobSource** supporta anche le due proprietà seguenti per la compatibilità con le versioni precedenti.

* **treatEmptyAsNull**: specifica se considerare una stringa vuota o Null come valore Null.
* **skipHeaderLineCount** : specifica il numero di righe che devono essere ignorate. È applicabile solo quando il set di dati di input usa TextFormat.

In modo analogo, **BlobSink** supporta la proprietà seguente per la compatibilità con le versioni precedenti.

* **blobWriterAddHeader**: specifica se aggiungere un'intestazione di definizioni di colonna durante la scrittura di un set di dati di output.

Ora i set di dati supportano le proprietà seguenti che implementano la stessa funzionalità: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

La tabella seguente fornisce indicazioni sull'uso delle nuove proprietà del set di dati al posto di queste proprietà del sink o dell'origine BLOB.

| Proprietà dell'attività di copia | Proprietà del set di dati |
|:--- |:--- |
| skipHeaderLineCount in BlobSource |skipLineCount e firstRowAsHeader. Le righe vengono prima ignorate e poi la prima riga viene letta come intestazione. |
| treatEmptyAsNull in BlobSource |treatEmptyAsNull nel set di dati di input |
| blobWriterAddHeader in BlobSink |firstRowAsHeader nel set di dati di output |

Per informazioni dettagliate su queste proprietà, vedere la sezione [Specifica di TextFormat](data-factory-supported-file-and-compression-formats.md#text-format) .    

### <a name="recursive-and-copybehavior-examples"></a>esempi ricorsivi e copyBehavior
In questa sezione viene descritto il comportamento derivante dell'operazione di copia per diverse combinazioni di valori ricorsivi e copyBehavior.

| ricorsiva | copyBehavior | Comportamento risultante |
| --- | --- | --- |
| true |preserveHierarchy |Per una cartella di origine Cartella1 con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5<br/><br/>la cartella di destinazione Cartella1 viene creata con la stessa struttura dell'origine<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5. |
| true |flattenHierarchy |Per una cartella di origine Cartella1 con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5<br/><br/>la Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File5 |
| true |mergeFiles |Per una cartella di origine Cartella1 con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5<br/><br/>la Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 + File3 + File4 + File 5 viene unito in un file con nome file generato automaticamente |
| false |preserveHierarchy |Per una cartella di origine Cartella1 con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5<br/><br/>la cartella di destinazione Cartella1 viene creata con la struttura seguente<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Sottocartella1 con File3, File4 e File5 non considerati. |
| false |flattenHierarchy |Per una cartella di origine Cartella1 con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5<br/><br/>la cartella di destinazione Cartella1 viene creata con la struttura seguente<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File2<br/><br/><br/>Sottocartella1 con File3, File4 e File5 non considerati. |
| false |mergeFiles |Per una cartella di origine Cartella1 con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5<br/><br/>la cartella di destinazione Cartella1 viene creata con la struttura seguente<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 viene unito in un file con un nome file generato automaticamente. Nome generato automaticamente per File1<br/><br/>Sottocartella1 con File3, File4 e File5 non considerati. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Procedura dettagliata: Usare la copia guidata per copiare i dati in/da una risorsa di archiviazione BLOB
Ecco come copiare rapidamente i dati in/da una risorsa di archiviazione BLOB di Azure. In questa procedura dettagliata gli archivi dati sia di origine che di destinazione sono di tipo archivio BLOB di Azure. La pipeline in questa procedura dettagliata copia i dati da una cartella a un'altra cartella nello stesso contenitore BLOB. Questa procedura dettagliata è volutamente semplice perché illustra le impostazioni o le proprietà quando si usa l'archivio BLOB come origine o come sink. 

### <a name="prerequisites"></a>prerequisiti
1. Creare un **account di archiviazione di Azure** per utilizzo generico, se necessario. In questa procedura dettagliata si usa l'archivio BLOB come archivio dati sia di **origine** che di **destinazione**. Se non si ha un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione](../../storage/common/storage-create-storage-account.md#create-a-storage-account) per informazioni su come crearne uno.
2. Creare un contenitore BLOB denominato **adfblobconnector** nell'account di archiviazione. 
4. Creare una cartella denominata **input** nel contenitore **adfblobconnector**.
5. Creare un file denominato **emp.txt** con il contenuto seguente e caricarlo nella cartella **input** usando strumenti come [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```
### <a name="create-the-data-factory"></a>Creare la data factory
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Crea una risorsa** nell'angolo in alto a sinistra, selezionare **Intelligence e analisi** e quindi **Data factory**.
3. Nel riquadro **Nuova data factory**:   
    1. Immettere **ADFBlobConnectorDF** come **nome**. È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato l'errore `*Data factory name “ADFBlobConnectorDF” is not available`, modificare il nome della data factory, ad esempio, nomeutenteADFBlobConnectorDF, e provare di nuovo a crearla. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere l'argomento relativo alle [regole di denominazione di Data factory](data-factory-naming-rules.md) .
    2. Selezionare la **sottoscrizione**di Azure.
    3. Per Gruppo di risorse, selezionare **Usa esistente** per selezionare un gruppo di risorse esistente oppure selezionare **Crea nuovo** per immettere un nome per un gruppo di risorse.
    4. Selezionare una **località** per la data factory.
    5. Selezionare la casella di controllo **Aggiungi al dashboard** nella parte inferiore del pannello.
    6. Fare clic su **Crea**.
3. Al termine della creazione verrà visualizzato il pannello **Data factory**, come illustrato nell'immagine seguente: ![Home page di Data factory](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Copia guidata
1. Nella home page di Data Factory fare clic sul riquadro **Copia dati (ANTEPRIMA)** per avviare **Copy Data Wizard** (Copia dati guidata).    
    
    > [!NOTE]
    >    Se il Web browser è bloccato su "Concessione autorizzazioni in corso...", disabilitare/deselezionare l'impostazione **Block third party cookies and site data** (Blocca cookie e dati del sito di terze parti) oppure lasciarla abilitata, creare un'eccezione per **login.microsoftonline.com** e quindi provare di nuovo ad avviare la procedura guidata.
2. Nella pagina **Proprietà** :
    1. Immettere **CopyPipeline** in **Nome attività**. Il nome dell'attività è il nome della pipeline nella data factory.
    2. Immettere una **descrizione** per l'attività (facoltativo).
    3. In **Task cadence or Task schedule** (Cadenza attività o pianificazione attività) lasciare selezionata l'opzione **Run regularly on schedule** (Esegui periodicamente come pianificato). Per eseguire questa attività una sola volta invece che ripetutamente in base a una pianificazione, selezionare **Run once now** (Esegui una volta ora). Se si seleziona l'opzione **Run once now** (Esegui una volta ora), viene creata una [pipeline con esecuzione singola](data-factory-create-pipelines.md#onetime-pipeline). 
    4. Mantenere le impostazioni per **Recurring pattern** (Schema ricorrente). Questa attività viene eseguita ogni giorno tra le ore di inizio e fine specificate nel passaggio successivo.
    5. Impostare **Start date time** (Data e ora di inizio) su **21/04/2017**. 
    6. Impostare **End date time** (Data e ora di fine) su **25/04/2017**. È possibile digitare la data invece di cercarla nel calendario.     
    8. Fare clic su **Avanti**.
      ![Strumento di copia - Pagina Proprietà](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png) 
3. Nella pagina **Source data store** (Archivio dati di origine) fare clic sul riquadro **Archivio BLOB di Azure**. Usare questa pagina per specificare l'archivio dati di origine per l'attività di copia. È possibile usare un servizio collegato di archivio dati esistente oppure specificare un nuovo archivio dati. Per usare un servizio collegato esistente, selezionare **FROM EXISTING LINKED SERVICES** (DA SERVIZI COLLEGATI ESISTENTI) e selezionare il servizio collegato corretto. 
    ![Strumento di copia - Pagina Archivio dati di origine](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. Nella pagina **Specify the Azure Blob storage account** (Specificare l'account di archiviazione BLOB di Azure):
   1. Mantenere il nome generato automaticamente per **Nome connessione**. Il nome della connessione è il nome del servizio collegato di tipo: Archiviazione di Azure. 
   2. Verificare che in **Account selection method** (Metodo di selezione dell'account) sia selezionata l'opzione **From Azure subscriptions** (Da sottoscrizioni di Azure).
   3. Selezionare la sottoscrizione di Azure o mantenere **Seleziona tutte** per **Sottoscrizione di Azure**.   
   4. Selezionare un **Account di archiviazione di Azure** nell'elenco di quelli disponibili nella sottoscrizione selezionata. È anche possibile scegliere di immettere manualmente le impostazioni dell'account di archiviazione, selezionando l'opzione **Immetti manualmente** per **Account selection method** (Metodo di selezione dell'account).
   5. Fare clic su **Avanti**. 
      ![Strumento di copia - Specificare l'account di archiviazione BLOB di Azure](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. Nella pagina **Choose the input file or folder** (Scegliere il file o la cartella di input):
   1. Fare doppio clic su **adfblobcontainer**.
   2. Selezionare **input** e fare clic su **Scegli**. In questa procedura dettagliata si seleziona la cartella input. È anche possibile scegliere invece il file emp.txt nella cartella. 
      ![Strumento di copia - Scegliere il file o la cartella di input](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. Nella pagina **Choose the input file or folder** (Scegliere il file o la cartella di input):
    1. Verificare che **File o cartella** sia impostato su **adfblobconnector/input**. Se i file sono in cartelle secondarie, ad esempio 2017/04/01, 2017/04/02 e così via, immettere adfblobconnector/input/{anno}/{mese}/{giorno} per il file o la cartella. Quando si preme TAB al di fuori della casella di testo, vengono visualizzati tre elenchi a discesa per selezionare i formati per anno (yyyy), mese (MM) e giorno (dd). 
    2. Non impostare **Copy file recursively** (Copia file in modo ricorsivo). Selezionare questa opzione per cercare in modo ricorsivo nelle cartelle i file da copiare nella destinazione. 
    3. Non selezionare l'opzione **binary copy** (Copia binaria). Selezionare questa opzione per eseguire una copia binaria del file di origine nella destinazione. Non selezionarla per questa procedura dettagliata per poter visualizzare altre opzioni nelle pagine successive. 
    4. Verificare che **Tipo di compressione** sia impostato su **Nessuno**. Selezionare un valore per questa opzione se i file di origine sono compressi in uno dei formati supportati. 
    5. Fare clic su **Avanti**.
    ![Strumento di copia - Scegliere il file o la cartella di input](./media/data-factory-azure-blob-connector/chose-input-file-folder.png) 
7. Nella pagina **File format settings** (Impostazioni di formato file) vengono visualizzati i delimitatori e lo schema rilevati automaticamente dalla procedura guidata analizzando il file. 
    1. Confermare le opzioni seguenti: a. Il **formato file** è impostato su **Testo**. È possibile visualizzare tutti i formati supportati nell'elenco a discesa. Ad esempio: JSON, Avro, ORC, Parquet.
        b. Il **delimitatore di colonna** è impostato su `Comma (,)`. È possibile visualizzare gli altri delimitatori di colonna supportati da Data Factory nell'elenco a discesa. È anche possibile specificare un delimitatore personalizzato.
        c. Il **delimitatore di riga** è impostato su `Carriage Return + Line feed (\r\n)`. È possibile visualizzare gli altri delimitatori di riga supportati da Data Factory nell'elenco a discesa. È anche possibile specificare un delimitatore personalizzato.
        d. Il **numero di righe da ignorare** è impostato su **0**. Per ignorare alcune righe all'inizio del file, immettere il numero qui.
        e.  L'opzione **Nomi di colonne nella prima riga di dati** non è impostata. Se i file di origine contengano nomi di colonna nella prima riga, selezionare questa opzione.
        f. L'opzione **treat empty column value as null** (Considera i valori di colonna vuoti come null) è impostata.
    2. Espandere **Impostazioni avanzate** per visualizzare l'opzione avanzata disponibile.
    3. Nella parte inferiore della pagina visualizzare l'**anteprima** dei dati del file emp.txt.
    4. Fare clic sulla scheda **SCHEMA** nella parte inferiore per visualizzate lo schema derivato dalla copia guidata esaminando i dati nel file di origine.
    5. Dopo aver esaminato i delimitatori e i dati di anteprima, fare clic su **Next** (Avanti).
    ![Strumento di copia - Impostazioni di formattazioni del file](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)  
8. Nella pagina **Destination data store** (Archivio dati di destinazione) selezionare **Archivio BLOB di Azure** e quindi fare clic su **Avanti**. In questa procedura dettagliata si usa l'archivio BLOB di Azure come archivio dati sia di origine che di destinazione.    
    ![Strumento di copia - Selezionare l'archivio dati di destinazione](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. Nella pagina **Specify the Azure Blob storage account** (Specificare l'account di archiviazione BLOB di Azure):
   1. Immettere **AzureStorageLinkedService** nel campo **Connection name** (Nome connessione).
   2. Verificare che in **Account selection method** (Metodo di selezione dell'account) sia selezionata l'opzione **From Azure subscriptions** (Da sottoscrizioni di Azure).
   3. Selezionare la **sottoscrizione**di Azure.  
   4. Selezionare l'account di archiviazione di Azure. 
   5. Fare clic su **Avanti**.     
10. Nella pagina **Choose the output file or folder** (Scegliere il file o la cartella di output): 
    6. In **Percorso cartella** specificare **adfblobconnector/output/{year}/{month}/{day}** (adfblobconnector/output/{anno}/{mese}/{giorno}). Premere **TAB**.
    7. Per **anno**, selezionare **yyyy**.
    8. Per **mese**, verificare che sia impostato su **MM**.
    9. Per **giorno**, verificare che sia impostato su **dd** (gg).
    10. Verificare che **Tipo di compressione** sia impostato su **Nessuno**.
    11. Verificare che **copy behavior** (Comportamento copia) sia impostato su **Merge files** (Unisci file). Se esiste già un file di output con lo stesso nome, il nuovo contenuto viene aggiunto alla fine dello stesso file.
    12. Fare clic su **Avanti**.
    ![Strumento di copia - Scegliere il file o la cartella di output](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. Nella pagina **File format settings** (Impostazioni di formato file) rivedere le impostazioni e fare clic su **Avanti**. Una delle opzioni aggiuntive ora consiste nell'aggiungere un'intestazione al file di output. Se si seleziona tale opzione, viene aggiunta una riga di intestazione con i nomi delle colonne dalla schema dell'origine. È possibile rinominare i nomi di colonna predefiniti quando si visualizza lo schema per l'origine. È ad esempio possibile impostare la prima colonna su Nome e la seconda colonna su Cognome. Viene quindi generato il file di output con un'intestazione contenente questi nomi come nomi di colonna. 
    ![Strumento di copia - Impostazioni di formatto file per la destinazione](media/data-factory-azure-blob-connector/file-format-destination.png)
12. Nella pagina **Performance settings** (Impostazioni prestazioni) verificare che **cloud units** (Unità cloud) e **parallel copies** (Copie parallele) siano impostati su **Auto** e fare clic su Avanti. Per informazioni dettagliate su queste impostazioni, vedere [Guida alle prestazioni dell'attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md#parallel-copy).
    ![Strumento di copia - Impostazioni relative alle prestazioni](media/data-factory-azure-blob-connector/copy-performance-settings.png) 
14. Nella pagina **Riepilogo** rivedere tutte le impostazioni (proprietà dell'attività, impostazioni per l'origine e la destinazione e impostazioni di copia) e fare clic su **Avanti**.
    ![Strumento di copia - Pagina Riepilogo](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Verificare le informazioni nella pagina **Riepilogo** e fare clic su **Fine**. La procedura guidata crea due servizi collegati, due set di dati (input e output) e una pipeline nella data factory da cui è stata avviata la Copia guidata.
    ![Strumento di copia - Pagina di distribuzione](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>Monitorare la pipeline (attività di copia)

1. Fare clic sul collegamento `Click here to monitor copy pipeline` nella pagina **Distribuzione**. 
2. L'**applicazione Monitoraggio e gestione** verrà visualizzata in una scheda separata.  ![App Monitoraggio e gestione](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Impostare l'ora di **inizio** nella parte superiore su `04/19/2017` e l'ora di **fine** su `04/27/2017` e quindi fare clic su **Applica**. 
4. Verranno visualizzate cinque finestre attività nell'elenco **ACTIVITY WINDOWS** (FINESTRE ATTIVITÀ). Gli orari indicati da **WindowStart** (Inizio finestra) devono coprire tutti i giorni dagli orari di inizio della pipeline a quelli di fine. 
5. Fare clic sul pulsante **Refresh** (Aggiorna) di **ACTIVITY WINDOWS** (FINESTRE ATTIVITÀ) alcune volte finché lo stato di tutte le finestre attività non risulta impostato su Ready (Pronto). 
6. Verificare ora che i file di output vengano generati nella cartella di output del contenitore adfblobconnector. Nella cartella di output verrà visualizzata la struttura di cartelle seguente: 
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25    
    ```
Per informazioni dettagliate sul monitoraggio e la gestione delle data factory, vedere l'articolo [Monitorare e gestire le pipeline di Data Factory](data-factory-monitor-manage-app.md). 
 
### <a name="data-factory-entities"></a>Entità di Data factory
Tornare ora alla scheda con la home page di Data Factory. Si noti che ora nella data factory sono presenti due servizi collegati, due set di dati e una pipeline. 

![Home page di Data Factory con entità](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Fare clic su **Creare e distribuire** per avviare l'editor di Data Factory. 

![Editor di Data factory](media/data-factory-azure-blob-connector/data-factory-editor.png)

Nella data factory verranno visualizzate le entità di Data Factory seguenti: 

 - Due servizi collegati, uno per l'origine e l'altro per la destinazione. Entrambi i servizi collegati fanno riferimento allo stesso account di archiviazione di Azure in questa procedura dettagliata. 
 - Due set di dati, uno di input e uno di output. In questa procedura dettagliata entrambi usano lo stesso contenitore BLOB, ma fanno riferimento a cartelle diverse (input e output).
 - Una pipeline. La pipeline contiene un'attività di copia che usa un'origine BLOB e un sink BLOB per copiare i dati da una posizione del BLOB di Azure a un'altra. 

Le sezioni seguenti offrono altre informazioni su queste entità. 

#### <a name="linked-services"></a>Servizi collegati
Verranno visualizzati due servizi collegati, uno per l'origine e l'altro per la destinazione. In questa procedura dettagliata entrambe le definizioni sono uguali, fatta eccezione per i nomi. Il **tipo** del servizio collegato è impostato su **AzureStorage**. La proprietà più importante della definizione di un servizio collegato è **connectionString**, usata da Data Factory per connettersi all'account di archiviazione di Azure in fase di esecuzione. Ignorare la proprietà hubName nella definizione. 

##### <a name="source-blob-storage-linked-service"></a>Servizio collegato di archiviazione BLOB di origine
```json
{
    "name": "Source-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

##### <a name="destination-blob-storage-linked-service"></a>Servizio collegato di archiviazione BLOB di destinazione

```json
{
    "name": "Destination-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

Per altre informazioni sui servizi collegati di archiviazione di Azure, vedere la sezione [Proprietà del servizio collegato](#linked-service-properties). 

#### <a name="datasets"></a>Set di dati
Sono presenti due set di dati, uno di input e uno di output. Il tipo di set di dati è impostato su **AzureBlob** per entrambi. 

Il set di dati di input punta alla cartella **input** del contenitore BLOB **adfblobconnector**. La proprietà **external** è impostata su **true** per questo set di dati perché i dati non vengono generati dalla pipeline con l'attività di copia che accetta questo set di dati come input. 

Il set di dati di output punta alla cartella **output** dello stesso contenitore BLOB. Il set di dati di output usa anche l'anno, il mese e il giorno della variabile di sistema **SliceStart** per valutare in modo dinamico il percorso del file di output. Per un elenco di funzioni e variabili di sistema supportate da Data Factory, vedere l'articolo [Funzioni e variabili di sistema di Data Factory](data-factory-functions-variables.md). La proprietà **external** è impostata su **false** (valore predefinito) perché questo set di dati viene generato dalla pipeline. 

Per altre informazioni sulle proprietà supportate dal set di dati del BLOB di Azure, vedere la sezione [Proprietà dei set di dati](#dataset-properties).

##### <a name="input-dataset"></a>Set di dati di input

```json
{
    "name": "InputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Source-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/input/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

##### <a name="output-dataset"></a>Set di dati di output

```json
{
    "name": "OutputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Destination-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/output/{year}/{month}/{day}",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
            "partitionedBy": [
                { "name": "year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }
            ]
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

#### <a name="pipeline"></a>Pipeline
La pipeline ha una sola attività. Il **tipo** dell'attività è impostato su **Copy**.  Nelle proprietà del tipo dell'attività sono presenti due sezioni, una per l'origine e l'altra per il sink. Il tipo dell'origine è impostato su **BlobSource** perché l'attività copia i dati da un archivio BLOB. Il tipo del sink è impostato su **BlobSink** perché l'attività copia i dati da un archivio BLOB. L'attività di copia accetta InputDataset-z4y come input e OutputDataset-z4y come output. 

Per altre informazioni sulle proprietà supportate da BlobSource e BlobSink, vedere la sezione [Proprietà dell'attività di copia](#copy-activity-properties). 

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "MergeFiles",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-z4y"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-z4y"
                    }
                ],
                "policy": {
                    "timeout": "1.00:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 3,
                    "longRetry": 0,
                    "longRetryInterval": "00:00:00"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "Activity-0-Blob path_ adfblobconnector_input_->OutputDataset-z4y"
            }
        ],
        "start": "2017-04-21T22:34:00Z",
        "end": "2017-04-25T05:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>Esempi JSON per la copia dei dati da e verso un archivio BLOB  
Gli esempi seguenti forniscono le definizioni JSON di esempio da usare per creare una pipeline con il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Tali esempi mostrano come copiare dati da/in Archiviazione BLOB di Azure e Database SQL Azure. Tuttavia, i dati possono essere copiati **direttamente** da una delle origini in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Data factory di Azure.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>Esempio JSON: Copiare dati da un archivio BLOB al database SQL
L'esempio seguente mostra:

1. Un servizio collegato di tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](#linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureBlob](#dataset-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [BlobSource](#copy-activity-properties) e [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties).

L'esempio copia i dati di una serie temporale da un BLOB di Azure in una tabella di Azure SQL ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato SQL di Azure:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Servizio collegato Archiviazione di Azure:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory supporta due tipi di servizi collegati di Archiviazione di Azure, **AzureStorage** e **AzureStorageSas**. Per il primo specificare la stringa di connessione che include la chiave dell'account e per il secondo specificare l'URI di firma di accesso condiviso. Per informazioni dettagliate, vedere la sezione [Servizi collegati](#linked-service-properties) .  

**Set di dati di input del BLOB di Azure:**

I dati vengono prelevati da un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella e il nome del file per il BLOB vengono valutati dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, e giorno dell'ora di inizio e il nome del file usa la parte dell'ora di inizio relativa all'ora. L'impostazione di "external" su "true" comunica a Data Factory che la tabella è esterna alla data factory e non è prodotta da un'attività al suo interno.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
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
**Set di dati di output SQL Azure:**

L'esempio copia i dati in una tabella denominata "MyTable" in un database SQL Azure. Creare la tabella nel database SQL di Azure con lo stesso numero di colonne di quelle previste nel file CSV del BLOB. Alla tabella vengono aggiunte nuove righe ogni ora.

```json
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Un'attività di copia in una pipeline con un'origine BLOB e un sink SQL:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **BlobSource** e il tipo **sink** è impostato su **SqlSink**.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
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
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>Esempio JSON: Copiare i dati da SQL Azure al BLOB di Azure
L'esempio seguente mostra:

1. Un servizio collegato di tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](#linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](#dataset-properties).
5. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) e [BlobSink](#copy-activity-properties).

L'esempio copia i dati di una serie temporale da una tabella di Azure SQL in un BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato SQL di Azure:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Servizio collegato Archiviazione di Azure:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory supporta due tipi di servizi collegati di Archiviazione di Azure, **AzureStorage** e **AzureStorageSas**. Per il primo specificare la stringa di connessione che include la chiave dell'account e per il secondo specificare l'URI di firma di accesso condiviso. Per informazioni dettagliate, vedere la sezione [Servizi collegati](#linked-service-properties) .  

**Set di dati di input SQL Azure:**

L'esempio presuppone che sia stata creata una tabella "MyTable" in SQL Azure e che contenga una colonna denominata "timestampcolumn" per i dati di una serie temporale.

Impostando "external" su "true" si comunica al servizio Data Factory che la tabella è esterna alla data factory e non è prodotta da un'attività al suo interno.

```json
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
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

**Set di dati di output del BLOB di Azure:**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": { "type": "DateTime",  "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Un'attività di copia in una pipeline con un'origine SQL e un sink BLOB:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo **source** è impostato su **SqlSource** e il tipo **sink** è impostato su **BlobSink**. La query SQL specificata per la proprietà **SqlReaderQuery** consente di selezionare i dati da copiare nell'ultima ora.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
              {
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureSQLInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

> [!NOTE]
> Per eseguire il mapping dal set di dati di origine alle colonne del set di dati sink, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Ottimizzazione delle prestazioni
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).
