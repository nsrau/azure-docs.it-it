---
title: LightIngest is a command-line utility for ingestion into Azure Data Explorer.
description: Informazioni su LightIngest, un'utilità della riga di comando per l'inserimento di dati ad hoc in Azure Data Explorer.Learn about LightIngest, a command-line utility for ad-hoc data ingestion into Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 95d943685cf511acb88f9e48d36a9dd43b0a27d2
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548009"
---
# <a name="install-and-use-lightingest"></a>Installare e utilizzare LightIngest

LightIngest is a command-line utility for ad-hoc data ingestion into Azure Data Explorer.
L'utilità può estrarre i dati di origine da una cartella locale o da un contenitore di archiviazione BLOB di Azure.The utility can pull source data from a local folder or from an Azure blob storage container.

## <a name="prerequisites"></a>Prerequisiti

* LightIngest - scaricarlo come parte del [pacchetto Microsoft.Azure.Kusto.Tools NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)

    ![Download di Lightingest](media/lightingest/lightingest-download-area.png)

* WinRAR - scaricalo da [www.win-rar.com/download.html](http://www.win-rar.com/download.html)

## <a name="install-lightingest"></a>Installazione di LightIngest

1. Passare alla posizione nel computer in cui è stato scaricato LightIngest. 
1. Utilizzando WinRAR, estrarre la directory degli *strumenti* nel computer.

## <a name="run-lightingest"></a>Eseguire LightIngest

1. Passare alla directory degli *strumenti* estratti nel computer.
1. Eliminare le informazioni sulla posizione esistenti dalla barra delle posizioni.
    
      ![Eliminare le informazioni sulla posizione](media/lightingest/lightingest-location-bar.png)

1. Invio `cmd` e premere **INVIO**.
1. Al prompt dei `LightIngest.exe` comandi, immettere seguito dall'argomento della riga di comando pertinente.

    > [!Tip]
    > Per un elenco degli argomenti della `LightIngest.exe /help`riga di comando supportati, immettere .
    >
    >![Guida della riga di comando](media/lightingest/lightingest-cmd-line-help.png)

1. Immettere seguito `ingest-` dalla stringa di connessione al cluster azure Data Explorer che gestirà l'inserimento.
    Racchiudere la stringa di connessione tra virgolette doppie e seguire la specifica delle stringhe di [connessione Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto).

    Ad esempio:
    ```
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

* Il metodo consigliato prevede che LightIngest funzioni con `https://ingest-{yourClusterNameAndRegion}.kusto.windows.net`l'endpoint di inserimento in . In questo modo, il servizio Azure Data Explorer può gestire il carico di inserimento ed è possibile eseguire facilmente il ripristino da errori temporanei. Tuttavia, è anche possibile configurare LightIngest`https://{yourClusterNameAndRegion}.kusto.windows.net`in modo che funzioni direttamente con l'endpoint del motore ( ).

> [!Note]
> Se si ingerisse direttamente con l'endpoint `ingest-`del motore, non è necessario includere , ma non sarà disponibile una funzionalità DM per proteggere il motore e migliorare la percentuale di successo dell'inserimento.

* Per prestazioni di inserimento ottimali, è importante che LightIngest conosca le dimensioni dei dati non elaborati e quindi LightIngest stima le dimensioni non compresse dei file locali. Tuttavia, LightIngest potrebbe non essere in grado di stimare correttamente le dimensioni non elaborate dei BLOB compressi senza prima scaricarli. Pertanto, quando si ingeriscono BLOB compressi, impostare la `rawSizeBytes` proprietà nei metadati BLOB su dimensioni dei dati non compressi in byte.

## <a name="general-command-line-arguments"></a>Argomenti generali della riga di comando

|Nome dell'argomento         |Nome breve   |Type    |Obbligatorio |Descrizione                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|                      |             |string  |Obbligatorio |[Stringa](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto) di connessione di Azure Data Explorer che specifica l'endpoint Kusto che gestirà l'inserimento. Deve essere racchiuso tra virgolette doppie |
|-database             |-db          |string  |Facoltativo  |Nome del database di Azure Data Explorer di destinazioneTarget Azure Data Explorer database name |
|-tabella                |             |string  |Obbligatorio |Nome tabella di Azure Data Explorer di destinazioneTarget Azure Data Explorer table name |
|-sourcePath           |-source      |string  |Obbligatorio |Percorso dei file di origine o dell'URI radice del contenitore BLOB. Se i dati si trova nei BLOB, devono contenere la chiave dell'account di archiviazione o la sAS. Consigliato per racchiudere tra virgolette doppie |
|-prefisso               |             |string  |Facoltativo  |Quando i dati di origine da ingerire si trovano nell'archiviazione BLOB, questo prefisso URL viene condiviso da tutti i BLOB, escluso il nome del contenitore. <br>Ad esempio, se i `MyContainer/Dir1/Dir2`dati sono in `Dir1/Dir2`, il prefisso deve essere . Si raccomanda di racchiudere tra virgolette doppie |
|-modello              |             |string  |Facoltativo  |Modello in base al quale vengono selezionati i file/BLOB di origine. Supporta i caratteri jolly. Ad esempio: `"*.csv"`. Consigliato per racchiudere tra virgolette doppie |
|-zipPattern (comprimere)           |             |string  |Facoltativo  |Espressione regolare da utilizzare quando si selezionano i file di un archivio zip da ingerire.<br>Tutti gli altri file nell'archivio verranno ignorati. Ad esempio, `"*.csv"`. Si consiglia di circondarlo tra virgolette doppie |
|-formato               |-f           |string  |Facoltativo  |Formato dei dati di origine. Deve essere uno dei [formati supportati](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) |
|-ingestionMappingPath |-mappingPercorso |string  |Facoltativo  |Percorso del file di mapping delle colonne di inserimento (obbligatorio per i formati Json e Avro). Visualizzare [i mapping dei dati](https://docs.microsoft.com/azure/kusto/management/mappings) |
|-ingestionMappingRef  |-mappingRef  |string  |Facoltativo  |Nome di un mapping di colonna di inserimento creato in precedena (obbligatorio per i formati Json e Avro). Visualizzare [i mapping dei dati](https://docs.microsoft.com/azure/kusto/management/mappings) |
|-creationTimePattern  |             |string  |Facoltativo  |Quando è impostata, viene usata per estrarre la proprietà CreationTime dal percorso del file o del BLOB. Vedere [Utilizzo dell'argomento CreationTimePatternSee Using CreationTimePattern argument](#using-creationtimepattern-argument) |
|-ignoreFirstRow (informazioni in base a un nomea per il       |-ignorePrima |bool    |Facoltativo  |Se impostato, il primo record di ogni file/blob viene ignorato (ad esempio, se i dati di origine hanno intestazioni)If set, the first record of each file/blob is ignored (for example, if the source data has headers) |
|-tag                  |             |string  |Facoltativo  |[Tag](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) da associare ai dati ingeriti. Sono consentite più occorrenze |
|-dontAttendere             |             |bool    |Facoltativo  |Se impostato su 'true', non attende il completamento dell'inserimento. Utile per l'inserimento di grandi quantità di file/BLOB |

### <a name="using-creationtimepattern-argument"></a>Utilizzo dell'argomento CreationTimePattern

L'argomento `-creationTimePattern` estrae la proprietà CreationTime dal percorso del file o del BLOB. Non è necessario che il modello rifletta l'intero percorso dell'elemento, ma solo la sezione che racchiude il timestamp che si vuole usare.

I valori dell'argomento devono includere:The argument values must include:
* Test costante immediatamente precedente al timestamp, racchiuso tra virgolette singole
* Il formato timestamp, nella [notazione DateTime standard](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) di .NET
* Testo costante immediatamente successivo al timestamp. Ad esempio, se i `historicalvalues19840101.parquet` nomi dei BLOB terminano con (il timestamp è composto da quattro cifre per `-creationTimePattern` l'anno, due cifre per il mese e due cifre per il giorno del mese), il valore corrispondente per l'argomento è:For example, if blob names end with (the timestamp is four digits for the year, two digits for the month, and two digits for the day of month), the corresponding value for the argument is:

```
ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -creationTimePattern:"'historicalvalues'yyyyMMdd'.parquet'"
 -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
```

### <a name="command-line-arguments-for-advanced-scenarios"></a>Argomenti della riga di comando per scenari avanzatiCommand-line arguments for advanced scenarios

|Nome dell'argomento         |Nome breve   |Type    |Obbligatorio |Descrizione                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|-compressione          |-cr          |double  |Facoltativo  |Suggerimento rapporto di compressione. Utile quando si archiviano file/BLOB compressi per aiutare Azure Data Explorer a valutare le dimensioni dei dati non elaborati. Calcolata come dimensione originale divisa per la dimensione compressa |
|-limite                |-l           |integer |Facoltativo  |Se impostato, limita l'inserimento ai primi N file |
|-listSolo             |-list        |bool    |Facoltativo  |Se impostato, visualizza solo gli elementi che sarebbero stati selezionati per l'inserimento| 
|-ingestTimeout (informazioni in questo periodo ingereto)        |             |integer |Facoltativo  |Timeout in minuti per il completamento di tutte le operazioni di inserimento. L'impostazione predefinita è `60`|
|-forceSync (finestra di intraetrice)            |             |bool    |Facoltativo  |Se impostato, forza l'inserimento sincrono. L'impostazione predefinita è `false` |
|-dataBatchSize        |             |integer |Facoltativo  |Imposta il limite di dimensione totale (MB, non compresso) di ogni operazione di inserimento |
|-filesInBatch         |             |integer |Facoltativo  |Imposta il limite di conteggio di file/BLOB di ogni operazione di inserimento |
|-devTracing           |-traccia       |string  |Facoltativo  |Se impostato, i log di diagnostica vengono `RollingLogs` scritti in una directory locale (per impostazione predefinita, nella directory corrente o possono essere modificati impostando il valore switch) |

## <a name="blob-metadata-properties"></a>Proprietà dei metadati BLOBBlob metadata properties
Se usato con i BLOB di Azure, LightIngest utilizzerà determinate proprietà dei metadati BLOB per aumentare il processo di inserimento.

|Metadata (proprietà)                            | Uso                                                                           |
|---------------------------------------------|---------------------------------------------------------------------------------|
|`rawSizeBytes`, `kustoUncompressedSizeBytes` | Se impostato, verrà interpretato come la dimensione dei dati non compressi                       |
|`kustoCreationTime`, `kustoCreationTimeUtc`  | Interpretato come timestamp UTC. Se impostato, verrà utilizzato per sostituire il tempo di creazione in Kusto. Utile per gli scenari di riempimento |

## <a name="usage-examples"></a>Esempi di utilizzo

<!-- Waiting for Tzvia or Vladik to rewrite the instructions for this example before publishing it

### Ingesting a specific number of blobs in JSON format

* Ingest two blobs under a specified storage account {Account}, in `JSON` format matching the pattern `.json`
* Destination is the database {Database}, the table `SampleData`
* Indicate that your data is compressed with the approximate ratio of 10.0
* LightIngest won't wait for the ingestion to be completed

To use the LightIngest command below:
1. Create a table command and enter the table name into the LightIngest command, replacing `SampleData`.
1. Create a mapping command and enter the IngestionMappingRef command, replacing `SampleData_mapping`.
1. Copy your cluster name and enter it into the LightIngest command, replacing `{ClusterandRegion}`.
1. Enter the database name into the LightIngest command, replacing `{Database name}`.
1. Replace `{Account}` with your account name and replace `{ROOT_CONTAINER}?{SAS token}` with the appropriate information.

    ```
    LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"  
        -db:{Database name} 
        -table:SampleData 
        -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}" 
        -IngestionMappingRef:SampleData_mapping 
        -pattern:"*.json" 
        -format:JSON 
        -limit:2 
        -cr:10.0 
        -dontWait:true
    ```
     
1. In Azure Data Explorer, open query count.

    ![Injestion result in Azure Data Explorer](media/lightingest/lightingest-show-failure-count.png)
-->

### <a name="ingesting-blobs-using-a-storage-account-key-or-a-sas-token"></a>Inserimento di BLOB usando una chiave dell'account di archiviazione o un token di firma di accesso condivisoIng blobs using a storage account key or a SAS token

* Inserimento di 10 BLOB `ACCOUNT`nell'account `DIR`di `CONT`archiviazione specificato, nella cartella , nel contenitore e corrispondente al modello`*.csv.gz`
* La destinazione `DB`è `TABLE`database , tabella `MAPPING` e il mapping di inserimento viene precreato nella destinazione
* Lo strumento attenderà il completamento delle operazioni di inserimento
* Prendere nota delle diverse opzioni per specificare il database di destinazione e la chiave dell'account di archiviazione e il token di firma di accesso condivisoNote the different options for specifying the target database and storage account key vs.

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10

LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True;Initial Catalog=DB"
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10
```

### <a name="ingesting-all-blobs-in-a-container-not-including-header-rows"></a>Inserimento di tutti i BLOB in un contenitore, escluse le righe di intestazioneIng all blobs in a container, not including header rows

* Immettere tutti i BLOB `ACCOUNT`nell'account `DIR1/DIR2`di `CONT`archiviazione specificato, nella cartella , nel contenitore e nella corrispondenza del modello`*.csv.gz`
* La destinazione `DB`è `TABLE`database , tabella `MAPPING` e il mapping di inserimento viene precreato nella destinazione
* I BLOB di origine contengono la riga di intestazione, pertanto allo strumento viene richiesto di eliminare il primo record di ogni BLOBSource blobs contain header line, so the tool is structed to drop the first record of each blob
* Lo strumento pubblicherà i dati per l'inserimento e non attenderà il completamento delle operazioni di inserimento

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR1/DIR2"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -ignoreFirstRow:true
```

### <a name="ingesting-all-json-files-from-a-path"></a>Inserimento di tutti i file JSON da un percorsoIngesting all JSON files from a path

* Ingerisci tutti `PATH`i file nel percorso , che corrispondono al modello`*.json`
* La destinazione `DB`è `TABLE`database , tabella e il mapping di inserimento è definito nel file locale`MAPPING_FILE_PATH`
* Lo strumento pubblicherà i dati per l'inserimento e non attenderà il completamento delle operazioni di inserimento

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
```

### <a name="ingesting-files-and-writing-diagnostic-trace-files"></a>Inserimento di file e scrittura di file di traccia diagnostica

* Ingerisci tutti `PATH`i file nel percorso , che corrispondono al modello`*.json`
* La destinazione `DB`è `TABLE`database , tabella e il mapping di inserimento è definito nel file locale`MAPPING_FILE_PATH`
* Lo strumento pubblicherà i dati per l'inserimento e non attenderà il completamento delle operazioni di inserimento
* I file di traccia di diagnostica verranno scritti localmente nella cartella`LOGS_PATH`

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
  -trace:"LOGS_PATH"
```
## <a name="changelog"></a>Log delle modifiche
|Versione        |Modifiche                                                                             |
|---------------|------------------------------------------------------------------------------------|
|4.0.9.0        |<ul><li>Aggiunta dell'argomento `-zipPattern` a </li><li>Aggiunta dell'argomento `-listOnly` a </li><li>Il riepilogo degli argomenti viene visualizzato prima dell'avvio dell'esecuzione</li><li>CreationTime viene letto dalle proprietà dei metadati BLOB `-creationTimePattern` o dal nome blob o file, in base all'argomento</li></ul>|
