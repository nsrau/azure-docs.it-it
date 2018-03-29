---
title: Copiare dati da MongoDB usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da MongoDB in archivi dati sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: jingwang
ms.openlocfilehash: 3c1e5dbf60c247399b620a437da92a166990087e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Copiare i dati da MongoDB con Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-on-premises-mongodb-connector.md)
> * [Versione 2 - Anteprima](connector-mongodb.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da un database MongoDB. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Connettore MongoDB in V1](v1/data-factory-on-premises-mongodb-connector.md).


## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da un database MongoDB in un qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore MongoDB supporta:

- MongoDB **versioni 2.4, 2.6, 3.0 e 3.2**.
- La copia dei dati usando l'autenticazione **Di base** o **Anonima**.

## <a name="prerequisites"></a>prerequisiti

Per copiare i dati da un database MongoDB non accessibile pubblicamente, è necessario configurare un runtime di integrazione self-hosted. Per i dettagli, vedere l'articolo [Runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md). Il runtime di integrazione offre un driver per MongoDB predefinito e non è quindi necessario installare manualmente alcun driver quando si copiano dati da MongoDB.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di data factory specifiche per il connettore MongoDB.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di MongoDB sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type |La proprietà type deve essere impostata su **MongoDb** |Sì |
| server |Indirizzo IP o nome host del server MongoDB. |Sì |
| port |Porta TCP che il server MongoDB usa per ascoltare le connessioni client. |No (il valore predefinito è 27017) |
| databaseName |Nome del database MongoDB a cui si vuole accedere. |Sì |
| authenticationType | Tipo di autenticazione usato per connettersi al database MongoDB.<br/>I valori consentiti sono: **Di base** e **Anonima**. |Sì |
| username |Account utente per accedere a MongoDB. |Sì (se si usa l'autenticazione di base). |
| password |Password per l'utente. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |Sì (se si usa l'autenticazione di base). |
| authSource |Nome del database MongoDB che si vuole usare per controllare le credenziali di autenticazione. |di serie Per l'autenticazione di base il valore predefinito usa l'account di amministrazione e il database specificati usando la proprietà databaseName. |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione self-hosted o il runtime di integrazione di Azure (se l'archivio dati è accessibile pubblicamente). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No  |

**Esempio:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDb",
        "typeProperties": {
            "server": "<server name>",
            "databaseName": "<database name>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati di MongoDB.

Per copiare dati da MongoDB, impostare la proprietà type del set di dati su **MongoDbCollection**. Sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **MongoDbCollection** | Sì |
| collectionName |Nome della raccolta nel database MongoDB. |Sì |

**Esempio:**

```json
{
     "name":  "MongoDbDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }

```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine di MongoDB.

### <a name="mongodb-as-source"></a>MongoDB come origine

Per copiare dati da MongoDB, impostare il tipo di origine nell'attività di copia su **MongoDbSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **MongoDbSource** | Sì |
| query |Usare la query SQL-92 personalizzata per leggere i dati. Ad esempio: selezionare * da MyTable. |No (se "collectionName" nel set di dati è specificato) |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "MongoDbSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!TIP]
> Quando si specifica la query SQL, prestare attenzione al formato di DateTime. Ad esempio: `SELECT * FROM Account WHERE LastModifiedDate >= {{ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')}'}} AND LastModifiedDate < {{ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}'}}`

## <a name="schema-by-data-factory"></a>Schema da Data Factory

Il servizio Azure Data Factory deduce lo schema da una raccolta MongoDB usando **gli ultimi 100 documenti** nella raccolta. Se questi 100 documenti non contengono lo schema completo, alcune colonne possono essere ignorate durante l'operazione di copia.

## <a name="data-type-mapping-for-mongodb"></a>Mapping del tipo di dati per MongoDB

Quando si copiano dati da MongoDB, vengono usati i mapping seguenti tra i tipi di dati MongoDB e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati di MongoDB | Tipo di dati provvisori di Data Factory |
|:--- |:--- |
| Binary |Byte[] |
| boolean |boolean |
| Data |Datetime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |string |
| string |string |
| UUID |Guid |
| Oggetto |Rinormalizzato in colonne rese flat con "_" come separatore annidato |

> [!NOTE]
> Per informazioni sul supporto di matrici che usano tabelle virtuali, vedere la sezione [Supporto per tipi complessi con tabelle virtuali](#support-for-complex-types-using-virtual-tables).
>
> I tipi di dati MongoDB seguenti non sono attualmente supportati: DBPointer, JavaScript, chiave Max/Min, Espressione regolare, Simbolo, Timestamp, Undefined.

## <a name="support-for-complex-types-using-virtual-tables"></a>Supporto per tipi complessi con tabelle virtuali

Azure Data Factory usa un driver ODBC integrato per connettersi ai dati di un database MongoDB e copiarli. Per i tipi complessi come le matrici o gli oggetti con tipi diversi tra i documenti, il driver rinormalizza i dati nelle tabelle virtuali corrispondenti. In particolare, se una tabella contiene tali colonne, il driver genera le tabelle virtuali seguenti:

* Una **tabella di base**che contiene gli stessi dati della tabella reale eccetto le colonne di tipo complesso. La tabella di base ha lo stesso nome della tabella reale che rappresenta.
* Una **tabella virtuale** per ogni colonna di tipo complesso che espande i dati annidati. Alle tabelle virtuali vengono assegnati nomi composti dal nome della tabella reale seguito dal separatore "_" e dal nome della matrice o dell'oggetto.

Le tabelle virtuali fanno riferimento ai dati nella tabella reale, consentendo al driver di accedere ai dati denormalizzati. È possibile accedere al contenuto delle matrici MongoDB eseguendo query e join sulle tabelle virtuali.

### <a name="example"></a>Esempio

Ad esempio, ExampleTable di seguito è una tabella MongoDB che contiene una colonna con una matrice di oggetti in ogni cella, Fatture, e una colonna con una matrice di tipi scalari, Classificazioni.

| _id | Nome del cliente | Fatture | Contratto | Classificazioni |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", elemento:"tostapane", prezzo:"456", sconto:"0,2"}, {invoice_id:"124", elemento:"forno", prezzo: "1235", sconto: "0,2"}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", elemento:"frigorifero", prezzo: "12543", sconto: "0,0"}] |Gold |[1,2] |

Il driver genera più tabelle virtuali per rappresentare questa singola tabella. La prima tabella virtuale è la tabella di base denominata "ExampleTable", illustrata nell'esempio. La tabella di base contiene tutti i dati della tabella originale, ma i dati dalle matrici sono stati omessi e vengono espansi nelle tabelle virtuali.

| _id | Nome del cliente | Contratto |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Gold |

Le tabelle seguenti illustrano le tabelle virtuali che rappresentano le matrici originali nell'esempio. In queste tabelle è possibile vedere:

* Un riferimento alla colonna della chiave primaria originale corrispondente alla riga della matrice originale (con la colonna _id)
* Un'indicazione della posizione dei dati all'interno della matrice originale
* I dati espansi per ogni elemento all'interno della matrice

**Tabella "ExampleTable_Invoices":**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | price | Discount |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |toaster |456 |0,2 |
| 1111 |1 |124 |oven |1235 |0,2 |
| 2222 |0 |135 |fridge |12543 |0.0 |

**Tabella "ExampleTable_Ratings":**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).