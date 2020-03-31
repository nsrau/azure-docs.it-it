---
title: Mapping dello schema nell'attività di copia
description: Informazioni su come l'attività di copia in Azure Data Factory mappa gli schemi e i tipi di dati dai dati di origine ai dati sink quando si copiano i dati.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: jingwang
ms.openlocfilehash: 9ae07e2a471cc417b467092a2616a5a0cdafb1fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260813"
---
# <a name="schema-mapping-in-copy-activity"></a>Mapping dello schema nell'attività di copia

Questo articolo descrive come l'attività di copia di Azure Data Factory esegue il mapping dello schema e del tipo di dati dai dati di origine ai dati sink durante l'esecuzione della copia dei dati.

## <a name="schema-mapping"></a>Mapping dello schema

Il mapping delle colonne viene applicato quando si copiano dati dall'origine al sink. Per impostazione predefinita, copiare i dati di origine del mapping attività **in sink in base ai nomi**di colonna . È possibile specificare il [mapping esplicito](#explicit-mapping) per personalizzare il mapping delle colonne in base alle proprie esigenze. Più in particolare, l'attività di copia:

1. Legge i dati dall'origine e determina lo schema di origine
2. Utilizzare il mapping di colonne predefinito per eseguire il mapping delle colonne in base al nome o applicare il mapping esplicito delle colonne, se specificato.
3. Scrivere dati nel sink

### <a name="explicit-mapping"></a>Mapping esplicito

È possibile specificare le colonne di `translator`  ->  `mappings` cui eseguire il mapping nella proprietà -> attività di copia. L'esempio seguente definisce un'attività di copia in una pipeline per copiare i dati dal testo delimitato al database SQL di Azure.The following example defines a copy activity in a pipeline to copy data from delimited text to Azure SQL Database.

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

Le seguenti proprietà `translator`  ->  `mappings` sono supportate `source` `sink`in -> oggetto con e :

| Proprietà | Descrizione                                                  | Obbligatoria |
| -------- | ------------------------------------------------------------ | -------- |
| name     | Nome della colonna di origine o di sink.                           | Sì      |
| ordinal  | Indice di colonna. Iniziare con 1. <br>Applica e obbligatorio quando si utilizza testo delimitato senza riga di intestazione. | No       |
| path     | Espressione di percorso JSON per ogni campo da estrarre o mappare. Richiedere dati gerarchici, ad esempio MongoDB/REST.<br>Per i campi nell'oggetto radice, il percorso JSON inizia con root ; per i campi all'interno della matrice scelta dalla `collectionReference` proprietà, il percorso JSON inizia dall'elemento della matrice. | No       |
| type     | Tipo di dati provvisori di Data Factory della colonna di origine o sink. | No       |
| culture  | Impostazioni cultura della colonna di origine o del sink. <br>Applicare quando `Datetime` il `Datetimeoffset`tipo è o . Il valore predefinito è `en-us`. | No       |
| format   | Stringa di formato da `Datetime` utilizzare `Datetimeoffset`quando type è o . Per informazioni su come formattare datetime, vedere [Stringhe di formato di data e ora personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). | No       |

Le seguenti proprietà `translator`  ->  `mappings` sono supportate `source` in `sink`oltre all'oggetto con e :

| Proprietà            | Descrizione                                                  | Obbligatoria |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Supportato solo quando i dati gerarchici, ad esempio MongoDB/REST sono di origine.<br>Per eseguire l'iterazione dei dati ed estrarli dagli oggetti **presenti nel campo di una matrice** con lo stesso modello e convertirli in una struttura per riga e per oggetto, specificare il percorso JSON di tale matrice per eseguire il cross apply. | No       |

### <a name="alternative-column-mapping"></a>Mapping di colonne alternativo

È possibile specificare `translator`  ->  `columnMappings` l'attività di copia -> eseguire il mapping tra dati di forma tabulare. In questo caso, la sezione "struttura" è necessaria sia per i set di dati di input che per quelli di output. Il mapping di colonne supporta il **mapping di tutte le colonne o di un sottoinsieme delle colonne nella "struttura" del set di dati di origine a tutte le colonne della "struttura" del set di dati del sink**. Le seguenti sono condizioni di errore che generano un'eccezione:

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

In this sample, the output dataset has a structure and it points to a table in Salesfoce.

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

Il codice JSON seguente definisce un'attività di copia in una pipeline. Le colonne dall'origine mappate alle colonne nel sink utilizzando la proprietà**columnMappings** del **traduttore.** -> 

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

È possibile specificare `translator`  ->  `schemaMapping` l'attività di copia -> per eseguire il mapping tra dati di forma gerarchica e dati a forma tabulare, ad esempio copiare da MongoDB/REST al file di testo e copiare da Oracle all'API di Azure Cosmos DB per MongoDB. Nella sezione `translator` dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del traduttore dell'attività di copia deve essere impostata su: **TabularTranslator** | Sì |
| schemaMapping | Raccolta di coppie chiave-valore, che rappresenta la relazione di mapping **dal lato di origine al lato del sink.**<br/>- **Chiave:** rappresenta l'origine. Per **l'origine tabulare**, specificare il nome della colonna come definito nella struttura del set di dati; per **l'origine gerarchica**, specificare l'espressione di percorso JSON per ogni campo da estrarre e mappare.<br>- **Valore:** rappresenta il sink. Per **sink tabulare**, specificare il nome della colonna come definito nella struttura del set di dati; per **il sink gerarchico**, specificare l'espressione di percorso JSON per ogni campo da estrarre e mappare. <br>Nel caso dei dati gerarchici, per i campi nell'oggetto radice, il percorso JSON inizia con root; per i campi all'interno della matrice scelta dalla `collectionReference` proprietà, il percorso JSON inizia dall'elemento della matrice.  | Sì |
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

e si vuole copiare tale contenuto in una tabella SQL di Azure nel formato seguente, rendendo flat i dati nella matrice *(order_pd e order_price)* e nel crossjoin con le informazioni radice comuni *(numero, data e città)*:

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
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
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

Data Factory supporta i seguenti tipi di dati intermedi: è possibile specificare i valori seguenti durante la configurazione delle informazioni sul tipo nella configurazione della struttura del set di dati:Data Factory supports the following interim data types: You can specify below values when configuring type information in [dataset structure](concepts-datasets-linked-services.md#dataset-structure-or-schema) configuration:

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
* string
* TimeSpan

## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri articoli relativi all'attività di copia:

- [Panoramica dell'attività di copia](copy-activity-overview.md)
