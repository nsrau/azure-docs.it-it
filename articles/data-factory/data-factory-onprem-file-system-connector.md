---
title: Copiare dati da e in un file system con Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare i dati da e in un file system locale usando Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: ce19f1ae-358e-4ffc-8a80-d802505c9c84
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: jingwang
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: d25f1346ae35f7733ac3ca95c59a12616a60cc93
ms.contentlocale: it-it
ms.lasthandoff: 06/10/2017


---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Copiare dati da e in un file system locale usando Azure Data Factory
Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in un file system locale. Si basa sull'articolo relativo alle [attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con l'attività di copia.

## <a name="supported-scenarios"></a>Scenari supportati
È possibile copiare dati **da un file system locale** negli archivi dati seguenti:

[!INCLUDE [data-factory-supported-sink](../../includes/data-factory-supported-sinks.md)]

È possibile copiare i dati dagli archivi dati seguenti **in un file system locale**:

[!INCLUDE [data-factory-supported-sources](../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> L'attività di copia non elimina il file di origine dopo che è stato correttamente copiato nella destinazione. Se è necessario eliminare il file di origine dopo una copia con esito positivo, creare un'attività personalizzata per eliminare il file e usare l'attività nella pipeline. 

## <a name="enabling-connectivity"></a>Abilitazione della connettività
Data Factory supporta la connessione da e verso il file system locale tramite il **Gateway di gestione dati**. È necessario installare il Gateway di gestione di dati nell'ambiente locale per consentire al servizio Data Factory per connettersi a qualsiasi archivio dati locale supportato, incluso il file system. Per informazioni sul Gateway di gestione dati e per istruzioni dettagliate sulla configurazione del gateway vedere l'articolo [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md). Non è necessario installare altri file binari per la comunicazione da e verso il file system locale, tranne il Gateway di gestione dati. È necessario installare e usare il Gateway di gestione dati anche se il file system si trova in una macchina virtuale di Azure IaaS. Per informazioni dettagliate sul gateway, vedere [Gateway di gestione dati](data-factory-data-management-gateway.md).

Per usare una condivisione di file Linux, installare [Samba](https://www.samba.org/) sul server Linux e installare il Gateway di gestione dati in un server Windows. L'installazione di un Gateway di gestione dati su un server Linux non è supportata.

## <a name="getting-started"></a>Introduzione
È possibile creare una pipeline con l'attività di copia che sposta i dati da e verso un file system usando diversi strumenti/API.

Il modo più semplice per creare una pipeline è usare la **Copia guidata**. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

È possibile anche usare gli strumenti seguenti per creare una pipeline: **portale di Azure**, **Visual Studio**, **Azure PowerShell**, **modello di Azure Resource Manager**, **API .NET** e **API REST**. Vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Se si usano gli strumenti o le API, eseguire la procedura seguente per creare una pipeline che sposta i dati da un archivio dati di origine a un archivio dati sink:

1. Creare una **data factory**. Una data factory può contenere una o più pipeline. 
2. Creare i **servizi collegati** per collegare gli archivi di dati di input e output alla data factory. Ad esempio, se si copiano dati da un archivio BLOB di Azure a un file system locale, si creano due servizi collegati per collegare il file system locale e l'account di archiviazione di Azure alla data factory. Per le proprietà del servizio collegato specifiche del file system locale, vedere la sezione sulle [proprietà del servizio collegato](#linked-service-properties).
3. Creare i **set di dati** per rappresentare i dati di input e di output per le operazioni di copia. Nell'esempio citato nel passaggio precedente, si crea un set di dati per specificare un contenitore BLOB e la cartella che contiene i dati di input. Si crea anche un altro set di dati per specificare la cartella e il nome file (facoltativo) nel file system. Per le proprietà del set di dati specifiche del file system locale, vedere la sezione sulle [proprietà del set di dati](#dataset-properties).
4. Creare una **pipeline** con un'attività di copia che accetti un set di dati come input e un set di dati come output. Nell'esempio indicato in precedenza si usa BlobSource come origine e FileSystemSink come sink per l'attività di copia. Analogamente, se si effettua la copia dal file system locale all'archivio BLOB di Azure, si usa FileSystemSource e BlobSink nell'attività di copia. Per le proprietà dell'attività di copia specifiche del file system locale, vedere la sezione sulle [proprietà dell'attività di copia](#copy-activity-properties). Per informazioni dettagliate su come usare un archivio dati come origine o come sink, fare clic sul collegamento nella sezione precedente per l'archivio dati.

Quando si usa la procedura guidata, le definizioni JSON per queste entità di data factory (servizi, set di dati e pipeline collegati) vengono create automaticamente. Quando si usano gli strumenti o le API, ad eccezione delle API .NET, usare il formato JSON per definire le entità di data factory.  Per esempi con definizioni JSON per entità di data factory utilizzate per copiare i dati da e verso un file system, vedere la sezione degli [esempi JSON](#json-examples-for-copying-data-to-and-from-file-system) in questo articolo.

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà JSON che vengono usate per definire entità di data factory specifiche di un file system:

## <a name="linked-service-properties"></a>Proprietà del servizio collegato
È possibile collegare un file system locale a una data factory di Azure con il servizio collegato del **file server locale**. La tabella seguente include le descrizioni degli elementi JSON specifici del servizio collegato del file server locale.

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| type |Verificare che la proprietà type sia impostata su **OnPremisesFileServer**. |Sì |
| host |Specifica il percorso radice della cartella da copiare. Usare il carattere di escape '\' per i caratteri speciali nella stringa. Per ottenere alcuni esempi, vedere [Servizio collegato di esempio e definizioni del set di dati](#sample-linked-service-and-dataset-definitions) . |Sì |
| userid |Specificare l'ID dell'utente che ha accesso al server. |No (se si sceglie encryptedCredential) |
| password |Specificare la password per l'utente (userid). |No (se si sceglie encryptedCredential) |
| encryptedCredential |Specificare le credenziali crittografate che è possibile ottenere eseguendo il cmdlet New-AzureRmDataFactoryEncryptValue. |No (se si sceglie di specificare ID utente e password in testo normale) |
| gatewayName |Specifica il nome del gateway che Data Factory deve usare per connettersi al file server locale. |Sì |


### <a name="sample-linked-service-and-dataset-definitions"></a>Servizio collegato di esempio e definizioni del set di dati
| Scenario | Host nella definizione del servizio collegato | folderPath nella definizione del set di dati |
| --- | --- | --- |
| Cartella locale nel computer del gateway di gestione dati:  <br/><br/>Esempi: D:\\\* o D:\cartella\sottocartella\\* |D:\\\\ (per Gateway di gestione dati versione 2.0 e successive) <br/><br/> localhost (per le versioni precedenti alla versione 2.0 di Gateway di gestione dati) |.\\\\ o cartella\\\\sottocartella (per Gateway di gestione dati 2.0 e versioni successive) <br/><br/>D:\\\\ o D:\\\\cartella\\\\sottocartella (per la versione del gateway precedente a 2.0) |
| Cartella condivisa remota:  <br/><br/>Esempi: \\\\myserver\\share\\\* o \\\\myserver\\share\\cartella\\sottocartella\\* |\\\\\\\\myserver\\\\share |.\\\\ o cartella\\\\sottocartella |


### <a name="example-using-username-and-password-in-plain-text"></a>Esempio: Uso di nome utente e password in testo normale

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

### <a name="example-using-encryptedcredential"></a>Esempio: Uso di encryptedcredential

```JSON
{
  "Name": " OnPremisesFileServerLinkedService ",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "D:\\",
      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
      "gatewayName": "mygateway"
    }
  }
}
```

## <a name="dataset-properties"></a>Proprietà dei set di dati
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo [Creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati.

La sezione typeProperties è diversa per ogni tipo di set di dati e fornisce informazioni come la posizione e il formato dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo **FileShare** presenta le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| folderPath |Specifica il percorso secondario della cartella. Usare il carattere di escape '\' per i caratteri speciali nella stringa. Per ottenere alcuni esempi, vedere [Servizio collegato di esempio e definizioni del set di dati](#sample-linked-service-and-dataset-definitions) .<br/><br/>È possibile combinare questa proprietà con **partitionBy** per ottenere percorsi di cartelle basati su data e ora di inizio/fine delle sezioni. |Sì |
| fileName |Specificare il nome del file in **folderPath** se si vuole che la tabella faccia riferimento a un file specifico nella cartella. Se non si specifica alcun valore per questa proprietà, la tabella punta a tutti i file nella cartella.<br/><br/>Quando **fileName** non è specificato per un set di dati di output e **preserveHierarchy** non è specificato nel sink dell'attività, il nome del file generato avrà il formato seguente: <br/><br/>`Data.<Guid>.txt` Esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| fileFilter |Specificare un filtro da usare per selezionare un sottoinsieme di file in folderPath anziché tutti i file. <br/><br/>I valori consentiti sono: `*` (più caratteri) e `?` (carattere singolo).<br/><br/>Esempio 1: "fileFilter": "*. log"<br/>Esempio 2: "fileFilter": 2014 - 1-?. txt"<br/><br/>Si noti che fileFilter è applicabile per un set di dati di input FileShare. |No |
| partitionedBy |È possibile usare partitionedBy per specificare un valore folderPath/fileName dinamico per i dati di una serie temporale. Un esempio è folderPath con parametri per ogni ora di dati. |No |
| format | Sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni [TextFormat](data-factory-supported-file-and-compression-formats.md#text-format), [JsonFormat](data-factory-supported-file-and-compression-formats.md#json-format), [AvroFormat](data-factory-supported-file-and-compression-formats.md#avro-format), [OrcFormat](data-factory-supported-file-and-compression-formats.md#orc-format) e [ParquetFormat](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Per **copiare i file così come sono** tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output. |No |
| compressione | Specificare il tipo e il livello di compressione dei dati. I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. I livelli supportati sono **Ottimale** e **Più veloce**. vedere [File e formati di compressione in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

> [!NOTE]
> Non è possibile usare fileName e fileFilter contemporaneamente.

### <a name="using-partitionedby-property"></a>Uso della proprietà partitionedBy
Come indicato nella sezione precedente, è possibile specificare valori fileName e folderPath dinamici per i dati di una serie temporale con la proprietà **partitionedBy**, le [funzioni di data factory e le variabili di sistema](data-factory-functions-variables.md).

Per altri dettagli sui set di dati delle serie temporali, sulla pianificazione e sulle sezioni, vedere gli articoli [Creazione di set di dati](data-factory-create-datasets.md), [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md) e [Creazione di pipeline](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Esempio 1.

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

In questo esempio {Slice} viene sostituito con il valore della variabile di sistema SliceStart di Data Factory nel formato (AAAAMMGGHH). SliceStart fa riferimento all'ora di inizio della sezione. La proprietà folderPath è diversa per ogni sezione. Ad esempio: wikisampledataout/wikidatagateway/2014100103 o wikisampledataout/wikidatagateway/2014100104.

#### <a name="sample-2"></a>Esempio 2:

```JSON
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

In questo esempio l'anno, il mese, il giorno e l'ora di SliceStart vengono estratti in variabili separate usate dalle proprietà folderPath e fileName.

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo [Creazione di pipeline](data-factory-create-pipelines.md). Proprietà quali nome, descrizione, criteri e set di dati di input e di output sono disponibili per tutti i tipi di attività. Le proprietà disponibili nella sezione **typeProperties** dell'attività variano invece in base al tipo di attività.

Per l'attività di copia variano in base ai tipi di origine e sink. Se si effettua il trasferimento dei dati da un file system locale, impostare il tipo di origine nell'attività di copia su **FileSystemSource**. Se si effettua il trasferimento dei dati verso un file system locale, impostare il tipo di sink nell'attività di copia su **FileSystemSink**. Questa sezione presenta un elenco delle proprietà supportate da FileSystemSource e FileSystemSink.

**FileSystemSource** supporta le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| ricorsiva |Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. |True, False (valore predefinito) |No |

**FileSystemSink** supporta le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| copyBehavior |Definisce il comportamento di copia quando l'origine è BlobSource o FileSystem. |**PreserveHierarchy:** mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><br/>**FlattenHierarchy**: tutti i file della cartella di origine vengono creati nel primo livello della cartella di destinazione. Il nome dei file di destinazione viene generato automaticamente.<br/><br/>**MergeFiles**: unisce tutti i file della cartella di origine in un solo file. Se il nome file/BLOB viene specificato, il nome del file unito sarà il nome specificato. In caso contrario, verrà usato un nome file generato automaticamente. |No |

### <a name="recursive-and-copybehavior-examples"></a>esempi ricorsivi e copyBehavior
Questa sezione descrive il comportamento derivante dell'operazione di copia per diverse combinazioni di valori ricorsivi e delle proprietà copyBehavior.

| valore ricorsivo | valore di copyBehavior | Comportamento risultante |
| --- | --- | --- |
| true |preserveHierarchy |Per una cartella di origine Cartella1 con la struttura seguente,<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la cartella di destinazione Cartella1 viene creata con la stessa struttura dell'origine:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy |Per una cartella di origine Cartella1 con la struttura seguente,<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File5 |
| true |mergeFiles |Per una cartella di origine Cartella1 con la struttura seguente,<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 + File3 + File4 + File 5 viene unito in un file con nome generato automaticamente. |
| false |preserveHierarchy |Per una cartella di origine Cartella1 con la struttura seguente,<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la Cartella1 di destinazione viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>La sottocartella1 con File3, File4 e File5 non viene considerata. |
| false |flattenHierarchy |Per una cartella di origine Cartella1 con la struttura seguente,<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la Cartella1 di destinazione viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File2<br/><br/>La sottocartella1 con File3, File4 e File5 non viene considerata. |
| false |mergeFiles |Per una cartella di origine Cartella1 con la struttura seguente,<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la Cartella1 di destinazione viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 viene unito in un file con un nome file generato automaticamente.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/><br/>La sottocartella1 con File3, File4 e File5 non viene considerata. |

## <a name="supported-file-and-compression-formats"></a>Formati di file e di compressione supportati
Per i dettagli, vedere l'articolo relativo ai [file e formati di compressione in Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>Esempi JSON per la copia dei dati da e verso un file system
Gli esempi seguenti forniscono le definizioni JSON di esempio da usare per creare una pipeline con il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Questi esempi mostrano come copiare dati da e nel file system locale e in Archiviazione BLOB di Azure. È tuttavia possibile copiare dati *direttamente* da una qualsiasi delle origini in uno qualsiasi dei sink elencati in [Sink e origini supportate](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tramite l'attività di copia in Azure Data Factory.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Esempio: Copiare i dati da un file system locale in Archiviazione BLOB di Azure
Questo esempio illustra come copiare dati da un file system locale in Archiviazione BLOB di Azure. L'esempio include le entità della data factory seguenti:

* Un servizio collegato di tipo [OnPremisesFileServer](#linked-service-properties).
* Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Un [set di dati](data-factory-create-datasets.md) di input di tipo [FileShare](#dataset-properties).
* Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

L'esempio seguente copia i dati appartenenti a una serie temporale da un file system locale in Archiviazione BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

Come primo passaggio configurare il Gateway di gestione dati secondo le istruzioni in [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md).

**Servizio collegato del file server locale:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Si consiglia di usare la proprietà **encryptedCredential** anziché le proprietà **userid** e **password**. Per informazioni dettagliate su questo servizio collegato, vedere l'articolo relativo al [servizio collegato del file server](#linked-service-properties).

**Servizio collegato Archiviazione di Azure:**

```JSON
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

**Set di dati di input del file system locale:**

I dati vengono prelevati da un nuovo file ogni ora. Le proprietà folderPath e fileName vengono determinate in base all'ora di inizio della sezione.  

L'impostazione di `"external": "true"` comunica a Data Factory che il set di dati è esterno alla data factory e non è prodotto da un'attività al suo interno.

```JSON
{
  "name": "OnpremisesFileSystemInput",
  "properties": {
    "type": " FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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

**Set di dati di ouput di Archiviazione BLOB di Azure**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Un'attività di copia in una pipeline con un'origine su file system e un sink BLOB:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **FileSystemSource** e il tipo di **sink** è impostato su **BlobSink**.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T19:00:00",
    "description":"Pipeline for copy activity",
    "activities":[  
      {
        "name": "OnpremisesFileSystemtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "OnpremisesFileSystemInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "FileSystemSource"
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

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Esempio: Copiare i dati dal database SQL di Azure in un file system locale
L'esempio seguente mostra:

* Un servizio collegato di tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
* Un servizio collegato di tipo [OnPremisesFileServer](#linked-service-properties).
* Un set di dati di input di tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
* Un set di dati di output di tipo [FileShare](#dataset-properties).
* Una pipeline con attività di copia che usa [SqlSource](data-factory-azure-sql-connector.md##copy-activity-properties) e [FileSystemSink](#copy-activity-properties).

L'esempio copia i dati di una serie temporale da una tabella di Azure SQL a un sistema di file locale ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato per il database SQL Azure:**

```JSON
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

**Servizio collegato del file server locale:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Si consiglia di usare la proprietà **encryptedCredential** anziché le proprietà **userid** e **password**. Per informazioni dettagliate su questo servizio collegato, vedere l'articolo relativo al [servizio collegato del file system](#linked-service-properties).

**Set di dati di input SQL Azure:**

L'esempio presuppone che sia stata creata una tabella "MyTable" in SQL Azure e che contenga una colonna denominata "timestampcolumn" per i dati di una serie temporale.

L'impostazione di ``“external”: ”true”`` comunica a Data Factory che il set di dati è esterno alla data factory e non è prodotto da un'attività al suo interno.

```JSON
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

**Set di dati di output del file system locale:**

I dati vengono copiati in un nuovo file ogni ora. folderPath e fileName per il BLOB vengono determinati in base all'ora di inizio della sezione.

```JSON
{
  "name": "OnpremisesFileSystemOutput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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

**Un'attività di copia in una pipeline con un'origine SQL e un sink di file system:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo **source** è impostato su **SqlSource** e il tipo **sink** è impostato su **FileSystemSink**. La query SQL specificata per la proprietà **SqlReaderQuery** consente di selezionare i dati da copiare nell'ultima ora.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T20:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "AzureSQLtoOnPremisesFile",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "OnpremisesFileSystemOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "FileSystemSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 3,
          "timeout": "01:00:00"
        }
      }
     ]
   }
}
```


È anche possibile eseguire il mapping delle colonne del set di dati di origine alle colonne del set di dati sink nella definizione dell'attività di copia. Per altre informazioni, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestazioni e ottimizzazione
 Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

