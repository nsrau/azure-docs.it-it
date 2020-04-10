---
title: Formato JSON in Azure Data Factory
description: Questo argomento descrive come gestire il formato JSON in Azure Data Factory.This topic describes how to deal with JSON format in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: jingwang
ms.openlocfilehash: b63dcfd6ed293cab9d0107a8b6a35c7ce358d429
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011685"
---
# <a name="json-format-in-azure-data-factory"></a>Formato JSON in Azure Data Factory

Seguire questo articolo quando si desidera **analizzare i file JSON o scrivere i dati in formato JSON.** 

Il formato JSON è supportato per i connettori seguenti: [Amazon S3](connector-amazon-simple-storage-service.md), Blob di [Azure](connector-azure-blob-storage.md), Azure Data Lake [Storage Gen1](connector-azure-data-lake-store.md), Azure Data Lake Storage [Gen2](connector-azure-data-lake-storage.md), [Archiviazione file di Azure](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), Google [Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di](concepts-datasets-linked-services.md) dati. In questa sezione viene fornito un elenco delle proprietà supportate dal set di dati JSON.

| Proprietà         | Descrizione                                                  | Obbligatoria |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La proprietà type del dataset deve essere impostata su **Json**. | Sì      |
| posizione         | Impostazioni di posizione dei file. Ogni connettore basato su file ha `location`il proprio tipo di posizione e le proprietà supportate in . **Vedere i dettagli nell'articolo del connettore -> sezione delle proprietà del set di dati**. | Sì      |
| encodingName     | Tipo di codifica utilizzato per leggere/scrivere i file di test. <br>Allowed values are as follows: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| No       |
| compressione | Gruppo di proprietà per configurare la compressione dei file. Configurare questa sezione quando si desidera eseguire la compressione/decompressione durante l'esecuzione dell'attività. | No |
| type | Codec di compressione utilizzato per leggere/scrivere file JSON. <br>I valori consentiti sono **bzip2**, **gzip**, **defLATE**, **.ipDeflate**, **snappy**o **lz4**. da utilizzare quando si salva il file. L'impostazione predefinita non è compressa.<br>**Nota:** Attualmente l'attività di copia non supporta "snappy" & "lz4" e il flusso di dati di mapping non supporta ".ipDeflate".<br>**Si noti che** quando si utilizza l'attività di copia per decomprimere i file zipDeflate `<path specified in dataset>/<folder named as source zip file>/`e scrivere nell'archivio dati sink basato su file, i file verranno estratti nella cartella: . | No.  |
| level | Il rapporto di compressione. <br>I valori consentiti sono **Ottimale** o **Più veloce**.<br>- **Il più veloce:** L'operazione di compressione dovrebbe essere completata il più rapidamente possibile, anche se il file risultante non è compresso in modo ottimale.<br>- **Ottimale**: L'operazione di compressione deve essere compressa in modo ottimale, anche se il completamento dell'operazione richiede più tempo. Per maggiori informazioni, vedere l'argomento relativo al [livello di compressione](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | No       |

Di seguito è riportato un esempio di set di dati JSON nell'archiviazione BLOB di Azure:Below is an example of JSON dataset on Azure Blob Storage:

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). In questa sezione viene fornito un elenco delle proprietà supportate dall'origine JSON e dal sink.

### <a name="json-as-source"></a>JSON come origine

Le proprietà seguenti sono supportate nella sezione *** \*relativa all'origine\* *** dell'attività di copia.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà type dell'origine dell'attività di copia deve essere impostata su **JSONSource**. | Sì      |
| StoreImpostazioni | Gruppo di proprietà su come leggere i dati da un archivio dati. Ogni connettore basato su file `storeSettings`dispone di impostazioni di lettura supportate in . **Vedere i dettagli nell'articolo del connettore -> sezione Delle proprietà dell'attività Copy**. | No       |

### <a name="json-as-sink"></a>JSON come sink

Le proprietà seguenti sono supportate nella sezione del *** \*sink\* *** dell'attività di copia.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà type dell'origine dell'attività di copia deve essere impostata **su JSONSink**. | Sì      |
| formatImpostazioni | Gruppo di proprietà. Fare riferimento alla tabella delle impostazioni di **scrittura JSON** riportata di seguito. | No       |
| StoreImpostazioni | Gruppo di proprietà su come scrivere dati in un archivio dati. Ogni connettore basato su file `storeSettings`dispone di impostazioni di scrittura supportate in . **Vedere i dettagli nell'articolo del connettore -> sezione Delle proprietà dell'attività Copy**. | No       |

Impostazioni di scrittura `formatSettings`JSON **supportate** in:

| Proprietà      | Descrizione                                                  | Obbligatoria                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Il tipo di formatSettings deve essere impostato su **JsonWriteSettings**. | Sì                                                   |
| filePattern |Indicare il modello dei dati archiviati in ogni file JSON. I valori consentiti sono: **setOfObjects** e **arrayOfObjects**. Il valore **predefinito** è **setOfObjects**. Vedere la sezione [Modelli di file JSON](#json-file-patterns) per i dettagli su questi modelli. |No |

### <a name="json-file-patterns"></a>Modelli di file JSON

L'attività di copia è in grado di rilevare e analizzare automaticamente i modelli di file JSON seguenti. 

- **Tipo I: setOfObjects**

    Ogni file contiene un solo oggetto o più oggetti con delimitatori di riga/concatenati. 
    Quando questa opzione viene scelta nel sink dell'attività di copia, l'attività di copia produce un singolo file JSON con ogni oggetto per riga (delimitato da riga).

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

    * **Esempio di JSON con delimitatori di riga**

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

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

I tipi di file JSON possono essere usati sia come sink che come origine nel mapping del flusso di dati.

### <a name="creating-json-structures-in-a-derived-column"></a>Creazione di strutture JSON in una colonna derivataCreating JSON structures in a derived column

È possibile aggiungere una colonna complessa al flusso di dati tramite il generatore di espressioni di colonna derivata. Nella trasformazione della colonna derivata, aggiungere una nuova colonna e aprire il generatore di espressioni facendo clic sulla casella blu. Per rendere complessa una colonna, è possibile immettere manualmente la struttura JSON o usare l'esperienza utente per aggiungere sottocolonne in modo interattivo.

#### <a name="using-the-expression-builder-ux"></a>Utilizzo dell'esperienza utente del generatore di espressioniUsing the expression builder UX

Nel riquadro laterale dello schema di output passare il mouse su una colonna e fare clic sull'icona più. Selezionare **Aggiungi colonna secondaria** per impostare la colonna in modo che sia complessa.

![Aggiungi colonna secondaria](media/data-flow/addsubcolumn.png "Aggiungi colonna secondaria")

È possibile aggiungere ulteriori colonne e sottocolonne nello stesso modo. Per ogni campo non complesso, è possibile aggiungere un'espressione nell'editor delle espressioni a destra.

![Colonna complessa](media/data-flow/complexcolumn.png "Colonna complessa")

#### <a name="entering-the-json-structure-manually"></a>Immissione manuale della struttura JSON

Per aggiungere manualmente una struttura JSON, aggiungere una nuova colonna e immettere l'espressione nell'editor. L'espressione segue il seguente formato generale:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Se questa espressione fosse stata immessa per una colonna denominata "complexColumn", verrebbe scritta nel sink come JSON seguente:

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

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Script manuale di esempio per una definizione gerarchica completa
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

### <a name="source-format-options"></a>Opzioni del formato di origine

L'uso di un set di dati JSON come origine nel flusso di dati consente di configurare cinque impostazioni aggiuntive. Queste impostazioni sono disponibili nella fisarmonica **delle impostazioni JSON** nella scheda Opzioni di **origine.**  

![Impostazioni JSON](media/data-flow/json-settings.png "Impostazioni JSON")

#### <a name="default"></a>Predefinito

Per impostazione predefinita, i dati JSON vengono letti nel formato seguente.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>Documento singolo

Se è selezionato **Documento singolo,** i flussi di dati di mapping leggono un documento JSON da ogni file. 

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

#### <a name="unquoted-column-names"></a>Nomi di colonna senza virgolette

Se è selezionato Nomi di **colonna senza virgolette,** il mapping dei flussi di dati legge le colonne JSON non racchiuse tra virgolette. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>Ha commenti

Selezionare **Ha commenti** se i dati JSON hanno commenti in stile C o C.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>Singolo citato

Selezionare **Virgolette** singole se i campi e i valori JSON utilizzano virgolette singole anziché virgolette doppie.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>Barra rovesciata preceduta

Selezionare **Virgolette** singole se vengono utilizzate barre rovesciate per eseguire l'escape dei caratteri nei dati JSON.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Mapping del flusso di dati](concepts-data-flow-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
