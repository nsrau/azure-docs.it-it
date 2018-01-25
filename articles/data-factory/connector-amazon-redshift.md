---
title: Copiare dati da Amazon Redshift usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da Amazon Redshift in archivi dati di sink supportati usando Azure Data Factory.
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
ms.openlocfilehash: 1e1c8e03bbfc2a07f4d4faee4c3b171c44fa312d
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Copiare dati da Amazon Redshift usando Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-amazon-redshift-connector.md)
> * [Versione 2 - Anteprima](connector-amazon-redshift.md)


Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da Amazon Redshift. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere le informazioni sul [connettore Amazon Redshift nella versione 1](v1/data-factory-amazon-redshift-connector.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da Amazon Redshift a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore Amazon Redshift supporta il recupero di dati da Redshift mediante l'uso di query o dello strumento UNLOAD Redshift.

> [!TIP]
> Per ottenere prestazioni ottimali quando si copiano grandi quantità di dati da Redshift, è possibile usare lo strumento UNLOAD Redshift predefinito tramite Amazon S3. Per informazioni dettagliate, vedere la sezione [Usare UNLOAD per copiare i dati da Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>prerequisiti

* Se si copiano dati in un archivio dati locale usando il [runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md), concedere al runtime di integrazione (usare l'indirizzo IP del computer) l'accesso al cluster Amazon Redshift. Vedere [Autorizzare l'accesso al cluster](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) per le istruzioni.
* Se si copiano dati in un archivio dati di Azure, vedere [Azure Data Center IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (Intervalli IP del centro dati di Azure) per gli intervalli di indirizzi IP ed SQL di calcolo usati dai data center di Azure.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore Amazon Redshift.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Amazon Redshift sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **AmazonRedshift** | Sì |
| server |Indirizzo IP o nome host del server Amazon Redshift. |Sì |
| port |Il numero della porta TCP che il server Amazon Redshift usa per ascoltare le connessioni client. |No, il valore predefinito è 5439 |
| database |Nome del database Amazon Redshift. |Sì |
| Nome utente |Nome dell'utente che ha accesso al database. |Sì |
| password |La password per l'account utente. Contrassegnare questo campo come SecureString. |Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No  |

**Esempio:**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "<server name>",
            "database": "<database name>",
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati Amazon Redshift.

Per copiare dati da Amazon Redshift, impostare la proprietà type del set di dati su **RelationalTable**. Sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **RelationalTable** | Sì |
| tableName | Nome della tabella in Amazon Redshift. | No (se nell'origine dell'attività è specificato "query") |

**Esempio**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Amazon Redshift.

### <a name="amazon-redshift-as-source"></a>Amazon Redshift come origine

Per copiare dati da Amazon Redshift, impostare il tipo di origine nell'attività di copia su **AmazonRedshiftSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **AmazonRedshiftSource** | Sì |
| query |Usare la query personalizzata per leggere i dati. |Stringa di query SQL. Ad esempio: selezionare * da MyTable. |No (se nel set di dati è specificato "tableName") |
| redshiftUnloadSettings | Gruppo di proprietà quando si usa lo strumento UNLOAD di Amazon Redshift. | No  |
| s3LinkedServiceName | Fa riferimento a un'istanza di Amazon S3 da usare come archivio provvisorio specificando un nome di servizio collegato di tipo "AmazonS3". | Sì, se si usa UNLOAD |
| bucketName | Indicare il bucket S3 per archiviare i dati provvisori. Se non viene specificato, il servizio Data Factory lo genera automaticamente.  | Sì, se si usa UNLOAD |

**Esempio: origine Amazon Redshift nell'attività di copia usando UNLOAD**

```json
"source": {
    "type": "AmazonRedshiftSource",
    "query": "<SQL query>",
    "redshiftUnloadSettings": {
        "s3LinkedServiceName": {
            "referenceName": "<Amazon S3 linked service>",
            "type": "LinkedServiceReference"
        },
        "bucketName": "bucketForUnload"
    }
}
```

Per altre informazioni su come usare UNLOAD per copiare in modo efficiente dati da Amazon Redshift, vedere la sezione successiva.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Usare UNLOAD per copiare i dati da Amazon Redshift

[UNLOAD](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) è un meccanismo fornito da Amazon Redshift, che consente di scaricare i risultati di una query in uno o più file in Amazon Simple Storage Service (Amazon S3). Si tratta del metodo consigliato da Amazon per la copia di set di dati di grandi dimensioni da Redshift.

**Esempio: copiare dati da Amazon Redshift in Azure SQL Data Warehouse usando UNLOAD, la copia di staging e PolyBase**

Per questo caso d'uso di esempio, l'attività di copia scarica i dati da Amazon Redshift in Amazon S3 come configurato in "redshiftUnloadSettings", quindi copia i dati da Amazon S3 in BLOB di Azure, come specificato in "stagingSettings", e infine usa PolyBase per caricare i dati in SQL Data Warehouse. Tutto il formato provvisorio viene gestito correttamente dall'attività di copia.

![Flusso di lavoro di copia da Redshift a SQL Data Warehouse](media\copy-data-from-amazon-redshift\redshift-to-sql-dw-copy-workflow.png)

```json
"activities":[
    {
        "name": "CopyFromAmazonRedshiftToSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "AmazonRedshiftDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonRedshiftSource",
                "query": "select * from MyTable",
                "redshiftUnloadSettings": {
                    "s3LinkedServiceName": {
                        "referenceName": "AmazonS3LinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "bucketName": "bucketForUnload"
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "AzureStorageLinkedService",
                "path": "adfstagingcopydata"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

## <a name="data-type-mapping-for-amazon-redshift"></a>Mapping del tipo di dati di Amazon Redshift

Quando si copiano dati da Amazon Redshift, vengono usati i mapping seguenti tra i tipi di dati Amazon Redshift e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati di Amazon Redshift | Tipo di dati provvisori di Data Factory |
|:--- |:--- |
| BIGINT |Int64 |
| BOOLEAN |string |
| CHAR |string |
| DATE |Datetime |
| DECIMAL |Decimal |
| DOUBLE PRECISION |Double |
| INTEGER |Int32 |
| REAL |Single |
| SMALLINT |Int16 |
| TEXT |string |
| TIMESTAMP |Datetime |
| VARCHAR |string |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).
