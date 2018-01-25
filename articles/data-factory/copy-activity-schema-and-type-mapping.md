---
title: "Mapping dello schema nell'attività di copia | Microsoft Docs"
description: "Informazioni su come l'attività di copia in Azure Data Factory mappa gli schemi e i tipi di dati dai dati di origine ai dati sink quando si copiano i dati."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: jingwang
ms.openlocfilehash: c1fde0abd2f5fa8e36c71707d14cd5afeb3a0d2d
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="schema-mapping-in-copy-activity"></a>Mapping dello schema nell'attività di copia
Questo articolo illustra come l'attività di copia di Azure Data Factory esegue il mapping dello schema e il mapping dei tipi di dati dai dati di origine ai dati sink quando viene eseguita la copia dei dati.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-introduction.md).


## <a name="column-mapping"></a>Mapping di colonne

Per impostazione predefinita, l'attività di copia **esegue il mapping dei dati di origine al sink in base ai nomi delle colonne** a meno che non sia configurato il [mapping esplicito di colonne](#explicit-column-mapping). Più in particolare, l'attività di copia:

1. Legge i dati dall'origine e determina lo schema di origine

    * Per le origini dati con schema predefinito nel formato archivio dati/file, ad esempio database/file con metadati (Avro, ORC, Parquet, Text con intestazione), lo schema di origine viene estratto dal risultato della query o dai metadati del file.
    * Per le origini di dati con schema flessibile, ad esempio tabella di Azure/Cosmos DB, lo schema di origine viene dedotto dal risultato della query. È possibile sovrascriverlo specificando la "struttura" nel set di dati.
    * Per i file di testo senza intestazione, i nomi di colonna predefiniti vengono generati seguendo il criterio "Prop_0", "Prop_1", e così via. È possibile sovrascriverli specificando la "struttura" nel set di dati.
    * Per le origini Dynamics è necessario fornire le informazioni dello schema nella sezione "struttura" del set di dati.

2. Se specificato, applicare il mapping esplicito di colonne.

3. Scrivere dati nel sink

    * Per gli archivi di dati con schema predefinito, i dati vengono scritti nelle colonne con lo stesso nome.
    * Per gli archivi di dati senza uno schema fisso e per i formati di file, i nomi colonna/metadati verranno generati in base allo schema di origine.

### <a name="explicit-column-mapping"></a>Mapping esplicito di colonne

È possibile specificare il valore **columnMapping** nella sezione **typeProperties** dell'attività di copia per eseguire il mapping esplicito di colonne. In questo scenario la sezione "struttura" è necessaria per entrambi i set di dati di input e output. Il mapping di colonne supporta il **mapping di tutte le colonne o di un sottoinsieme delle colonne nella "struttura" del set di dati di origine a tutte le colonne della "struttura" del set di dati del sink**. Le seguenti sono condizioni di errore che generano un'eccezione:

* Il risultato della query dell'archivio dati di origine non ha un nome colonna specificato nella sezione "struttura" del set di dati di input.
* L'archivio dati sink (con schema predefinito) non ha un nome colonna specificato nella sezione "struttura" del set di dati di output.
* Un numero inferiore o superiore di colonne nella "struttura" del set di dati di sink rispetto a quanto specificato nel mapping.
* Mapping duplicato.

#### <a name="explicit-column-mapping-example"></a>Esempio di mapping esplicito di colonne

In questo esempio, la tabella di input ha una struttura che fa riferimento a una tabella in un database SQL locale.

```json
{
    "name": "SqlServerInput",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "SqlServerLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

In questo esempio, la tabella di output ha una struttura che fa riferimento a una tabella in un database SQL di Azure.

```json
{
    "name": "AzureSqlOutput",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

Il codice JSON seguente definisce un'attività di copia in una pipeline. Le colonne dell'origine vengono mappate alle colonne del sink (**columnMappings**) usando la proprietà **translator**.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "SqlServerInput",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "AzureSqlOutput",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "SqlSource" },
        "sink": { "type": "SqlSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    }
}
```

**Flusso del mapping di colonne:**

![Flusso del mapping di colonne](./media/copy-activity-schema-and-type-mapping/column-mapping-sample.png)

## <a name="data-type-mapping"></a>Mapping dei tipi di dati

L'attività di copia esegue il mapping dei tipi di origine ai tipi sink con il metodo seguente in due passaggi:

1. Conversione dai tipi di origine nativi ai tipi di dati provvisori di Azure Data Factory
2. Conversione dai tipi di dati provvisori di Azure Data Factory al tipo di sink nativo

È possibile trovare il mapping dal tipo nativo al tipo provvisorio nella sezione "Mapping dei tipi di dati" di ogni argomento sui connettori.

### <a name="supported-data-types"></a>Tipi di dati supportati

Data Factory supporta i seguenti tipi di dati provvisori. È possibile specificare i valori seguenti quando si forniscono informazioni sul tipo nella configurazione della [struttura del set di dati](concepts-datasets-linked-services.md#dataset-structure):

* Byte[]
* boolean
* DateTime
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Single
* string
* TimeSpan

### <a name="explicit-data-type-conversion"></a>Esempio di conversione esplicita dei tipi di dati

Quando si copiano i dati negli archivi di dati con schema predefinito, ad esempio SQL Server/Oracle, o quando l'origine e il sink hanno un tipo diverso nella stessa colonna, è necessario dichiarare la conversione esplicita del tipo nel lato di origine:

* Per l'origine file, ad esempio CSV/Avro, è necessario dichiarare la conversione del tipo tramite la struttura di origine con l'elenco completo delle colonne (nome colonna del lato di origine e tipo del lato sink).
* Per l'origine relazionale (ad esempio, SQL/Oracle), è necessario ottenere la conversione del tipo eseguendo il cast esplicito del tipo nell'istruzione della query.

## <a name="when-to-specify-dataset-structure"></a>Quando specificare la "struttura" del set di dati

Negli scenari seguenti è necessario specificare la "struttura" nel set di dati:

* Applicazione della [conversione esplicita dei tipi di dati](#explicit-data-type-conversion) per le origini file durante la copia (set di dati di input)
* Applicazione del [mapping esplicito di colonne](#explicit-column-mapping) durante la copia (entrambi i set di dati di input e output)
* Copia dall'origine Dynamics 365/CRM (set di dati di input)
* Copia in Cosmos DB come oggetto annidato quando l'origine non è un file JSON (set di dati di output)

Negli scenari seguenti è consigliabile specificare la "struttura" nel set di dati:

* Copia da un file di testo senza intestazione (set di dati di input). È possibile specificare i nomi delle colonne per l'allineamento del file di testo con le colonne sink corrispondenti per non dover specificare il mapping esplicito di colonne.
* Copia dagli archivi di dati con schema flessibile, ad esempio tabella Azure o Cosmos DB (set di dati di input), per garantire che i dati previsti (colonne) vengano copiati anziché lasciare che l'attività di copia deduca lo schema in base alle righe superiori durante ogni esecuzione dell'attività.


## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri articoli relativi all'attività di copia:

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Tolleranza di errore dell'attività di copia](copy-activity-fault-tolerance.md)
- [Prestazioni dell'attività di copia](copy-activity-performance.md)
