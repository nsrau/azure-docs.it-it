---
title: Copiare dati da Cassandra usando Azure Data Factory | Microsoft Docs
description: "Informazioni su come copiare dati da Cassandra in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory."
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
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: 5b6a2cde9bea3d3aba9262bb9446d54773cf0297
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Copiare dati da Cassandra usando Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-onprem-cassandra-connector.md)
> * [Versione 2 - Anteprima](connector-cassandra.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da un database Cassandra. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.


> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere le informazioni sul [connettore Cassandra nella versione 1](v1/data-factory-onprem-cassandra-connector.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da un database Cassandra in qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore Cassandra supporta:

- Cassandra **versioni 2.X**.
- La copia dei dati usando l'autenticazione **Di base** o **Anonima**.

## <a name="prerequisites"></a>prerequisiti

Per copiare i dati da un database Cassandra non accessibile pubblicamente, è necessario configurare un runtime di integrazione self-hosted. Per i dettagli, vedere l'articolo [Runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md). Il runtime di integrazione offre un driver per Cassandra integrato e non è quindi necessario installare manualmente alcun driver quando si copiano dati da/in Cassandra.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore Cassandra.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Cassandra sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type |La proprietà type deve essere impostata su: **Cassandra** |Sì |
| host |Uno o più indirizzi IP o nomi host di server Cassandra.<br/>Specificare un elenco delimitato da virgole degli indirizzi IP o nomi host per la connessione a tutti i server contemporaneamente. |Sì |
| port |La porta TCP che il server Cassandra usa per ascoltare le connessioni client. |No (il valore predefinito è 9042) |
| authenticationType | Tipo di autenticazione usato per la connessione al database Cassandra.<br/>I valori consentiti sono: **Di base** e **Anonima**. |Sì |
| username |Specificare il nome utente per l'account utente. |Sì, se authenticationType è impostato su Basic. |
| password |Specifica la password per l'account utente. Contrassegnare questo campo come SecureString. |Sì, se authenticationType è impostato su Basic. |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione self-hosted o il runtime di integrazione di Azure (se l'archivio dati è accessibile pubblicamente). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No  |

**Esempio:**

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "Cassandra",
        "typeProperties": {
            "host": "<host>",
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati Cassandra.

Per copiare dati da Cassandra, impostare la proprietà type del set di dati su **RelationalTable**. Sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **CassandraTable** | Sì |
| keyspace |Nome del keyspace o schema nel database Cassandra. |No (se per "CassandraSource" è specificato "query") |
| tableName |Nome della tabella in un database Cassandra. |No (se per "CassandraSource" è specificato "query") |

**Esempio:**

```json
{
    "name": "CassandraDataset",
    "properties": {
        "type": "CassandraTable",
        "linkedServiceName": {
            "referenceName": "<Cassandra linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "keySpace": "<keyspace name>",
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia


Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Cassandra.

### <a name="cassandra-as-source"></a>Cassandra come origine

Per copiare dati da Cassandra, impostare il tipo di origine nell'attività di copia su **CassandraSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **CassandraSource** | Sì |
| query |Usare la query personalizzata per leggere i dati. |Query SQL-92 o query CQL. Vedere il [riferimento a CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Quando si usa una query SQL, specificare **nome keyspace.nome tabella** per indicare la tabella su cui eseguire la query. |No (se nel set di dati sono specificati "tableName" e "keyspace"). |
| consistencyLevel |Il livello di coerenza specifica quante repliche devono rispondere a una richiesta di lettura prima della restituzione dei dati all'applicazione client. Cassandra controlla il numero di repliche specificato perché i dati soddisfino la richiesta di lettura. Per informazioni dettagliate, vedere [Configuring data consistency](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) (Configurazione della coerenza dei dati).<br/><br/>I valori consentiti sono: **ONE**, **TWO**, **THREE**, **QUORUM**, **ALL**, **LOCAL_QUORUM**, **EACH_QUORUM** e **LOCAL_ONE**. |No (il valore predefinito è `ONE`) |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromCassandra",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cassandra input dataset name>",
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
                "type": "CassandraSource",
                "query": "select id, firstname, lastname from mykeyspace.mytable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-cassandra"></a>Mapping dei tipi di dati per Cassandra

Quando si copiano dati da Cassandra, vengono usati i mapping seguenti tra i tipi di dati di Cassandra e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati di Cassandra | Tipo di dati provvisori di Data Factory |
|:--- |:--- |
| ASCII |string |
| BIGINT |Int64 |
| BLOB |Byte[] |
| BOOLEAN |BOOLEAN |
| DECIMAL |Decimal |
| DOUBLE |DOUBLE |
| FLOAT |Single |
| INET |string |
| INT |Int32 |
| TEXT |string |
| TIMESTAMP |Datetime |
| TIMEUUID |Guid |
| UUID |Guid |
| VARCHAR |string |
| VARINT |DECIMAL |

> [!NOTE]
> Per i tipi di raccolta (mappa, set, elenco e così via), vedere la sezione [Uso delle raccolte con una tabella virtuale](#work-with-collections-using-virtual-table) .
>
> I tipi definiti dall'utente non sono supportati.
>
> La lunghezza di una colonna Binary o String non può essere maggiore di 4000.
>

## <a name="work-with-collections-using-virtual-table"></a>Uso delle raccolte con una tabella virtuale

Azure Data Factory usa un driver ODBC integrato per connettersi ai dati di un database Cassandra e copiarli. Per i tipi di raccolta, fra cui mappa, set ed elenco, il driver normalizza di nuovo i dati in tabelle virtuali corrispondenti. In particolare, se una tabella contiene colonne della raccolta, il driver genera le tabelle virtuali seguenti:

* Una **tabella di base**che contiene gli stessi dati della tabella reale eccetto le colonne della raccolta. La tabella di base ha lo stesso nome della tabella reale che rappresenta.
* Una **tabella virtuale** per ogni colonna della raccolta che espande i dati nidificati. Alle tabelle virtuali che rappresentano le raccolte vengono assegnati nomi composti dal nome della tabella reale seguito dal separatore "*vt*" e dal nome della colonna.

Le tabelle virtuali fanno riferimento ai dati nella tabella reale, consentendo al driver di accedere ai dati denormalizzati. Per i dettagli vedere la sezione Esempio. È possibile accedere al contenuto delle raccolte Cassandra eseguendo query e join sulle tabelle virtuali.

### <a name="example"></a>Esempio

Ad esempio, "ExampleTable" è una tabella di un database Cassandra contenente una colonna chiave primaria integer denominata "pk_int", una colonna testo denominata value, una colonna elenco, una colonna mappa e una colonna set (denominata "StringSet").

| pk_int | Valore | Elenco | Mappa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"valore di esempio 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"valore di esempio 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Il driver genera più tabelle virtuali per rappresentare questa singola tabella. Le colonne chiave esterna nelle tabelle virtuali fanno riferimento alle colonne chiave primaria nella tabella reale e indicano a quale riga della tabella reale corrisponde la riga della tabella virtuale.

La prima tabella virtuale è la tabella di base denominata "ExampleTable", illustrata nell'esempio seguente: 

| pk_int | Valore |
| --- | --- |
| 1 |"valore di esempio 1" |
| 3 |"valore di esempio 3" |

La tabella di base contiene gli stessi dati della tabella di database originale, tranne le raccolte, che vengono omesse da questa tabella ed espanse in altre tabelle virtuali.

Le tabelle seguenti illustrano le tabelle virtuali che normalizzano di nuovo i dati dalle colonne elenco, mappa e StringSet. Le colonne con nomi che terminano con "_index" o "_key" indicano la posizione dei dati all'interno dell'elenco o della mappa originale. Le colonne con nomi che terminano con "_value" contengono i dati espansi dalla raccolta.

**Tabella "ExampleTable_vt_List":**

| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

**Tabella "ExampleTable_vt_Map":**

| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |Una  |
| 1 |S2 |b |
| 3 |S1 |t |

**Tabella "ExampleTable_vt_StringSet":**

| pk_int | StringSet_value |
| --- | --- |
| 1 |Una  |
| 1 |b |
| 1 |C |
| 3 |Una  |
| 3 |E |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).