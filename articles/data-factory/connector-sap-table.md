---
title: Copiare i dati dalla tabella SAP usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati dalla tabella SAP in archivi dati sink supportati usando un'attività di copia in una pipeline di Data Factory di Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/10/2018
ms.author: jingwang
ms.openlocfilehash: 49f07b4aaadfd45e9743bde58dc715230e5bc983
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074054"
---
# <a name="copy-data-from-sap-table-using-azure-data-factory"></a>Copiare i dati dalla tabella SAP usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da una tabella di SAP. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati dalla tabella SAP a qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore SAP tabella supporta:

- Copia dei dati dalla tabella SAP in:

    - **SAP ECC** con 7.01 o versioni successive (in un recente SAP supporto pacchetto Stack rilasciato dopo il 2015)
    - **SAP BW** con 7.01 o versioni successive
    - **SAP S/4HANA**
    - **Gli altri prodotti SAP Business Suite** con 7.01 o versioni successive 

- Copia dei dati da entrambe **tabella trasparente SAP** e **visualizzazione**.
- La copia dei dati tramite **l'autenticazione di base** oppure **SNC** (proteggere le comunicazioni di rete) se è configurato SNC.
- La connessione al **Server applicazioni** oppure **Server messaggi**.

## <a name="prerequisites"></a>Prerequisiti

Per usare questo connettore tabella SAP, è necessario:

- Configurare un Runtime di integrazione Self-Hosted con versione 3.17 o versioni successive. Per i dettagli, vedere l'articolo [Runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md).

- Scaricare il **[connettore SAP .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) a 64 bit** dal sito Web di SAP e installarlo nel computer di runtime di integrazione self-hosted. Quando l'installazione, nella finestra di passaggi di configurazione facoltative, assicurarsi di selezionare il **installare gli assembly alla GAC** opzione. 

    ![Installare il connettore SAP .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Utente SAP in uso nel connettore tabella di Data Factory SAP deve avere le autorizzazioni seguenti: 

    - Autorizzazione per RFC. 
    - Autorizzazioni per l'attività "Esecuzione" dell'oggetto di autorizzazione "S_SDSAUTH".

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore tabella SAP.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di SAP Business Warehouse Open Hub sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **SapTable** | Yes |
| server | Nome del server in cui risiede l'istanza di SAP.<br/>Applicabile se si desidera connettersi **Server applicazioni SAP**. | No |
| systemNumber | Numero del sistema del sistema SAP.<br/>Applicabile se si desidera connettersi **Server applicazioni SAP**.<br/>Valore consentito: numero decimale a due cifre rappresentato come stringa. | No |
| messageServer | Il nome host del Server messaggi SAP.<br/>Applicabile se si desidera connettersi **Server messaggi SAP**. | No |
| messageServerService | Il nome del servizio o la porta numero del Server messaggi.<br/>Applicabile se si desidera connettersi **Server messaggi SAP**. | No |
| systemId | SystemID del sistema SAP in cui si trova nella tabella.<br/>Applicabile se si desidera connettersi **Server messaggi SAP**. | No |
| logonGroup | Il gruppo di accesso per il sistema SAP.<br/>Applicabile se si desidera connettersi **Server messaggi SAP**. | No |
| clientId | ID client del client nel sistema SAP.<br/>Valore consentito: numero decimale a tre cifre rappresentato come stringa. | Sì |
| language | Lingua usata dal sistema SAP. | No (valore predefinito: **EN**)|
| userName | Nome dell'utente che ha accesso al server SAP. | Sì |
| password | Password per l'utente. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| sncMode | Indicatore di attivazione SNC per accedere al server SAP in cui si trova nella tabella.<br/>Applicabile se si desidera usare SNC per connettersi al server SAP.<br/>I valori consentiti sono i seguenti: **0** (disattivata, predefinita) o **1** (attivato). | No |
| sncMyName | Nome SNC dell'Initiator per accedere al server SAP in cui si trova nella tabella.<br/>Si applica quando `sncMode` si trova in. | No |
| sncPartnerName | Nome di un partner di comunicazione SNC per accedere al server SAP in cui si trova nella tabella.<br/>Si applica quando `sncMode` si trova in. | No |
| sncLibraryPath | Libreria del prodotto di protezione esterne per accedere al server SAP in cui si trova nella tabella.<br/>Si applica quando `sncMode` si trova in. | No |
| sncQop | Qualità SNC della protezione.<br/>Si applica quando `sncMode` si trova in. <br/>I valori consentiti sono i seguenti: **1** (autenticazione), **2** (integrità), **3** (riservatezza), **8** (predefinito), **9** (massimo). | No |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È necessario un runtime di integrazione self-hosted come indicato in [Prerequisiti](#prerequisites). |Yes |

**Esempio 1: connessione al Server applicazioni SAP**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio 2: connessione al Server messaggi SAP**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system id>",
            "logonGroup": "<logon group>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio 3: connessione tramite SNC**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "snc myname",
            "sncPartnerName": "snc partner name",
            "sncLibraryPath": "snc library path",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati tabella SAP.

Per copiare dati da e verso Hub aperto di SAP BW, vengono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **SapTableResource**. | Yes |
| tableName | Il nome della tabella da copiare dati da SAP. | Yes |

**Esempio:**

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "linkedServiceName": {
            "referenceName": "<SAP Table linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<SAP table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine tabella SAP.

### <a name="sap-table-as-source"></a>Tabella SAP come origine

Per copiare dati dalla tabella SAP, sono supportate le proprietà seguenti.

| Proprietà                         | Descrizione                                                  | Obbligatoria |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| type                             | La proprietà type deve essere impostata su **SapTableSource**.       | Yes      |
| rowCount                         | Numero di righe da recuperare.                              | No       |
| rfcTableFields                   | Campi da copiare dalla tabella SAP. Ad esempio: `column0, column1`. | No       |
| rfcTableOptions                  | Opzioni per filtrare le righe nella tabella SAP. Ad esempio: `COLUMN0 EQ 'SOMEVALUE'`. Vedere altre descrizione sotto questa tabella. | No       |
| customRfcReadTableFunctionModule | RFC funzione modulo personalizzato che può essere usato per leggere dati dalla tabella SAP. | No       |
| partitionOption                  | Il meccanismo di partizione per la lettura dalla tabella SAP. Le opzioni supportate includono: <br/>- **None**<br/>- **PartitionOnInt** (integer normale o valori integer con zero la spaziatura interna a sinistra, ad esempio 0000012345)<br/>- **PartitionOnCalendarYear** (4 cifre nel formato "YYYY")<br/>- **PartitionOnCalendarMonth** (6 cifre nel formato "YYYYMM")<br/>- **PartitionOnCalendarDate** (8 cifre nel formato "Aaaammgg") | No       |
| partitionColumnName              | Il nome della colonna per partizionare i dati. | No       |
| partitionUpperBound              | Il valore massimo della colonna specificata `partitionColumnName` che verrà usato per il partizionamento di procedere. | No       |
| partitionLowerBound              | Il valore minimo della colonna specificata `partitionColumnName` che verrà usato per il partizionamento di procedere. | No       |
| maxPartitionsNumber              | Il numero massimo di partizioni per dividere i dati. | No       |

>[!TIP]
>- Se la tabella SAP ha una grande quantità di dati, ad esempio diversi miliardi di righe, usare `partitionOption` e `partitionSetting` per suddividere i dati in partizioni piccole, nel qual caso i dati vengono letti da partizioni e ogni partizione di dati viene recuperato dal server SAP tramite un'unica Chiamata RFC.<br/>
>- Prendendo `partitionOption` come `partitionOnInt` ad esempio, viene calcolato il numero di righe in ogni partizione (totale righe che rientrano tra *partitionUpperBound* e *partitionLowerBound*) /*maxPartitionsNumber*.<br/>
>- Se si desidera eseguire ulteriormente le partizioni in parallelo per velocizzare la copia, è consigliabile apportare `maxPartitionsNumber` a un multiplo del valore di `parallelCopies` (altre informazioni, vedere [copia parallela](copy-activity-performance.md#parallel-copy)).

In `rfcTableOptions`, è possibile utilizzare ad esempio, i seguente comuni SAP gli operatori di query per filtrare le righe: 

| Operator | Descrizione |
| :------- | :------- |
| EQ | Uguale a |
| NE | Diverso da |
| LT | Minore di |
| LE | Minore o uguale a |
| GT | Maggiore di |
| GE | Maggiore o uguale a |
| AD ESEMPIO | Come in LIKE '% Emma' |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP Table input dataset name>",
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
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-table"></a>Mapping dei tipi di dati per la tabella di SAP

Quando si copiano dati da SAP tabella, vengono usati i mapping seguenti dai tipi di dati tabella SAP per tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo SAP ABAP | Tipo di dati provvisori di Data Factory |
|:--- |:--- |
| C (String) | String |
| I (Integer) | Int32 |
| F (Float) | Double |
| D (Date) | string |
| T (Time) | string |
| P (BCD Packed, Currency, Decimal, Qty) | Decimal |
| N (numerico) | String |
| X (Binary e Raw) | string |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
