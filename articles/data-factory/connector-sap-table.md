---
title: Copiare dati da una tabella SAP usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da una tabella SAP in archivi dati sink supportati usando un'attività di copia in una pipeline di Data Factory di Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: jingwang
ms.openlocfilehash: 9216f5c00cbdac273b562736abdd1c812d172237
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827764"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Copiare dati da una tabella SAP usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da una tabella SAP. Per altre informazioni, vedere [panoramica dell'attività di copia](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da una tabella SAP a qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats) tabella.

In particolare, il connettore tabella SAP supporta:

- Copia dei dati da una tabella SAP in:

  - SAP ERP componente centrale (SAP ECC) 7.01 o versione successiva (in un recente SAP supporto pacchetto Stack rilasciato dopo il 2015).
  - SAP Business Warehouse (BW SAP) 7.01 o versione successiva.
  - SAP S/4HANA.
  - Gli altri prodotti SAP Business Suite 7.01 o versione successiva.

- Copia dei dati da tabelle trasparenti SAP, una tabella in pool, una tabella cluster e da una vista.
- Copia dei dati usando l'autenticazione di base o Secure Network Communications (SNC), se è configurato SNC.
- La connessione a un server applicazioni SAP o il server messaggi SAP.

## <a name="prerequisites"></a>Prerequisiti

Per usare questo connettore tabella SAP, è necessario:

- Configurare un runtime di integrazione self-hosted (3.17 o versione successiva). Per altre informazioni, vedere [creare e configurare un runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md).

- Scaricare il 64-bit [SAP Connector per Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) dal sito Web di SAP e installarlo nel computer del runtime di integrazione self-hosted. Durante l'installazione, assicurarsi di selezionare il **installare gli assembly alla GAC** opzione il **procedure di configurazione facoltative** finestra.

  ![Installare SAP Connector per .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- L'utente SAP che viene utilizzato in Data Factory SAP tabella connector deve disporre delle autorizzazioni seguenti:

  - Autorizzazione per l'uso di destinazioni di chiamata di funzione remota (RFC).
  - Autorizzazioni per l'attività di esecuzione dell'oggetto autorizzazione S_SDSAUTH.

## <a name="get-started"></a>Attività iniziali

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore tabella SAP.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio SAP BW Open Hub collegati sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| `type` | Il `type` proprietà deve essere impostata su `SapTable`. | Sì |
| `server` | Il nome del server in cui si trova l'istanza di SAP.<br/>Usare per connettersi a un server applicazioni SAP. | No |
| `systemNumber` | Il numero del sistema SAP.<br/>Usare per connettersi a un server applicazioni SAP.<br/>Valore consentito: Numero decimale a due cifre rappresentato come stringa. | No |
| `messageServer` | Il nome host del server messaggi SAP.<br/>Usare per connettersi a un server messaggi SAP. | No |
| `messageServerService` | Il nome del servizio o la porta numero del server messaggi.<br/>Usare per connettersi a un server messaggi SAP. | No |
| `systemId` | L'ID del sistema SAP in cui si trova nella tabella.<br/>Usare per connettersi a un server messaggi SAP. | No |
| `logonGroup` | Il gruppo di accesso per il sistema SAP.<br/>Usare per connettersi a un server messaggi SAP. | No |
| `clientId` | L'ID del client nel sistema SAP.<br/>Valore consentito: Un numero decimale a tre cifre rappresentato come stringa. | Sì |
| `language` | La lingua utilizzata dal sistema SAP.<br/>Il valore predefinito è `EN`.| No |
| `userName` | Il nome dell'utente che ha accesso al server SAP. | Sì |
| `password` | Password dell'utente. Contrassegnare questo campo con il `SecureString` tipo per archiviarlo in modo sicuro in Data Factory, oppure [fanno riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| `sncMode` | L'indicatore di attivazione SNC per accedere al server SAP in cui si trova nella tabella.<br/>Usare se si desidera usare SNC per connettersi al server SAP.<br/>I valori consentiti sono `0` (off, il valore predefinito) o `1` (attivato). | No |
| `sncMyName` | Nome SNC dell'initiator per accedere al server SAP in cui si trova nella tabella.<br/>Si applica quando `sncMode` si trova in. | No |
| `sncPartnerName` | Nome SNC del partner di comunicazione per accedere al server SAP in cui si trova nella tabella.<br/>Si applica quando `sncMode` si trova in. | No |
| `sncLibraryPath` | Libreria del prodotto protezione esterne per accedere al server SAP in cui si trova nella tabella.<br/>Si applica quando `sncMode` si trova in. | No |
| `sncQop` | Il livello di qualità di protezione SNC da applicare.<br/>Si applica quando `sncMode` si trova in. <br/>I valori consentiti sono `1` (autenticazione), `2` (integrità), `3` (riservatezza), `8` (predefinito), `9` (massimo). | No |
| `connectVia` | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Un runtime di integrazione self-hosted è obbligatorio, come indicato in precedenza nella [prerequisiti](#prerequisites). |Yes |

**Esempio 1: Connettersi a un server applicazioni SAP**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
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

### <a name="example-2-connect-to-an-sap-message-server"></a>Esempio 2 Connettersi a un server messaggi SAP

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>Esempio 3: Connettersi tramite SNC

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà per la definizione di set di dati, vedere [set di dati](concepts-datasets-linked-services.md). La sezione seguente fornisce un elenco delle proprietà supportate dal set di dati tabella SAP.

Per copiare dati da e verso il servizio SAP BW Open Hub collegato, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| `type` | Il `type` proprietà deve essere impostata su `SapTableResource`. | Sì |
| `tableName` | Il nome della tabella da copiare dati da SAP. | Sì |

### <a name="example"></a>Esempio

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<SAP table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà per la definizione di attività, vedere [pipeline](concepts-pipelines-activities.md). La sezione seguente fornisce un elenco delle proprietà supportate dall'origine della tabella SAP.

### <a name="sap-table-as-a-source"></a>Tabella SAP come origine

Per copiare dati da tabelle SAP, sono supportate le proprietà seguenti:

| Proprietà                         | Descrizione                                                  | Obbligatoria |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | Il `type` proprietà deve essere impostata su `SapTableSource`.         | Yes      |
| `rowCount`                         | Il numero di righe da recuperare.                              | No       |
| `rfcTableFields`                   | I campi (colonne) da copiare dalla tabella SAP. Ad esempio `column0, column1`. | No       |
| `rfcTableOptions`                  | Le opzioni per filtrare le righe in una tabella di SAP. Ad esempio `COLUMN0 EQ 'SOMEVALUE'`. Vedere anche la tabella di operatore query SAP più avanti in questo articolo. | No       |
| `customRfcReadTableFunctionModule` | RFC funzione modulo personalizzato che può essere usato per leggere dati da una tabella SAP.<br>È possibile usare un modulo di funzione RFC personalizzato per definire come i dati vengono recuperati dal sistema SAP e restituiti al servizio Data Factory. Il modulo di funzione personalizzata deve avere un'interfaccia implementata (importazione, esportazione, le tabelle) che è simile a `/SAPDS/RFC_READ_TABLE2`, ovvero l'interfaccia predefinita usata da Data Factory. | No       |
| `partitionOption`                  | Il meccanismo di partizione per la lettura da una tabella SAP. Le opzioni supportate includono: <ul><li>`None`</li><li>`PartitionOnInt` (integer normale o valori integer con zero spaziatura interna a sinistra, ad esempio `0000012345`)</li><li>`PartitionOnCalendarYear` (4 cifre nel formato "YYYY")</li><li>`PartitionOnCalendarMonth` (6 cifre nel formato "YYYYMM")</li><li>`PartitionOnCalendarDate` (8 cifre nel formato "Aaaammgg")</li></ul> | No       |
| `partitionColumnName`              | Il nome della colonna utilizzata per partizionare i dati.                | No       |
| `partitionUpperBound`              | Il valore massimo della colonna specificata `partitionColumnName` che verrà usato per continuare con il partizionamento. | No       |
| `partitionLowerBound`              | Il valore minimo della colonna specificata `partitionColumnName` che verrà usato per continuare con il partizionamento. | No       |
| `maxPartitionsNumber`              | Il numero massimo di partizioni per dividere i dati.     | No       |

>[!TIP]
>Se la tabella SAP ha un volume elevato di dati, ad esempio diverse miliardi righe, usare `partitionOption` e `partitionSetting` per suddividere i dati in partizioni più piccole. In questo caso, i dati vengono letti per ogni partizione e ogni partizione di dati viene recuperato dal server SAP tramite una singola chiamata RFC.<br/>
<br/>
>Prendendo `partitionOption` come `partitionOnInt` ad esempio, il numero di righe in ogni partizione viene calcolato con questa formula: (totale righe che rientrano tra `partitionUpperBound` e `partitionLowerBound`) /`maxPartitionsNumber`.<br/>
<br/>
>Per eseguire partizioni in parallelo per velocizzare la copia, è consigliabile rendere `maxPartitionsNumber` un multiplo del valore della `parallelCopies` proprietà. Per altre informazioni, vedere [copia parallela](copy-activity-performance.md#parallel-copy).

In `rfcTableOptions`, è possibile utilizzare i seguenti operatori di query comuni di SAP per filtrare le righe:

| Operator | Descrizione |
| :------- | :------- |
| `EQ` | Uguale a |
| `NE` | Diverso da |
| `LT` | Minore di |
| `LE` | Minore o uguale a |
| `GT` | Maggiore di |
| `GE` | Maggiore o uguale a |
| `LIKE` | Come in `LIKE 'Emma%'` |

### <a name="example"></a>Esempio

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
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

## <a name="data-type-mappings-for-an-sap-table"></a>Mapping dei tipi di dati per tabelle SAP

Quando si copiano dati da una tabella SAP, vengono usati i mapping seguenti dai tipi di dati tabella SAP per i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo SAP ABAP | Tipo di dati provvisorio di Data Factory |
|:--- |:--- |
| `C` (String) | `String` |
| `I` (Integer) | `Int32` |
| `F` (Float) | `Double` |
| `D` (Date) | `String` |
| `T` (Ora) | `String` |
| `P` (BCD compressi, valuta, Decimal, Qty) | `Decimal` |
| `N` (Numerico) | `String` |
| `X` (Binario e non elaborato) | `String` |

## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
