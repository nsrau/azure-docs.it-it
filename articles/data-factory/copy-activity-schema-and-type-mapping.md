---
title: Mapping dello schema nell'attività di copia | Microsoft Docs
description: Informazioni su come l'attività di copia in Azure Data Factory mappa gli schemi e i tipi di dati dai dati di origine ai dati sink quando si copiano i dati.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: b705123dc6492466c30b3c1ddaf4b330b0d684a1
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71272273"
---
# <a name="schema-mapping-in-copy-activity"></a>Mapping dello schema nell'attività di copia

Questo articolo descrive il modo in cui l'attività di copia Azure Data Factory esegue il mapping dello schema e il mapping dei tipi di dati dai dati di origine ai dati sink quando si esegue la copia dei dati.

## <a name="schema-mapping"></a>Mapping dello schema

Il mapping delle colonne viene applicato per la copia dei dati dall'origine al sink. Per impostazione predefinita, l'attività di copia **mappa i dati di origine a sink in base ai nomi di colonna**. È possibile specificare il [mapping esplicito](#explicit-mapping) per personalizzare il mapping delle colonne in base alle esigenze. Più in particolare, l'attività di copia:

1. Legge i dati dall'origine e determina lo schema di origine
2. Utilizzare il mapping di colonna predefinito per eseguire il mapping delle colonne in base al nome oppure applicare il mapping esplicito della colonna se specificato.
3. Scrivere dati nel sink

### <a name="explicit-mapping"></a>Mapping esplicito

È possibile specificare le colonne di cui eseguire il mapping nell'attività `translator` di copia->  ->  `mappings` proprietà. L'esempio seguente definisce un'attività di copia in una pipeline per copiare dati da testo delimitato al database SQL di Azure.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [{
        "referenceName": "DelimitedTextInput",
        "type": "DatasetReference"
    }],
    "outputs": [{
        "referenceName": "AzureSqlOutput",
        "type": "DatasetReference"
    }],
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": {
                        "name": "UserId",
                        "type": "Guid"
                    },
                    "sink": {
                        "name": "MyUserId"
                    }
                }, 
                {
                    "source": {
                        "name": "Name",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyName"
                    }
                }, 
                {
                    "source": {
                        "name": "Group",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyGroup"
                    }
                }
            ]
        }
    }
}
```

Le proprietà seguenti sono supportate sotto `translator`  ->  `mappings` -> oggetto con `source` e `sink`:

| Proprietà | Descrizione                                                  | Richiesto |
| -------- | ------------------------------------------------------------ | -------- |
| name     | Nome della colonna di origine o sink.                           | Yes      |
| ordinal  | Indice di colonna. Iniziare con 1. <br>Apply e Required quando si usa un testo delimitato senza riga di intestazione. | No       |
| path     | Espressione del percorso JSON per ogni campo da estrarre o mappare. Applicare per i dati gerarchici, ad esempio MongoDB/REST.<br>Per i campi sotto l'oggetto radice, il percorso JSON inizia con la radice $; per i campi all'interno della matrice `collectionReference` scelta dalla proprietà, il percorso JSON inizia dall'elemento della matrice. | No       |
| type     | Data Factory tipo di dati provvisori della colonna di origine o sink. | No       |
| culture  | Impostazioni cultura della colonna di origine o sink. <br>Applicare quando il tipo `Datetime` è `Datetimeoffset`o. Il valore predefinito è `en-us`. | No       |
| format   | Stringa di formato da utilizzare quando il tipo `Datetime` è `Datetimeoffset`o. Per informazioni su come formattare datetime, vedere [Stringhe di formato di data e ora personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). | No       |

Le proprietà seguenti `translator` sono supportate  ->  `mappings` in oltre all'oggetto con `source` e `sink`:

| Proprietà            | Descrizione                                                  | Richiesto |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Supportato solo quando i dati gerarchici, ad esempio MongoDB/REST, sono di origine.<br>Per eseguire l'iterazione dei dati ed estrarli dagli oggetti **presenti nel campo di una matrice** con lo stesso modello e convertirli in una struttura per riga e per oggetto, specificare il percorso JSON di tale matrice per eseguire il cross apply. | No       |

### <a name="alternative-column-mapping"></a>Mapping di colonne alternative

È possibile specificare l'attività di copia `translator` ->  ->  `columnMappings` per eseguire il mapping tra dati a forma di tabulazione. In questo caso, la sezione "Structure" è obbligatoria per i set di dati di input e di output. Il mapping di colonne supporta il **mapping di tutte le colonne o di un sottoinsieme delle colonne nella "struttura" del set di dati di origine a tutte le colonne della "struttura" del set di dati del sink**. Le seguenti sono condizioni di errore che generano un'eccezione:

* Il risultato della query dell'archivio dati di origine non ha un nome colonna specificato nella sezione "struttura" del set di dati di input.
* L'archivio dati sink (con schema predefinito) non ha un nome colonna specificato nella sezione "struttura" del set di dati di output.
* Un numero inferiore o superiore di colonne nella "struttura" del set di dati di sink rispetto a quanto specificato nel mapping.
* Mapping duplicato.

Nell'esempio seguente, il set di dati di input ha una struttura e punta a una tabella in un database Oracle locale.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

In questo esempio, il set di dati di output ha una struttura che punta a una tabella in Salesfoce.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

Il codice JSON seguente definisce un'attività di copia in una pipeline. Le colonne dell'origine vengono mappate alle colonne nel sink utilizzando la proprietà **Translator** -> **ColumnMappings** .

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

La sintassi di `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` per specificare il mapping di colonne è ancora supportata.

### <a name="alternative-schema-mapping"></a>Mapping dello schema alternativo

È possibile specificare l'attività di copia `translator` ->  ->  `schemaMapping` per eseguire il mapping tra i dati a forma di gerarchia e i dati a forma di tabulazione, ad esempio copia da MongoDB/Rest a file di testo e copia da Oracle all'API Azure Cosmos DB per MongoDB. Nella sezione `translator` dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del convertitore dell'attività di copia deve essere impostata su: **TabularTranslator** | Yes |
| schemaMapping | Raccolta di coppie chiave-valore che rappresenta la relazione di mapping **dal lato di origine al lato del sink**.<br/>- **Key:** origine rappresenta. Per l' **origine tabulare**, specificare il nome della colonna come definito nella struttura del set di dati. per l' **origine gerarchica**, specificare l'espressione del percorso JSON per ogni campo da estrarre e mappare.<br>- **Value:** rappresenta sink. Per il **sink tabulare**, specificare il nome della colonna come definito nella struttura del set di dati. per il **sink gerarchico**, specificare l'espressione del percorso JSON per ogni campo da estrarre e mappare. <br>Nel caso di dati gerarchici, per i campi sotto l'oggetto radice, il percorso JSON inizia con la radice $; per i campi all'interno della matrice `collectionReference` scelta dalla proprietà, il percorso JSON inizia dall'elemento della matrice.  | Yes |
| collectionReference | Per eseguire l'iterazione dei dati ed estrarli dagli oggetti **presenti nel campo di una matrice** con lo stesso modello e convertirli in una struttura per riga e per oggetto, specificare il percorso JSON di tale matrice per eseguire il cross apply. Questa proprietà è supportata solo quando l'origine è costituita da dati gerarchici. | No |

**Esempio: copia da MongoDB a Oracle:**

Se ad esempio si ha un il documento di MongoDB con il contenuto seguente:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

e si vuole copiare tale contenuto in una tabella SQL di Azure nel formato seguente, rendendo flat i dati nella matrice *(order_pd e order_price)* e nel crossjoin con le informazioni radice comuni *(numero, data e città)* :

| orderNumber | orderDate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Configurare la regola di mapping dello schema come l'esempio JSON seguente di attività di copia:

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "orderNumber": "$.number",
                "orderDate": "$.date",
                "order_pd": "prod",
                "order_price": "price",
                "city": " $.city[0].name"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>Mapping dei tipi di dati

L'attività di copia esegue il mapping dei tipi di origine ai tipi sink con il metodo seguente in due passaggi:

1. Conversione dai tipi di origine nativi ai tipi di dati provvisori di Azure Data Factory
2. Conversione dai tipi di dati provvisori di Azure Data Factory al tipo di sink nativo

È possibile trovare il mapping dal tipo nativo al tipo provvisorio nella sezione "Mapping dei tipi di dati" di ogni argomento sui connettori.

### <a name="supported-data-types"></a>Tipi di dati supportati

Data Factory supporta i tipi di dati provvisori seguenti: Quando si configurano le informazioni sui tipi nella configurazione della [struttura del set di dati](concepts-datasets-linked-services.md#dataset-structure-or-schema) è possibile specificare i valori seguenti:

* Byte[]
* Boolean
* Datetime
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Single
* String
* Timespan

## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri articoli relativi all'attività di copia:

- [Panoramica dell'attività di copia](copy-activity-overview.md)
