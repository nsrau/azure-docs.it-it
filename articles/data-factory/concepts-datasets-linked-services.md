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
ms.date: 08/24/2020
ms.openlocfilehash: 621d39a684495edadf6c3134635ade6b86a4ab77
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798228"
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

Di seguito è riportato uno scenario di esempio. Per copiare dati da un archivio BLOB a un database SQL, si creano due servizi collegati: archiviazione BLOB di Azure e database SQL di Azure. Quindi, creare due set di dati: set di dati di testo delimitato, che fa riferimento al servizio collegato di archiviazione BLOB di Azure, presupponendo che siano presenti file di testo come origine, e il set di dati della tabella SQL di Azure, che fa riferimento al servizio collegato del database SQL di Azure. I servizi collegati archiviazione BLOB di Azure e database SQL di Azure contengono stringhe di connessione che Data Factory USA in fase di esecuzione per connettersi rispettivamente all'archiviazione di Azure e al database SQL di Azure. Il set di dati di testo delimitato specifica il contenitore BLOB e la cartella BLOB che contiene i BLOB di input nell'archivio BLOB, insieme alle impostazioni relative al formato. Il set di dati della tabella SQL di Azure specifica la tabella SQL del database SQL in cui verranno copiati i dati.

Nel diagramma seguente viene illustrata la relazione tra pipeline, attività, set di dati e il servizio collegato in Data Factory:

![Relazione tra pipeline, attività, set di dati, i servizi collegati](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>Set di dati JSON
Un set di dati in Data Factory viene definito nel formato JSON seguente:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: DelimitedText, AzureSqlTable etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema":[

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
type | Tipo del set di dati. Specificare uno dei tipi supportati da Data Factory (ad esempio: DelimitedText, AzureSqlTable). <br/><br/>Per informazioni dettagliate, vedere [Tipi di set di dati](#dataset-type). | Sì |
schema | Schema del set di dati, che rappresenta il tipo di dati fisico e la forma. | No |
typeProperties | Le proprietà del tipo sono diverse per ogni tipo. Per informazioni dettagliate sui tipi supportati e le relative proprietà, vedere la sezione [Tipo di set di dati](#dataset-type). | Sì |

Quando si importa lo schema del set di dati, selezionare il pulsante **Importa schema** e scegliere di importare dall'origine o da un file locale. Nella maggior parte dei casi, lo schema verrà importato direttamente dall'origine. Ma se è già disponibile un file di schema locale (un file Parquet o un CSV con intestazioni), è possibile indicare a Data Factory di usare tale file come base per lo schema.

Nell'attività di copia i set di dati vengono usati in origine e sink. Lo schema definito nel set di dati è facoltativo come riferimento. Se si desidera applicare il mapping di colonne/campi tra l'origine e il sink, vedere [schema e mapping dei tipi](copy-activity-schema-and-type-mapping.md).

In Flusso di dati i set di dati vengono usati nelle trasformazioni di origine e sink. I set di dati definiscono gli schemi di base dei dati. Se i dati non hanno uno schema, è possibile usare la deviazione dello schema per l'origine e il sink. I metadati dei set di dati vengono visualizzati nella trasformazione di origine come proiezione di origine. La proiezione nella trasformazione origine rappresenta i dati di Flusso di dati con nomi e tipi definiti.

## <a name="dataset-type"></a>Tipo di set di dati

Azure Data Factory supporta molti tipi diversi di set di dati, a seconda degli archivi dati usati. È possibile trovare l'elenco degli archivi dati supportati da Data Factory dall'articolo [Panoramica del connettore](connector-overview.md) . Fare clic su un archivio dati per informazioni su come creare un servizio collegato e un set di dati.

Per un set di dati di testo delimitato, ad esempio, il tipo di set di dati è impostato su **DelimitedText** , come illustrato nell'esempio JSON seguente:

```json
{
    "name": "DelimitedTextInput",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorage",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "input.log",
                "folderPath": "inputdata",
                "container": "adfgetstarted"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

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
