---
title: Copiare dati da e in Oracle usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da archivi di origine supportati in un database Oracle o da Oracle in archivi di sink supportati usando Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: 64f348880bf8872c61a1d1c90930c25ce9551bbf
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2019
ms.locfileid: "55658030"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Copiare dati da e in Oracle usando Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1](v1/data-factory-onprem-oracle-connector.md)
> * [Versione corrente](connector-oracle.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in un database Oracle. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta informazioni generali sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da un database Oracle in qualsiasi archivio dati di sink supportato. È anche possibile copiare dati da qualsiasi archivio di dati di origine supportato in un database Oracle. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, questo connettore Oracle supporta le versioni seguenti di un database Oracle. Sono anche supportate le autenticazioni di base o OID:

- Oracle 12c R1 (12.1)
- Oracle 11g R1, R2 (11.1, 11.2)
- Oracle 10g R1, R2 (10.1, 10.2)
- Oracle 9i R1, R2 (9.0.1, 9.2)
- Oracle 8i R3 (8.1.7)

> [!Note]
> Il server proxy Oracle non è supportato.

## <a name="prerequisites"></a>Prerequisiti

Per copiare dati da e in un database Oracle non accessibile pubblicamente, è necessario configurare un runtime di integrazione self-hosted. Per altre informazioni sul runtime di integrazione, vedere [Runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md). Il runtime di integrazione fornisce un driver Oracle incorporato. Non è pertanto necessario installare manualmente un driver quando si copiano dati da e in Oracle.

## <a name="get-started"></a>Attività iniziali

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà usate per definire entità di Data Factory specifiche per il connettore Oracle.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Oracle sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **Oracle**. | Sì |
| connectionString | Specifica le informazioni necessarie per la connessione all'istanza del database Oracle. <br/>Contrassegnare questo campo come SecureString per archiviare la chiave in modo sicuro in Data Factory. È anche possibile inserire la password in Azure Key Vault ed eseguire lo spostamento forzato dei dati della configurazione `password` all'esterno della stringa di connessione. Vedere gli esempi seguenti e l'articolo [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) per altri dettagli. <br><br>**Tipo di connessione supportato**: per identificare il database, è possibile usare il **SID Oracle** o il **nome del servizio Oracle**:<br>- Se si usa il SID: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- Se si usa il nome del servizio: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione self-hosted o il runtime di integrazione di Azure (se l'archivio dati è accessibile pubblicamente). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No  |

>[!TIP]
>Se si verifica l'errore "ORA 01025: UPI parameter out of range" (parametro UPI esterno all'intervallo) e la versione Oracle è la 8i, aggiungere `WireProtocolMode=1` alla stringa di connessione e riprovare.

**Per abilitare la crittografia sulla connessione Oracle**, sono disponibili due opzioni:

1.  Per usare To use **Triple-DES Encryption (3DES) e Advanced Encryption Standard (AES)** sul lato server Oracle, passare a Oracle Advanced Security (OAS) e configurare le impostazioni di crittografia. Per informazioni dettagliate, vedere [qui](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Connettore Azure Data factory Oracle negozia automaticamente il metodo di crittografia per usare uno dei due configurati in OAS per stabilire la connessione a Oracle.

2.  Per usare **SSL**, seguire questa procedura:

    1.  Ottenere informazioni sul certificato SSL. Ottenere le informazioni sul certificato con codifica DER del certificato SSL e salvare l'output (----- Begin Certificate … End Certificate -----) come file di testo.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Esempio:** estrarre le informazioni del certificato da DERcert.cer e quindi salvare l'output in cert.txt

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  Creare il file KeyStore o TrustStore. Il comando seguente crea il file TrustStore con o senza una password in formato PKCS-12.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Esempio:** crea un file TrustStore PKCS12 denominato MyTrustStoreFile con una password

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Posizionare il file TrustStore nel computer del runtime di integrazione self-hosted, ad esempio in C:\MyTrustStoreFile.
    4.  In Azure Data Factory, configurare la stringa di connessione Oracle con `EncryptionMethod=1` e il valore `TrustStore`/`TrustStorePassword`corrispondente, ad esempio `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Esempio:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio: archiviare la password in Azure Key Vault**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```
## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati Oracle.

Per copiare dati da e in Oracle, impostare la proprietà type del set di dati su **OracleTable**. Sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su **OracleTable**. | Sì |
| tableName |Nome della tabella nel database Oracle a cui fa riferimento il servizio collegato. | Sì |

**Esempio:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink Oracle.

### <a name="oracle-as-a-source-type"></a>Oracle come tipo di origine

Per copiare dati da Oracle, impostare il tipo di origine nell'attività di copia su **OracleSource**. Nella sezione **source** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **OracleSource**. | Sì |
| oracleReaderQuery | Usare la query SQL personalizzata per leggere i dati. Un esempio è `"SELECT * FROM MyTable"`. | No  |

Se non si specifica "oracleReaderQuery", le colonne definite nella sezione "structure" del set di dati vengono usate per creare una query (`select column1, column2 from mytable`) da eseguire nel database Oracle. Se la definizione del set di dati non include "structure", vengono selezionate tutte le colonne della tabella.

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-a-sink-type"></a>Oracle come tipo di sink

Per copiare dati in Oracle, impostare il tipo di sink nell'attività di copia su **OracleSink**. Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su **OracleSink**. | Sì |
| writeBatchSize | Inserisce dati nella tabella SQL quando la dimensione del buffer raggiunge writeBatchSize.<br/>I valori consentiti sono integer (numero di righe). |No (il valore predefinito è 10.000) |
| writeBatchTimeout | Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout.<br/>I valori consentiti sono un intervallo di tempo. Ad esempio "00:30:00" (30 minuti). | No  |
| preCopyScript | Specificare una query SQL per l'attività di copia da eseguire prima di scrivere i dati in Oracle a ogni esecuzione. È possibile usare questa proprietà per pulire i dati precaricati. | No  |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-oracle"></a>Mapping dei tipi di dati per Oracle

Quando si copiano dati da e in Oracle, vengono usati i mapping seguenti tra i tipi di dati di Oracle e i tipi di dati provvisori di Data Factory. Per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink, vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md).

| Tipo di dati Oracle | Tipo di dati provvisorio di Data Factory |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(supportato solo in Oracle 10g e versioni successive) |
| CHAR |string |
| CLOB |string |
| DATE |DateTime |
| FLOAT |Decimal, String (se la precisione > 28) |
| INTEGER |Decimal, String (se la precisione > 28) |
| LONG |string |
| LONG RAW |Byte[] |
| NCHAR |string |
| NCLOB |string |
| NUMBER |Decimal, String (se la precisione > 28) |
| NVARCHAR2 |string |
| RAW |Byte[] |
| ROWID |string |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |string |
| TIMESTAMP WITH TIME ZONE |string |
| UNSIGNED INTEGER |NUMBER |
| VARCHAR2 |string |
| XML |string |

> [!NOTE]
> I tipi di dati INTERVAL YEAR TO MONTH e INTERVAL DAY TO SECOND non sono supportati.


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).
