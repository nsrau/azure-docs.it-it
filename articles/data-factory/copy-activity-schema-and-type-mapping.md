---
title: Schema e mapping dei tipi di dati nell'attività di copia
description: Informazioni sul modo in cui l'attività di copia in Azure Data Factory esegue il mapping di schemi e tipi di dati dai dati di origine ai dati sink.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: jingwang
ms.openlocfilehash: b48fb28a56cdc1c836233cd2bd03a1f9e750a0a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85249653"
---
# <a name="schema-and-data-type-mapping-in-copy-activity"></a>Schema e mapping dei tipi di dati nell'attività di copia
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo descrive il modo in cui l'attività di copia Azure Data Factory esegue il mapping dello schema e il mapping dei tipi di dati dai dati di origine ai dati sink.

## <a name="schema-mapping"></a>Mapping dello schema

### <a name="default-mapping"></a>Mapping predefinito

Per impostazione predefinita, l'attività di copia esegue il mapping dei dati di origine al sink in **base ai nomi delle colonne** in modo che siano sensibili Se il sink non esiste, ad esempio scrivendo nei file, i nomi dei campi di origine verranno resi persistenti come nomi di sink. Tale mapping predefinito supporta schemi flessibili e la deriva dello schema dall'origine al sink dall'esecuzione all'esecuzione. tutti i dati restituiti dall'archivio dati di origine possono essere copiati in sink.

Se l'origine è un file di testo senza riga di intestazione, il [mapping esplicito](#explicit-mapping) è necessario perché l'origine non contiene nomi di colonna.

### <a name="explicit-mapping"></a>Mapping esplicito

È anche possibile specificare il mapping esplicito per personalizzare il mapping di colonne/campi dall'origine al sink in base alle esigenze. Con il mapping esplicito è possibile copiare solo i dati di origine parziali nel sink o eseguire il mapping dei dati di origine al sink con nomi diversi oppure modificare la forma dei dati tabulari o gerarchici. Attività di copia:

1. Legge i dati dall'origine e determina lo schema di origine.
2. Applica il mapping definito.
3. Scrive i dati nel sink.

Sono disponibili altre informazioni su:

- [Da origine tabulare a sink tabulare](#tabular-source-to-tabular-sink)
- [Da origine gerarchica a sink tabulare](#hierarchical-source-to-tabular-sink)
- [Da origine tabulare/gerarchica a sink gerarchico](#tabularhierarchical-source-to-hierarchical-sink)

È possibile configurare il mapping nell'interfaccia utente di Data Factory creazione > dell'attività di copia-> scheda mapping o specificare a livello di codice il mapping nella proprietà attività di copia-> `translator` . Le proprietà seguenti sono supportate negli `translator`  ->  `mappings` oggetti Array->-> `source` e `sink` , che punta alla colonna o al campo specifico per eseguire il mapping dei dati.

| Proprietà | Descrizione                                                  | Obbligatoria |
| -------- | ------------------------------------------------------------ | -------- |
| name     | Nome del campo o della colonna di origine o del sink. Applicare per l'origine e il sink tabulari. | Sì      |
| ordinal  | Indice di colonna. Iniziare da 1. <br>Apply e Required quando si usa un testo delimitato senza riga di intestazione. | No       |
| path     | Espressione del percorso JSON per ogni campo da estrarre o mappare. Applicare per l'origine e il sink gerarchici, ad esempio Cosmos DB, MongoDB o i connettori REST.<br>Per i campi sotto l'oggetto radice, il percorso JSON inizia con la radice. `$` per i campi all'interno della matrice scelta dalla `collectionReference` proprietà, il percorso JSON inizia dall'elemento della matrice senza `$` . | No       |
| type     | Data Factory tipo di dati provvisori della colonna di origine o sink. In generale, non è necessario specificare o modificare questa proprietà. Altre informazioni sul [mapping dei tipi di dati](#data-type-mapping). | No       |
| culture  | Impostazioni cultura della colonna di origine o sink. Applicare quando il tipo è `Datetime` o `Datetimeoffset` . Il valore predefinito è `en-us`.<br>In generale, non è necessario specificare o modificare questa proprietà. Altre informazioni sul [mapping dei tipi di dati](#data-type-mapping). | No       |
| format   | Stringa di formato da utilizzare quando il tipo è `Datetime` o `Datetimeoffset` . Per informazioni su come formattare datetime, vedere [Stringhe di formato di data e ora personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). In generale, non è necessario specificare o modificare questa proprietà. Altre informazioni sul [mapping dei tipi di dati](#data-type-mapping). | No       |

Le proprietà seguenti sono supportate `translator` in oltre a `mappings` :

| Proprietà            | Descrizione                                                  | Obbligatoria |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Applicare quando si copiano dati da un'origine gerarchica, ad esempio Cosmos DB, MongoDB o connettori REST.<br>Per eseguire l'iterazione dei dati ed estrarli dagli oggetti **presenti nel campo di una matrice** con lo stesso modello e convertirli in una struttura per riga e per oggetto, specificare il percorso JSON di tale matrice per eseguire il cross apply. | No       |

#### <a name="tabular-source-to-tabular-sink"></a>Da origine tabulare a sink tabulare

Ad esempio, per copiare dati da Salesforce nel database SQL di Azure ed eseguire il mapping esplicito di tre colonne:

1. Nella scheda mapping dell'attività di copia > fare clic sul pulsante **Importa schema** per importare gli schemi di origine e sink.

2. Eseguire il mapping dei campi necessari ed escludere/eliminare il resto.

![Mappa tabulare a tabulare](media/copy-activity-schema-and-type-mapping/map-tabular-to-tabular.png)

Lo stesso mapping può essere configurato come segue nel payload dell'attività di copia (vedere `translator` ):

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "SalesforceSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "name": "Id" },
                    "sink": { "name": "CustomerID" }
                },
                {
                    "source": { "name": "Name" },
                    "sink": { "name": "LastName" }
                },
                {
                    "source": { "name": "LastModifiedDate" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

Per copiare dati da file di testo delimitati senza riga di intestazione, le colonne sono rappresentate in base al numero ordinale anziché ai nomi. 

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "ordinal": "1" },
                    "sink": { "name": "CustomerID" }
                }, 
                {
                    "source": { "ordinal": "2" },
                    "sink": { "name": "LastName" }
                }, 
                {
                    "source": { "ordinal": "3" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

#### <a name="hierarchical-source-to-tabular-sink"></a>Da origine gerarchica a sink tabulare

Quando si copiano dati da un'origine gerarchica a un sink tabulare, l'attività di copia supporta le funzionalità seguenti:

- Estrarre dati da oggetti e matrici.
- Cross applica più oggetti con lo stesso modello da una matrice, nel qual caso è possibile convertire un oggetto JSON in più record in risultati tabulari.

Per una trasformazione gerarchica a tabella più avanzata, è possibile utilizzare il [flusso di dati](concepts-data-flow-overview.md). 

Ad esempio, se si dispone di un documento di origine MongoDB con il contenuto seguente:

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

E si desidera copiarlo in un file di testo nel formato seguente con la riga di intestazione, rendendo flat i dati all'interno della matrice *(order_pd e order_price)* e cross join con le informazioni radice comuni *(numero, data e città)*:

| orderNumber | orderDate | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

È possibile definire tale mapping in Data Factory interfaccia utente di creazione:

1. Nella scheda mapping dell'attività di copia > fare clic sul pulsante **Importa schema** per importare gli schemi di origine e sink. Data Factory esegue il campionamento dei primi oggetti quando si importa lo schema, se un campo non viene visualizzato, è possibile aggiungerlo al livello corretto nella gerarchia-passare il puntatore del mouse su un nome di campo esistente e scegliere di aggiungere un nodo, un oggetto o una matrice.

2. Selezionare la matrice da cui si desidera eseguire l'iterazione ed estrarre i dati. Verrà popolato automaticamente come **riferimento alla raccolta**. Si noti che per tale operazione è supportata una sola matrice.

3. Eseguire il mapping dei campi necessari per il sink. Data Factory determina automaticamente i percorsi JSON corrispondenti per il lato gerarchico.

![Mappa gerarchica a tabulare usando l'interfaccia utente](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-ui.png)

È anche possibile passare all' **Editor avanzato**, nel qual caso è possibile visualizzare e modificare direttamente i percorsi JSON dei campi. Se si sceglie di aggiungere nuovo mapping in questa visualizzazione, specificare il percorso JSON.

![Eseguire il mapping gerarchico a tabulare mediante l'editor avanzato](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-advanced-editor.png)

Lo stesso mapping può essere configurato come segue nel payload dell'attività di copia (vedere `translator` ):


```json
{
    "name": "CopyActivityHierarchicalToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "MongoDbV2Source" },
        "sink": { "type": "DelimitedTextSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "path": "$['number']" },
                    "sink": { "name": "orderNumber" }
                },
                {
                    "source": { "path": "$['date']" },
                    "sink": { "name": "orderDate" }
                },
                {
                    "source": { "path": "['prod']" },
                    "sink": { "name": "order_pd" }
                },
                {
                    "source": { "path": "['price']" },
                    "sink": { "name": "order_price" }
                },
                {
                    "source": { "path": "$['city'][0]['name']" },
                    "sink": { "name": "city" }
                }
            ],
            "collectionReference": "$['orders']"
        }
    },
    ...
}
```

#### <a name="tabularhierarchical-source-to-hierarchical-sink"></a>Da origine tabulare/gerarchica a sink gerarchico

Il flusso dell'esperienza utente è simile a quello dell' [origine gerarchica al sink tabulare](#hierarchical-source-to-tabular-sink). 

Quando si copiano dati da un'origine tabulare a un sink gerarchico, la scrittura in una matrice all'interno di un oggetto non è supportata.

Quando si copiano dati da un'origine gerarchica a un sink gerarchico, è inoltre possibile mantenere la gerarchia dell'intero livello selezionando l'oggetto o la matrice e il mapping per il sink senza toccare i campi interni.

Per una trasformazione di data shaping più avanzata, è possibile utilizzare il [flusso di dati](concepts-data-flow-overview.md). 

### <a name="parameterize-mapping"></a>Mapping di parametri

Se si desidera creare una pipeline creato un modello per copiare in modo dinamico un numero elevato di oggetti, determinare se è possibile utilizzare il [mapping predefinito](#default-mapping) o se è necessario definire il [mapping esplicito](#explicit-mapping) per i rispettivi oggetti.

Se è necessario un mapping esplicito, è possibile:

1. Definire un parametro con il tipo di oggetto a livello di pipeline, ad esempio `mapping` .

2. Parametrizzare il mapping: nell'attività di copia > scheda mapping, scegliere di aggiungere il contenuto dinamico e selezionare il parametro precedente. Il payload dell'attività sarà analogo al seguente:

    ```json
    {
        "name": "CopyActivityHierarchicalToTabular",
        "type": "Copy",
        "typeProperties": {
            "source": {...},
            "sink": {...},
            "translator": {
                "value": "@pipeline().parameters.mapping",
                "type": "Expression"
            },
            ...
        }
    }
    ```

3. Costruire il valore da passare al parametro di mapping. Deve corrispondere all'intero oggetto della `translator` definizione. vedere la sezione Esempi in [mapping esplicito](#explicit-mapping) . Ad esempio, per l'origine tabulare per la copia di sink tabulari, il valore deve essere `{"type":"TabularTranslator","mappings":[{"source":{"name":"Id"},"sink":{"name":"CustomerID"}},{"source":{"name":"Name"},"sink":{"name":"LastName"}},{"source":{"name":"LastModifiedDate"},"sink":{"name":"ModifiedDate"}}]}` .

## <a name="data-type-mapping"></a>Mapping dei tipi di dati

L'attività di copia esegue il mapping dei tipi di origine ai tipi di sink con il flusso seguente: 

1. Converte i tipi di dati nativi di origine in Azure Data Factory tipi di dati provvisori.
2. Converte automaticamente il tipo di dati provvisorio in base alle esigenze in modo da corrispondere ai tipi di sink corrispondenti, applicabili sia al mapping [predefinito](#default-mapping) sia al [mapping esplicito](#explicit-mapping)
3. Conversione da tipi di dati Azure Data Factory interim a tipi di dati sink nativi.

L'attività di copia supporta attualmente i tipi di dati provvisori seguenti: booleano, byte, matrice di byte, DateTime, DatetimeOffset, Decimal, Double, GUID, Int16, Int32, Int64, SByte, Single, String, TimeSpan, UInt16, UInt32 e UInt64.

Le conversioni dei tipi di dati seguenti sono supportate tra i tipi provvisori dall'origine al sink.

| Source\Sink | Boolean | Matrice di byte | Decimal | Data/ora <small>(1)</small> | Virgola mobile <small>(2)</small> | GUID | Integer <small>(3)</small> | string | TimeSpan |
| ----------- | ------- | ---------- | ------- | ---------------------------- | ------------------------------ | ---- | -------------------------- | ------ | -------- |
| Boolean     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Matrice di byte  |         | ✓          |         |                              |                                |      |                            | ✓      |          |
| Data/ora   |         |            |         | ✓                            |                                |      |                            | ✓      |          |
| Decimal     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Virgola mobile | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| GUID        |         |            |         |                              |                                | ✓    |                            | ✓      |          |
| Integer     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| string      | ✓       | ✓          | ✓       | ✓                            | ✓                              | ✓    | ✓                          | ✓      | ✓        |
| TimeSpan    |         |            |         |                              |                                |      |                            | ✓      | ✓        |

(1) la data e l'ora includono DateTime e DateTimeOffset.

(2) float-Point include single e Double.

(3) Integer include SByte, byte, Int16, UInt16, Int32, UInt32, Int64 e UInt64.

> [!NOTE]
> - Attualmente la conversione del tipo di dati è supportata quando si esegue la copia tra dati tabulari. Le origini e i sink gerarchici non sono supportati, ovvero non esiste alcuna conversione del tipo di dati definita dal sistema tra i tipi provvisori di origine e sink.
> - Questa funzionalità funziona con il modello di set di dati più recente. Se questa opzione non viene visualizzata nell'interfaccia utente, provare a creare un nuovo set di dati.

Le proprietà seguenti sono supportate nell'attività di copia per la conversione del tipo di dati (nella `translator` sezione relativa alla creazione a livello di codice):

| Proprietà                         | Descrizione                                                  | Obbligatoria |
| -------------------------------- | ------------------------------------------------------------ | -------- |
| typeConversion                   | Abilitare la nuova esperienza di conversione del tipo di dati. <br>Il valore predefinito è false a causa della compatibilità con le versioni precedenti.<br><br>Per le nuove attività di copia create tramite l'interfaccia utente di creazione Data Factory dalla fine del 2020 giugno, questa conversione del tipo di dati è abilitata per impostazione predefinita per un'esperienza ottimale ed è possibile visualizzare le seguenti impostazioni di conversione dei tipi nell'attività di copia-scheda mapping > per gli scenari applicabili. <br>Per creare una pipeline a livello di codice, è necessario impostare in modo esplicito la `typeConversion` proprietà su true per abilitarla.<br>Per le attività di copia esistenti create prima del rilascio di questa funzionalità, non verranno visualizzate le opzioni di conversione dei tipi nell'interfaccia utente di creazione Data Factory per la compatibilità con le versioni precedenti. | No       |
| typeConversionSettings           | Gruppo di impostazioni di conversione dei tipi. Applicare quando `typeConversion` è impostato su `true` . Le proprietà seguenti sono tutte sottoposte a questo gruppo. | No       |
| *In `typeConversionSettings`* |                                                              |          |
| allowDataTruncation              | Consente il troncamento dei dati durante la conversione dei dati di origine in sink con tipo diverso durante la copia, ad esempio da Decimal a Integer, da DatetimeOffset a DateTime. <br>Il valore predefinito è true. | No       |
| treatBooleanAsNumber             | Considera i valori booleani come numeri, ad esempio true come 1.<br>Il valore predefinito è false. | No       |
| dateTimeFormat                   | Stringa di formato quando si esegue la conversione tra date senza offset di fuso orario e stringhe, ad esempio `yyyy-MM-dd HH:mm:ss.fff` .  Per informazioni dettagliate, fare riferimento alle [stringhe di formato di data e ora personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) . | No       |
| dateTimeOffsetFormat             | Stringa di formato quando si esegue la conversione tra le date con la differenza di fuso orario e le stringhe, ad esempio `yyyy-MM-dd HH:mm:ss.fff zzz` .  Per informazioni dettagliate, fare riferimento alle [stringhe di formato di data e ora personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) . | No       |
| timeSpanFormat                   | Stringa di formato durante la conversione tra i periodi di tempo e le stringhe, ad esempio `dd\.hh\:mm` . Per informazioni dettagliate, vedere [stringhe di formato TimeSpan personalizzate](https://docs.microsoft.com/dotnet/standard/base-types/custom-timespan-format-strings) . | No       |
| culture                          | Informazioni sulle impostazioni cultura da utilizzare quando si convertono i tipi, ad esempio `en-us` o `fr-fr` . | No       |

**Esempio:**

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "ParquetSource"
        },
        "sink": {
            "type": "SqlSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "typeConversion": true,
            "typeConversionSettings": {
                "allowDataTruncation": true,
                "treatBooleanAsNumber": true,
                "dateTimeFormat": "yyyy-MM-dd HH:mm:ss.fff",
                "dateTimeOffsetFormat": "yyyy-MM-dd HH:mm:ss.fff zzz",
                "timeSpanFormat": "dd\.hh\:mm",
                "culture": "en-gb"
            }
        }
    },
    ...
}
```

## <a name="legacy-models"></a>Modalità legacy

> [!NOTE]
> I modelli seguenti per eseguire il mapping delle colonne o dei campi di origine al sink sono ancora supportati come per la compatibilità con le versioni precedenti. Si consiglia di utilizzare il nuovo modello indicato nel [mapping dello schema](#schema-mapping). Data Factory interfaccia utente di creazione ha cambiato la generazione del nuovo modello.

### <a name="alternative-column-mapping-legacy-model"></a>Mapping di colonne alternativo (modello Legacy)

È possibile specificare l'attività di copia-> per eseguire il `translator`  ->  `columnMappings` mapping tra dati a forma di tabulazione. In questo caso, la sezione "Structure" è obbligatoria per i set di dati di input e di output. Il mapping di colonne supporta il **mapping di tutte le colonne o di un sottoinsieme delle colonne nella "struttura" del set di dati di origine a tutte le colonne della "struttura" del set di dati del sink**. Le seguenti sono condizioni di errore che generano un'eccezione:

- Il risultato della query dell'archivio dati di origine non ha un nome colonna specificato nella sezione "struttura" del set di dati di input.
- L'archivio dati sink (con schema predefinito) non ha un nome colonna specificato nella sezione "struttura" del set di dati di output.
- Un numero inferiore o superiore di colonne nella "struttura" del set di dati di sink rispetto a quanto specificato nel mapping.
- Mapping duplicato.

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

Il codice JSON seguente definisce un'attività di copia in una pipeline. Le colonne dell'origine vengono mappate alle colonne nel sink utilizzando la proprietà **Translator**  ->  **ColumnMappings** .

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

### <a name="alternative-schema-mapping-legacy-model"></a>Mapping di schemi alternativi (modello Legacy)

È possibile specificare l'attività di copia-> per eseguire il `translator`  ->  `schemaMapping` mapping tra i dati a forma di gerarchia e i dati a forma di tabulazione, ad esempio, copiare da MongoDB/Rest a file di testo e copiare da Oracle nell'API Azure Cosmos DB per MongoDB. Nella sezione `translator` dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà            | Descrizione                                                  | Obbligatoria |
| :------------------ | :----------------------------------------------------------- | :------- |
| type                | La proprietà Type del convertitore dell'attività di copia deve essere impostata su: **TabularTranslator** | Sì      |
| schemaMapping       | Raccolta di coppie chiave-valore che rappresenta la relazione di mapping **dal lato di origine al lato del sink**.<br/>- **Key:** rappresenta l'origine. Per l' **origine tabulare**, specificare il nome della colonna come definito nella struttura del set di dati. per l' **origine gerarchica**, specificare l'espressione del percorso JSON per ogni campo da estrarre e mappare.<br>- **Valore:** rappresenta il sink. Per il **sink tabulare**, specificare il nome della colonna come definito nella struttura del set di dati. per il **sink gerarchico**, specificare l'espressione del percorso JSON per ogni campo da estrarre e mappare. <br>Nel caso di dati gerarchici, per i campi sotto l'oggetto radice, il percorso JSON inizia con la radice $; per i campi all'interno della matrice scelta dalla `collectionReference` proprietà, il percorso JSON inizia dall'elemento della matrice. | Sì      |
| collectionReference | Per eseguire l'iterazione dei dati ed estrarli dagli oggetti **presenti nel campo di una matrice** con lo stesso modello e convertirli in una struttura per riga e per oggetto, specificare il percorso JSON di tale matrice per eseguire il cross apply. Questa proprietà è supportata solo quando l'origine è costituita da dati gerarchici. | No       |

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

| orderNumber | orderDate | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

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

## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri articoli relativi all'attività di copia:

- [Panoramica dell'attività di copia](copy-activity-overview.md)
