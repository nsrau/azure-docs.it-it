---
title: Set di dati
description: Informazioni sui set di dati in Data Factory. I set di dati rappresentano dati di input o di output.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/25/2019
ms.openlocfilehash: 122725bff616a49d27981b88f465e04418db9526
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826113"
---
# <a name="datasets-in-azure-data-factory"></a>Set di dati in Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-create-datasets.md)
> * [Versione corrente](concepts-datasets-linked-services.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


In questo articolo vengono descritti i set di dati, la procedura di definizione dei set in formato JSON e le modalità di utilizzo nelle pipeline di Azure Data Factory.

Se non si ha dimestichezza con Data Factory, vedere [Introduzione al servizio Azure Data Factory](introduction.md).

## <a name="overview"></a>Panoramica
Una data factory può comprendere una o più pipeline. Una **pipeline** è un raggruppamento logico di **attività** che insieme eseguono un compito. Le attività in una pipeline definiscono le azioni da eseguire sui dati. Un **set di dati** è una visualizzazione dati denominata che punta o fa riferimento ai dati usati come input e output nelle **attività**. I set di dati identificano i dati all'interno dei diversi archivi dati, come tabelle, file, cartelle e documenti. Un set di dati BLOB di Azure, ad esempio, specifica il contenitore BLOB e la cartella nell'archiviazione BLOB da cui l'attività deve leggere i dati.

Prima di creare un set di dati, è necessario creare un [**servizio collegato**](concepts-linked-services.md) per collegare l'archivio dati alla data factory. I servizi collegati sono molto simili a stringhe di connessione e definiscono le informazioni necessarie per la connessione di Data Factory a risorse esterne. In altre parole, il set di dati rappresenta la struttura dei dati all'interno degli archivi dati collegati e il servizio collegato definisce la connessione all'origine dati. Il servizio collegato Archiviazione di Azure,ad esempio, collega l'account di archiviazione alla data factory. Un set di dati BLOB di Azure rappresenta il contenitore BLOB e la cartella all'interno dell'account di Archiviazione di Azure che contiene i BLOB di input da elaborare.

Di seguito è riportato uno scenario di esempio. Per copiare i dati da un'archiviazione BLOB a un database SQL, si creano due servizi collegati: Archiviazione di Azure e database SQL di Azure. Si creano quindi due set di dati: un set di dati BLOB di Azure, che si riferisce al servizio collegato Archiviazione di Azure, e un set di dati della tabella SQL di Azure, che si riferisce al servizio collegato Database SQL di Azure. I servizi collegati Archiviazione di Azure e Database SQL di Azure contengono stringhe di connessione usate da Data Factory in fase di runtime per connettersi rispettivamente all'archiviazione di Azure e al database SQL di Azure. Il set di dati BLOB di Azure specifica il contenitore e una cartella BLOB che contengono i BLOB di input presenti nell'archiviazione BLOB di Azure. Il set di dati della tabella SQL di Azure specifica la tabella SQL del database SQL in cui verranno copiati i dati.

Nel diagramma seguente viene illustrata la relazione tra pipeline, attività, set di dati e il servizio collegato in Data Factory:

![Relazione tra pipeline, attività, set di dati, i servizi collegati](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>Set di dati JSON
Un set di dati in Data Factory viene definito nel formato JSON seguente:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```
La tabella seguente descrive le proprietà nel codice JSON precedente:

Proprietà | Descrizione | Obbligatoria |
-------- | ----------- | -------- |
name | Nome del set di dati. Vedere [Azure Data Factory - Regole di denominazione](naming-rules.md). |  Sì |
type | Tipo del set di dati. Specificare uno dei tipi supportati da Data Factory, ad esempio AzureBlob o AzureSqlTable. <br/><br/>Per informazioni dettagliate, vedere [Tipi di set di dati](#dataset-type). | Sì |
structure | Schema del set di dati. Per informazioni dettagliate, vedere [Schema del set di dati](#dataset-structure-or-schema). | No |
typeProperties | Le proprietà del tipo sono diverse per ogni tipo, ad esempio BLOB di Azure Blob, tabella SQL di Azure. Per informazioni dettagliate sui tipi supportati e le relative proprietà, vedere la sezione [Tipo di set di dati](#dataset-type). | Sì |

### <a name="data-flow-compatible-dataset"></a>Set di dati compatibile con Flusso di dati



Vedere l'elenco di [tipi di set di dati supportati](#dataset-type) compatibili con [Flusso di dati](concepts-data-flow-overview.md). Per i set di dati compatibili con Flusso di dati sono necessarie definizioni di set di dati con granularità fine per le trasformazioni. Quindi, la definizione JSON è leggermente diversa. Invece di una proprietà _structure_, i set di dati compatibili con Flusso di dati hanno una proprietà _schema_.

In Flusso di dati i set di dati vengono usati nelle trasformazioni di origine e sink. I set di dati definiscono gli schemi di base dei dati. Se i dati non hanno uno schema, è possibile usare la deviazione dello schema per l'origine e il sink. Lo schema nel set di dati rappresenta il tipo e la forma dei dati fisici.

Definendo lo schema dal set di dati, si ottengono i tipi di dati, correlati, i formati dei dati, la posizione dei file e le informazioni di connessione dal servizio collegato associato. I metadati dei set di dati vengono visualizzati nella trasformazione di origine come *proiezione* di origine. La proiezione nella trasformazione origine rappresenta i dati di Flusso di dati con nomi e tipi definiti.

Quando si importa lo schema di un set di dati di Flusso di dati, selezionare il pulsante **Importa schema** e scegliere di eseguire l'importazione dall'origine o da un file locale. Nella maggior parte dei casi, lo schema verrà importato direttamente dall'origine. Ma se è già disponibile un file di schema locale (un file Parquet o un CSV con intestazioni), è possibile indicare a Data Factory di usare tale file come base per lo schema.


```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```

La tabella seguente descrive le proprietà nel codice JSON precedente:

Proprietà | Descrizione | Obbligatoria |
-------- | ----------- | -------- |
name | Nome del set di dati. Vedere [Azure Data Factory - Regole di denominazione](naming-rules.md). |  Sì |
type | Tipo del set di dati. Specificare uno dei tipi supportati da Data Factory, ad esempio AzureBlob o AzureSqlTable. <br/><br/>Per informazioni dettagliate, vedere [Tipi di set di dati](#dataset-type). | Sì |
schema | Schema del set di dati. Per informazioni dettagliate, vedere [Set di dati compatibili con Flusso di dati](#dataset-type). | No |
typeProperties | Le proprietà del tipo sono diverse per ogni tipo, ad esempio BLOB di Azure Blob, tabella SQL di Azure. Per informazioni dettagliate sui tipi supportati e le relative proprietà, vedere la sezione [Tipo di set di dati](#dataset-type). | Sì |


## <a name="dataset-example"></a>Esempio di set di dati
Nell'esempio seguente il set di dati rappresenta una tabella denominata MyTable in un database SQL.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": {
                "referenceName": "MyAzureSqlLinkedService",
                "type": "LinkedServiceReference",
        },
        "typeProperties":
        {
            "tableName": "MyTable"
        },
    }
}

```
Tenere presente quanto segue:

- type è impostato su AzureSqlTable.
- La proprietà del tipo tableName, specifica del tipo AzureSqlTable, è impostata su MyTable.
- linkedServiceName fa riferimento a un servizio collegato di tipo AzureSqlDatabase, che è definito nel frammento di codice JSON successivo.

## <a name="dataset-type"></a>Tipo di set di dati
Esistono molti tipi diversi di set di dati, a seconda dell'archivio dati usato. È possibile trovare l'elenco degli archivi dati supportati da Data Factory nell'articolo di [panoramica dei connettori](connector-overview.md). Fare clic su un archivio dati per informazioni su come creare un servizio collegato e un set di dati per tale archivio dati.

Nell'esempio della sezione precedente, il tipo di set di dati è impostato su **AzureSqlTable**. Analogamente, per un set di dati BLOB di Azure, il tipo di set di dati è impostato su **AzureBlob** come illustrato nel codice JSON seguente:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
                "referenceName": "MyAzureStorageLinkedService",
                "type": "LinkedServiceReference",
        },

        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        }
    }
}
```

## <a name="dataset-structure-or-schema"></a>Struttura o schema del set di dati
La sezione **structure** o **schema** (compatibile con Flusso di dati) dei set di dati è facoltativa. Definisce lo schema del set di dati presentando una raccolta di nomi e tipi di dati delle colonne. Usare la sezione structure per inserire informazioni sul tipo da usare per convertire i tipi e mappare le colonne dall'origine alla destinazione.

Ogni colonna della struttura contiene le proprietà seguenti:

Proprietà | Descrizione | Obbligatoria
-------- | ----------- | --------
name | Nome della colonna. | Sì
type | Tipo di dati della colonna. Data Factory supporta come valori consentiti i tipi di dati provvisori seguenti: **Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetime, Datetimeoffset e Timespan** | No
culture | Cultura basata su .NET da usare quando il tipo è un tipo .NET: `Datetime` o `Datetimeoffset`. Il valore predefinito è `en-us`. | No
format | Stringa di formato da usare quando il tipo è un tipo .NET: `Datetime` o `Datetimeoffset`. Per informazioni su come formattare datetime, vedere [Stringhe di formato di data e ora personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). | No

### <a name="example"></a>Esempio
Nell'esempio seguente si supponga che i dati BLOB di origine siano in formato CSV e contengano tre colonne: userid, name e lastlogindate. Sono di tipo Int64, String e Datetime con un formato datetime personalizzato che usa nomi abbreviati francesi per il giorno della settimana.

Definire la struttura dei set di dati BLOB come indicato di seguito e le definizioni di tipo per le colonne:

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Materiale sussidiario

Seguire queste linee guida per sapere quando includere le informazioni sulla struttura e quali elementi inserire nella sezione **structure**. Per altre informazioni sul modo in cui la data factory esegue il mapping dei dati di origine al sink e su quando specificare le informazioni sulla struttura, vedere [Mapping dello schema e dei tipi](copy-activity-schema-and-type-mapping.md).

- **Per le origini dati con schema sicuro**, specificare la sezione structure solo se si vuole eseguire il mapping delle colonne di origine alle colonne del sink e i nomi delle colonne non sono uguali. Questa tipologia di origine dati strutturata archivia le informazioni relative al tipo e allo schema dei dati insieme ai dati stessi. Alcuni esempi di origini dati strutturate sono SQL Server, Oracle e il database SQL di Azure.<br/><br/>Poiché per le origini dati strutturate le informazioni sul tipo sono già disponibili, non includere le informazioni sul tipo quando si include la sezione structure.
- **Per le origini dati senza schema o con schema vulnerabile, ad esempio un file di testo in archiviazione BLOB**, includere la sezione structure quando il set di dati è un input per un'attività di copia e i tipi di dati del set di dati di origine devono essere convertiti in tipi nativi per il sink. Includere inoltre la sezione structure per eseguire il mapping delle colonne di origine alle colonne del sink

## <a name="create-datasets"></a>Creare set di dati
È possibile creare set di dati tramite uno di questi strumenti o SDK: [API .NET](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [API REST](quickstart-create-data-factory-rest-api.md), modello di Azure Resource Manager e portale di Azure

## <a name="current-version-vs-version-1-datasets"></a>Set di dati della versione corrente e set di dati della versione 1

Di seguito sono riportate alcune differenze tra i set di dati di Data Factory e quelli di Data Factory versione 1:

- La proprietà external non è supportata nella versione corrente. e viene sostituita da un [trigger](concepts-pipeline-execution-triggers.md).
- Le proprietà policy e availability non sono supportate nella versione corrente. L'ora di inizio di una pipeline dipende da [trigger](concepts-pipeline-execution-triggers.md).
- I set di dati con ambito (set di dati definiti in una pipeline) non sono supportati nella versione corrente.

## <a name="next-steps"></a>Passaggi successivi
Vedere le esercitazioni seguenti per istruzioni dettagliate sulla creazione di pipeline e set di dati tramite uno di questi strumenti o SDK.

- [Quickstart: create a data factory using .NET](quickstart-create-data-factory-dot-net.md) (Avvio rapido: Creare una data factory tramite .NET)
- [Quickstart: create a data factory using PowerShell](quickstart-create-data-factory-powershell.md) (Avvio rapido: Creare una data factory tramite PowerShell)
- [Quickstart: create a data factory using REST API](quickstart-create-data-factory-rest-api.md) (Avvio rapido: Creare una data factory tramite API REST)
- [Avvio rapido: Creare una data factory con il portale di Azure](quickstart-create-data-factory-portal.md)
