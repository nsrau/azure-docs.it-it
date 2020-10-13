---
title: Formato Common Data Model
description: Trasformare i dati usando il sistema di metadati Common Data Model
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: daperlov
ms.openlocfilehash: 483e26cf4044b909c8d7923cfd74bd6fcf871e2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87905294"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Formato Common Data Model in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Il sistema di metadati Common Data Model (CDM) consente di condividere facilmente i dati e il relativo significato tra applicazioni e processi aziendali. Per altre informazioni, vedere Panoramica di [Common Data Model](https://docs.microsoft.com/common-data-model/) .

In Azure Data Factory, gli utenti possono trasformare i dati da entità CDM in model.jse in formato manifesto archiviati in [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) (ADLS Gen2) usando i flussi di dati di mapping. È anche possibile affondare i dati in formato CDM usando i riferimenti alle entità CDM che definiranno i dati in formato CSV o parquet in cartelle partizionate. 

> [!NOTE]
> Il connettore di formato Common Data Model (CDM) per i flussi di dati ADF è attualmente disponibile come anteprima pubblica.

## <a name="mapping-data-flow-properties"></a>Proprietà del flusso di dati per mapping

Common Data Model è disponibile come set di dati [inline](data-flow-source.md#inline-datasets) nel mapping di flussi di dati come origine e sink.

> [!NOTE]
> Quando si scrivono entità CDM, è necessario avere una definizione di entità CDM esistente (schema dei metadati) già definita da usare come riferimento. Il sink del flusso di dati ADF leggerà il file di entità CDM e importerà lo schema nel sink per il mapping dei campi.

### <a name="source-properties"></a>Proprietà origine

La tabella seguente elenca le proprietà supportate da un'origine CDM. È possibile modificare queste proprietà nella scheda **Opzioni di origine** .

| Nome | Descrizione | Obbligatoria | Valori consentiti | Proprietà script flusso di dati |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formato | Il formato deve essere `cdm` | sì | `cdm` | format |
| Formato metadati | Dove si trova il riferimento all'entità nei dati. Se si usa CDM versione 1,0, scegliere manifesto. Se si usa una versione CDM prima del 1,0, scegliere model.json. | Sì | `'manifest'` o `'model'` | manifestType |
| Percorso radice: contenitore | Nome del contenitore della cartella CDM | sì | string | fileSystem |
| Percorso radice: percorso cartella | Percorso cartella radice della cartella CDM | sì | string | folderPath |
| File manifesto: percorso entità | Percorso della cartella dell'entità all'interno della cartella radice | no | string | entityPath |
| File manifesto: nome del manifesto | Nome del file manifesto. Il valore predefinito è' default '  | No | string | manifestName |
| Filtra per Ultima modifica | Scegliere di filtrare i file in base alla data dell'Ultima modifica | no | Timestamp | modifiedAfter <br> modifiedBefore | 
| Servizio collegato schema | Il servizio collegato in cui si trova il Corpus | Sì, se si usa il manifesto | `'adlsgen2'` o `'github'` | corpusStore | 
| Contenitore di riferimento all'entità | Il corpo del contenitore è in | Sì, se si usano manifest e Corpus in ADLS Gen2 | string | adlsgen2_fileSystem |
| Repository di riferimento all'entità | Nome repository GitHub | Sì, se si usano manifest e Corpus in GitHub | string | github_repository |
| Ramo di riferimento all'entità | Ramo del repository GitHub | Sì, se si usano manifest e Corpus in GitHub | string |  github_branch |
| Cartella Corpus | posizione radice del Corpus | Sì, se si usa il manifesto | string | corpusPath |
| Entità Corpus | Percorso del riferimento all'entità | sì | string | Entità |
| Consenti nessun file trovato | Se true, non viene generato alcun errore se non viene trovato alcun file | no | `true` o `false` | ignoreNoFilesFound |

### <a name="sink-settings"></a>Impostazioni sink

* Puntare al file di riferimento dell'entità CDM che contiene la definizione dell'entità che si desidera scrivere.

![impostazioni entità](media/data-flow/common-data-model-111.png "Riferimento a entità")

* Definire il percorso della partizione e il formato dei file di output che si desidera utilizzare con ADF per la scrittura delle entità.

![formato entità](media/data-flow/common-data-model-222.png "Formato entità")

* Impostare il percorso del file di output e il percorso e il nome del file manifesto.

![posizione CDM](media/data-flow/common-data-model-333.png "Posizione CDM")


#### <a name="import-schema"></a>Importa schema

CDM è disponibile solo come set di dati inline e, per impostazione predefinita, non dispone di uno schema associato. Per ottenere i metadati della colonna, fare clic sul pulsante **Importa schema** nella scheda **proiezione** . In questo modo è possibile fare riferimento ai nomi di colonna e ai tipi di dati specificati dal corpus. Per importare lo schema, è necessario che sia attiva una [sessione di debug del flusso di dati](concepts-data-flow-debug-mode.md) ed è necessario disporre di un file di definizione dell'entità CDM esistente a cui puntare.

Quando si esegue il mapping delle colonne del flusso di dati alle proprietà dell'entità nella trasformazione sink, fare clic sulla scheda "mapping" e selezionare "Importa schema". ADF leggerà il riferimento all'entità a cui è stato fatto riferimento nelle opzioni di sink, consentendo di eseguire il mapping allo schema CDM di destinazione.

![Impostazioni sink CDM](media/data-flow/common-data-model-444.png "Mapping CDM")

> [!NOTE]
>  Quando si usa model.jssu un tipo di origine originato da flussi di dati Power BI o Power Platform, è possibile che si verifichino errori di tipo "il percorso del corpus è null o vuoto" della trasformazione di origine. Questa situazione è probabilmente dovuta a problemi di formattazione del percorso della partizione nel model.jssu file. Per risolvere il problema, attenersi alla seguente procedura: 

1. Aprire il model.jsnel file in un editor di testo
2. Trovare le partizioni. Proprietà Location 
3. Modificare "blob.core.windows.net" in "dfs.core.windows.net"
4. Correzione di qualsiasi codifica "% 2F" nell'URL per "/"
 

### <a name="cdm-source-data-flow-script-example"></a>Esempio di script del flusso di dati di origine CDM

```
source(output(
        ProductSizeId as integer,
        ProductColor as integer,
        CustomerId as string,
        Note as string,
        LastModifiedDate as timestamp
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    manifestType: 'manifest',
    manifestName: 'ProductManifest',
    entityPath: 'Product',
    corpusPath: 'Products',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    folderPath: 'ProductData',
    fileSystem: 'data') ~> CDMSource
```

### <a name="sink-properties"></a>Proprietà sink

La tabella seguente elenca le proprietà supportate da un sink CDM. È possibile modificare queste proprietà nella scheda **Impostazioni** .

| Nome | Descrizione | Obbligatoria | Valori consentiti | Proprietà script flusso di dati |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formato | Il formato deve essere `cdm` | sì | `cdm` | format |
| Percorso radice: contenitore | Nome del contenitore della cartella CDM | sì | string | fileSystem |
| Percorso radice: percorso cartella | Percorso cartella radice della cartella CDM | sì | string | folderPath |
| File manifesto: percorso entità | Percorso della cartella dell'entità all'interno della cartella radice | no | string | entityPath |
| File manifesto: nome del manifesto | Nome del file manifesto. Il valore predefinito è' default ' | No | string | manifestName |
| Servizio collegato schema | Il servizio collegato in cui si trova il Corpus | sì | `'adlsgen2'` o `'github'` | corpusStore | 
| Contenitore di riferimento all'entità | Il corpo del contenitore è in | Sì, se Corpus in ADLS Gen2 | string | adlsgen2_fileSystem |
| Repository di riferimento all'entità | Nome repository GitHub | Sì, se Corpus in GitHub | string | github_repository |
| Ramo di riferimento all'entità | Ramo del repository GitHub | Sì, se Corpus in GitHub | string |  github_branch |
| Cartella Corpus | posizione radice del Corpus | sì | string | corpusPath |
| Entità Corpus | Percorso del riferimento all'entità | sì | string | Entità |
| Percorso partizione | Posizione in cui verrà scritta la partizione | no | string | partitionPath |
| Cancella la cartella | Se la cartella di destinazione viene cancellata prima della scrittura | no | `true` o `false` | truncate |
| Tipo di formato | Scegliere di specificare il formato parquet | no | `parquet` Se specificato | sottoformati |
| Delimitatore di colonna | Se si scrive in DelimitedText, come delimitare le colonne | Sì, se si scrive in DelimitedText | string | columnDelimiter |
| Prima riga come intestazione | Se si usa DelimitedText, se i nomi delle colonne vengono aggiunti come intestazione | no | `true` o `false` | columnNamesAsHeader |

### <a name="cdm-sink-data-flow-script-example"></a>Esempio di script del flusso di dati sink CDM

Lo script del flusso di dati associato è:

```
CDMSource sink(allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    entityPath: 'ProductSize',
    manifestName: 'ProductSizeManifest',
    corpusPath: 'Products',
    partitionPath: 'adf',
    folderPath: 'ProductSizeData',
    fileSystem: 'cdm',
    subformat: 'parquet',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CDMSink

```

## <a name="next-steps"></a>Passaggi successivi

Creazione di una [trasformazione di origine](data-flow-source.md) nel flusso di dati del mapping.
