---
title: Formato JSON in Azure Data Factory
description: Questo argomento descrive come gestire il formato JSON in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: jingwang
ms.openlocfilehash: 8429f58b9b8ce1be12fea861b805084347a0e2b2
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537698"
---
# <a name="json-format-in-azure-data-factory"></a>Formato JSON in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Seguire questo articolo quando si vuole **analizzare i file JSON o scrivere i dati in formato JSON**. 

Il formato JSON è supportato per i connettori seguenti [: Amazon S3](connector-amazon-simple-storage-service.md), [BLOB di Azure](connector-azure-blob-storage.md), [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md), [archiviazione file di Azure](connector-azure-file-storage.md), [file System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati JSON.

| Proprietà         | Descrizione                                                  | Obbligatoria |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La proprietà Type del set di dati deve essere impostata su **JSON**. | Sì      |
| posizione         | Impostazioni del percorso dei file. Ogni connettore basato su file ha un tipo di percorso e proprietà supportate in `location` . **Per informazioni dettagliate, vedere l'articolo connettore-> sezione Proprietà set di dati**. | Sì      |
| encodingName     | Tipo di codifica utilizzato per leggere/scrivere file di test. <br>I valori consentiti sono i seguenti: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| No       |
| compressione | Gruppo di proprietà per configurare la compressione dei file. Configurare questa sezione quando si desidera eseguire la compressione/decompressione durante l'esecuzione dell'attività. | No |
| type<br/>(*in `compression` *) | Codec di compressione usato per leggere/scrivere file JSON. <br>I valori consentiti sono **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **Snapper**o **LZ4**. da usare quando si salva il file. Il valore predefinito non è compresso.<br>**Nota** attualmente l'attività di copia non supporta "blocco" & "LZ4" e il flusso di dati di mapping non supporta "ZipDeflate".<br>**Nota** quando si usa l'attività di copia per decomprimere i file **ZipDeflate** e scrivere nell'archivio dati sink basato su file, per impostazione predefinita i file vengono estratti nella cartella: `<path specified in dataset>/<folder named as source zip file>/` , usare `preserveZipFileNameAsFolder` nell' [origine dell'attività di copia](#json-as-source) per controllare se mantenere il nome del file zip come struttura di cartelle. | No.  |
| livello<br/>(*in `compression` *) | Rapporto di compressione. <br>I valori consentiti sono **ottimali** o più **veloci**.<br>- Più **veloce:** L'operazione di compressione deve essere completata il più rapidamente possibile, anche se il file risultante non è compresso in modo ottimale.<br>- **Ottimale**: l'operazione di compressione deve essere compressa in modo ottimale, anche se il completamento dell'operazione richiede più tempo. Per maggiori informazioni, vedere l'argomento relativo al [livello di compressione](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | No       |

Di seguito è riportato un esempio di set di dati JSON nell'archivio BLOB di Azure:

```json
{
    "name": "JSONDataset",
    "properties": {
        "type": "Json",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "gzip"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink JSON.

Informazioni su come estrarre i dati dai file JSON ed eseguire il mapping all'archivio dati/formato sink o viceversa dal [mapping dello schema](copy-activity-schema-and-type-mapping.md).

### <a name="json-as-source"></a>JSON come origine

Nella sezione *** \* origine \* *** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **JSONSource**. | Sì      |
| formatSettings | Gruppo di proprietà. Vedere la tabella **delle impostazioni di lettura JSON** riportata di seguito. | No       |
| storeSettings | Un gruppo di proprietà su come leggere i dati da un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di lettura supportate in `storeSettings` . **Per informazioni dettagliate, vedere l'articolo connettore > sezione proprietà dell'attività di copia**. | No       |

**Impostazioni di lettura JSON** supportate in `formatSettings` :

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Il tipo di formatSettings deve essere impostato su **JsonReadSettings**. | Sì      |
| compressionProperties | Gruppo di proprietà su come decomprimere i dati per un determinato codec di compressione. | No       |
| preserveZipFileNameAsFolder<br>(*in `compressionProperties` *) | Si applica quando il set di dati di input viene configurato con la compressione **ZipDeflate** . Indica se mantenere il nome del file zip di origine come struttura di cartelle durante la copia. Se è impostato su true (impostazione predefinita), Data Factory scrive file decompressi in `<path specified in dataset>/<folder named as source zip file>/` ; se è impostato su false, Data Factory scrive i file decompressi direttamente in `<path specified in dataset>` .  | No |

### <a name="json-as-sink"></a>JSON come sink

Nella sezione *** \* sink \* *** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **JSONSink**. | Sì      |
| formatSettings | Gruppo di proprietà. Vedere la tabella **delle impostazioni di scrittura JSON** riportata di seguito. | No       |
| storeSettings | Gruppo di proprietà su come scrivere dati in un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di scrittura supportate in `storeSettings` . **Per informazioni dettagliate, vedere l'articolo connettore > sezione proprietà dell'attività di copia**. | No       |

**Impostazioni di scrittura JSON** supportate in `formatSettings` :

| Proprietà      | Descrizione                                                  | Obbligatoria                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Il tipo di formatSettings deve essere impostato su **JsonWriteSettings**. | Sì                                                   |
| filePattern |Indicare il modello dei dati archiviati in ogni file JSON. I valori consentiti sono: **setOfObjects** (righe JSON) e **arrayOfObjects**. Il valore **predefinito** è **setOfObjects**. Vedere la sezione [Modelli di file JSON](#json-file-patterns) per i dettagli su questi modelli. |No |

### <a name="json-file-patterns"></a>Modelli di file JSON

Quando si copiano dati da file JSON, l'attività di copia può rilevare e analizzare automaticamente i modelli di file JSON seguenti. Quando si scrivono dati in file JSON, è possibile configurare il modello di file nel sink dell'attività di copia.

- **Tipo I: setOfObjects**

    Ogni file contiene un singolo oggetto, righe JSON o oggetti concatenati.

    * **Esempio di JSON a oggetto singolo**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **Righe JSON (impostazione predefinita per sink)**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **Esempio di JSON concatenati**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **Tipo II: arrayOfObjects**

    Ogni file contiene una matrice di oggetti.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

## <a name="mapping-data-flow-properties"></a>Proprietà del flusso di dati per mapping

In mapping di flussi di dati è possibile leggere e scrivere in formato JSON negli archivi dati seguenti: [archiviazione BLOB di Azure](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)e [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Proprietà origine

La tabella seguente elenca le proprietà supportate da un'origine JSON. È possibile modificare queste proprietà nella scheda **Opzioni di origine** .

| Nome | Descrizione | Obbligatoria | Valori consentiti | Proprietà script flusso di dati |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Percorsi Wild Card | Verranno elaborati tutti i file corrispondenti al percorso con caratteri jolly. Sostituisce la cartella e il percorso del file impostati nel set di dati. | no | String[] | wildcardPaths |
| Partition Root Path (Percorso radice partizione) | Per i dati di file partizionati, è possibile immettere un percorso radice della partizione per leggere le cartelle partizionate come colonne | no | Stringa | partitionRootPath |
| Elenco di file | Indica se l'origine sta puntando a un file di testo che elenca i file da elaborare | no | `true` o `false` | fileList |
| Colonna in cui archiviare il nome del file | Crea una nuova colonna con il nome e il percorso del file di origine | no | Stringa | rowUrlColumn |
| Al termine | Elimina o sposta i file dopo l'elaborazione. Il percorso del file inizia dalla radice del contenitore | no | Elimina: `true` o`false` <br> Spostare`['<from>', '<to>']` | purgeFiles <br> moveFiles |
| Filtra per Ultima modifica | Scegliere di filtrare i file in base alla data dell'Ultima modifica | no | Timestamp | modifiedAfter <br> modifiedBefore |
| Documento singolo | Mapping dei flussi di dati leggere un documento JSON da ogni file | no | `true` o `false` | singleDocument |
| Nomi di colonna non racchiusi tra virgolette | Se è selezionato **nomi di colonna non racchiusi tra virgolette** , il mapping dei flussi di dati legge le colonne JSON che non sono racchiuse tra virgolette | no | `true` o `false` |  unquotedColumnNames |
| Con commenti | Select **ha commenti** se i dati JSON hanno commenti in stile C o C++ | no | `true` o `false` | asComments |
| Virgoletta singola | Legge le colonne JSON che non sono racchiuse tra virgolette | no | `true` o `false` | singleQuoted |
| Barra rovesciata con escape | Selezionare la **barra rovesciata** con caratteri di escape se vengono usate barre rovesciate per l'escape dei caratteri nei dati JSON | no | `true` o `false` | backslashEscape |

### <a name="source-format-options"></a>Opzioni del formato di origine

L'uso di un set di dati JSON come origine nel flusso di dati consente di impostare cinque impostazioni aggiuntive. Queste impostazioni sono disponibili nella scheda **Opzioni di origine** del file **JSON Settings** Accordion.  

![Impostazioni JSON](media/data-flow/json-settings.png "Impostazioni JSON")

#### <a name="default"></a>Predefinito

Per impostazione predefinita, i dati JSON vengono letti nel formato seguente.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>Documento singolo

Se è selezionato un **singolo documento** , il mapping dei flussi di dati legge un documento JSON da ogni file. 

``` json
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```
> [!NOTE]
> Se i flussi di dati generano un errore che indica "corrupt_record" quando si visualizza in anteprima i dati JSON, è probabile che i dati contengano un singolo documento nel file JSON. Se si imposta "documento singolo", questo errore verrà cancellato.

#### <a name="unquoted-column-names"></a>Nomi di colonna non racchiusi tra virgolette

Se è selezionato **nomi di colonna non racchiusi tra virgolette** , il mapping dei flussi di dati legge le colonne JSON che non sono racchiuse tra virgolette 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>Con commenti

Select **ha commenti** se i dati JSON hanno commenti in stile C o C++.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>Virgoletta singola

Selezionare **singolo racchiuso tra** virgolette se i campi e i valori JSON usano virgolette singole anziché virgolette doppie.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>Barra rovesciata con escape

Selezionare la **barra rovesciata** con caratteri di escape se le barre rovesciate vengono usate per usare caratteri di escape nei dati JSON.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

### <a name="sink-properties"></a>Proprietà sink

La tabella seguente elenca le proprietà supportate da un sink JSON. È possibile modificare queste proprietà nella scheda **Impostazioni** .

| Nome | Descrizione | Obbligatoria | Valori consentiti | Proprietà script flusso di dati |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Cancella la cartella | Se la cartella di destinazione viene cancellata prima della scrittura | no | `true` o `false` | truncate |
| Opzione nome file | Formato di denominazione dei dati scritti. Per impostazione predefinita, un file per partizione è nel formato`part-#####-tid-<guid>` | no | Modello: stringa <br> Per partizione: stringa [] <br> Come dati in column: String <br> Output in un singolo file:`['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

### <a name="creating-json-structures-in-a-derived-column"></a>Creazione di strutture JSON in una colonna derivata

È possibile aggiungere una colonna complessa al flusso di dati tramite il generatore di espressioni della colonna derivata. Nella trasformazione colonna derivata aggiungere una nuova colonna e aprire il generatore di espressioni facendo clic sulla casella blu. Per rendere complessa una colonna, è possibile immettere la struttura JSON manualmente o usare l'esperienza utente per aggiungere le sottocolonne in modo interattivo.

#### <a name="using-the-expression-builder-ux"></a>Uso del generatore di espressioni UX

Nel riquadro lato schema di output posizionare il puntatore del mouse su una colonna e fare clic sull'icona con il segno più. Selezionare **Aggiungi sottocolonna** per rendere la colonna un tipo complesso.

![Aggiungi sottocolonna](media/data-flow/addsubcolumn.png "Aggiungi sottocolonna")

È possibile aggiungere colonne e sottocolonne aggiuntive nello stesso modo. Per ogni campo non complesso, è possibile aggiungere un'espressione nell'editor di espressioni a destra.

![Colonna complessa](media/data-flow/complexcolumn.png "Colonna complessa")

#### <a name="entering-the-json-structure-manually"></a>Immissione manuale della struttura JSON

Per aggiungere manualmente una struttura JSON, aggiungere una nuova colonna e immettere l'espressione nell'editor. L'espressione segue il formato generale seguente:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Se questa espressione è stata immessa per una colonna denominata "complexColumn", viene scritta nel sink come il JSON seguente:

```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Script manuale di esempio per la definizione gerarchica completa
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Flusso di dati per mapping](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
