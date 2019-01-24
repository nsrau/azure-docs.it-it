---
title: Mapping dello schema nell'attività di copia | Microsoft Docs
description: Informazioni su come l'attività di copia in Azure Data Factory mappa gli schemi e i tipi di dati dai dati di origine ai dati sink quando si copiano i dati.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: c2f58a3510699cdf74e3150d3ad5882929f4f05b
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358712"
---
# <a name="schema-mapping-in-copy-activity"></a>Mapping dello schema nell'attività di copia
Questo articolo illustra come l'attività di copia di Azure Data Factory esegue il mapping dello schema e dei tipi di dati dall'origine al sink quando si copiano dati.

## <a name="column-mapping"></a>Mapping di colonne

Il mapping di colonne si applica quando si esegue la copia tra dati in formato tabulare. Per impostazione predefinita, l'attività di copia **esegue il mapping dei dati di origine al sink in base ai nomi delle colonne** a meno che non sia configurato il [mapping esplicito di colonne](#explicit-column-mapping). Più in particolare, l'attività di copia:

1. Legge i dati dall'origine e determina lo schema di origine

    * Per le origini dati con schema predefinito nel formato archivio dati/file, ad esempio database/file con metadati (Avro, ORC, Parquet, Text con intestazione), lo schema di origine viene estratto dal risultato della query o dai metadati del file.
    * Per le origini di dati con schema flessibile, ad esempio tabella di Azure/Cosmos DB, lo schema di origine viene dedotto dal risultato della query. È possibile sovrascriverlo configurando la "struttura" nel set di dati.
    * Per il file di testo senza intestazione, i nomi di colonna predefiniti vengono generati seguendo il criterio "Prop_0", "Prop_1" e così via. È possibile sovrascriverli configurando la "struttura" nel set di dati.
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

In questo esempio la tabella di input ha una struttura che fa riferimento a una tabella in un database SQL locale.

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

**Flusso del mapping di colonne:**

![Flusso del mapping di colonne](./media/copy-activity-schema-and-type-mapping/column-mapping-sample.png)

## <a name="schema-mapping"></a>Mapping dello schema

Il mapping dello schema si applica quando si esegue la copia tra dati in formato gerarchico e dati in formato tabulare, ad esempio quando si copiano dati da MongoDB/REST a file di testo e da SQL all'API di Azure Cosmos DB per MongoDB. Nella sezione `translator` dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del convertitore dell'attività di copia deve essere impostata su: **TabularTranslator** | Yes |
| schemaMapping | Una raccolta di coppie chiave-valore, che rappresenta la relazione di mapping dai dati in formato tabulare a quelli in formato gerarchico.<br/>- **Chiave:** il nome della colonna di dati tabulari come definito nella struttura del set di dati.<br/>- **Valore:** l'espressione di percorso JSON per ogni campo da estrarre e sottoporre al mapping. Per i campi sotto l'oggetto radice, iniziare con la radice $. Per i campi nella matrice scelta dalla proprietà `collectionReference`, iniziare dall'elemento matrice.  | Yes |
| collectionReference | Per eseguire l'iterazione dei dati ed estrarli dagli oggetti **presenti nel campo di una matrice** con lo stesso modello e convertirli in una struttura per riga e per oggetto, specificare il percorso JSON di tale matrice per eseguire il cross apply. Questa proprietà è supportata solo quando l'origine è costituita da dati gerarchici. | No  |

**Esempio: copiare da MongoDB a SQL**.

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
    "name": "CopyFromMongoDBToSqlAzure",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "SqlSink"
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

Data Factory supporta i tipi di dati provvisori seguenti: Quando si configurano le informazioni sui tipi nella configurazione della [struttura del set di dati](concepts-datasets-linked-services.md#dataset-structure) è possibile specificare i valori seguenti:

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

* Copia da un file di testo senza intestazione (set di dati di input). È possibile specificare i nomi delle colonne per l'allineamento del file di testo con le colonne sink corrispondenti per non dover configurare il mapping esplicito di colonne.
* Copia dagli archivi di dati con schema flessibile, ad esempio tabella Azure o Cosmos DB (set di dati di input), per garantire che i dati previsti (colonne) vengano copiati anziché lasciare che l'attività di copia deduca lo schema in base alle righe superiori durante ogni esecuzione dell'attività.


## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri articoli relativi all'attività di copia:

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Tolleranza di errore dell'attività di copia](copy-activity-fault-tolerance.md)
- [Prestazioni dell'attività di copia](copy-activity-performance.md)
