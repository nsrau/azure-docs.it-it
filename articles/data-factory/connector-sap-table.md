---
title: Copiare dati da una tabella SAP
description: Informazioni su come copiare i dati da una tabella SAP agli archivi dati sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.Learn how to copy data from an SAP table to supported sink data stores by using a copy activity in an Azure Data Factory pipeline.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: d96b2b1f8465132549c59ac5555adf99e7758a3b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415229"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Copiare dati da una tabella SAP usando Azure Data FactoryCopy data from an SAP table by using Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da una tabella SAP. Per ulteriori informazioni, vedere [Panoramica dell'attività di copia](copy-activity-overview.md).

>[!TIP]
>Per informazioni sul supporto generale di ADF sullo scenario di integrazione dei dati SAP, vedere il [white paper sull'integrazione](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) dei dati SAP con introduzione, comparsa e indicazioni dettagliate.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore tabella SAP è supportato per le attività seguenti:This SAP table connector is supported for the following activities:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)

È possibile copiare i dati da una tabella SAP a qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella [Archivi dati supportati.](copy-activity-overview.md#supported-data-stores-and-formats)

In particolare, questo connettore tabella SAP supporta:Specifically, this SAP table connector supports:

- Copia dei dati da una tabella SAP in:

  - SAP ERP Central Component (SAP ECC) versione 7.01 o successiva (in un recente stack di pacchetti di supporto SAP rilasciato dopo il 2015).
  - SAP Business Warehouse (SAP BW) versione 7.01 o successiva (in un recente stack di pacchetti di supporto SAP rilasciato dopo il 2015).
  - SAP S/4HANA.
  - Altri prodotti in SAP Business Suite versione 7.01 o successiva (in un recente stack di pacchetti di supporto SAP rilasciato dopo il 2015).

- Copia dei dati sia da una tabella trasparente SAP, da una tabella in pool, da una tabella cluster e da una vista.
- Copia dei dati utilizzando l'autenticazione di base o Secure Network Communications (SNC), se SNC è configurato.
- Connessione a un server applicazioni SAP o a un server messaggi SAP.

## <a name="prerequisites"></a>Prerequisiti

Per utilizzare questo connettore tabella SAP, è necessario:To use this SAP table connector, you need to:

- Configurare un runtime di integrazione self-hosted (versione 3.17 o successiva). Per ulteriori informazioni, consultate Creare e configurare un runtime di [integrazione self-hosted](create-self-hosted-integration-runtime.md).

- Scaricare il connettore SAP a 64 bit [per Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) dal sito Web SAP e installarlo nel computer runtime di integrazione self-hosted. Durante l'installazione, assicurarsi di selezionare l'opzione **Installa assembly nella Global** Assembly nella Global Assembly Cache nella finestra Passaggi di installazione **facoltativi.**

  ![Installare SAP Connector per .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- L'utente SAP utilizzato nel connettore di tabella SAP di Data Factory deve disporre delle autorizzazioni seguenti:

  - Autorizzazione per l'utilizzo di destinazioni RFC (Remote Function Call).
  - Autorizzazioni per l'attività Esegui dell'oggetto autorizzazione S_SDSAUTH.

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Nelle sezioni seguenti vengono fornite informazioni dettagliate sulle proprietà utilizzate per definire le entità di Data Factory specifiche del connettore di tabella SAP.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Le seguenti proprietà sono supportate per il servizio collegato SAP BW Open Hub:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| `type` | La proprietà `type` deve essere impostata su `SapTable`. | Sì |
| `server` | Nome del server in cui si trova l'istanza SAP.<br/>Utilizzare per connettersi a un server applicazioni SAP. | No |
| `systemNumber` | Numero di sistema del sistema SAP.<br/>Utilizzare per connettersi a un server applicazioni SAP.<br/>Valore consentito: un numero decimale a due cifre rappresentato come stringa. | No |
| `messageServer` | Nome host del server dei messaggi SAP.<br/>Utilizzare per connettersi a un server dei messaggi SAP. | No |
| `messageServerService` | Il nome del servizio o il numero di porta del server dei messaggi.<br/>Utilizzare per connettersi a un server dei messaggi SAP. | No |
| `systemId` | ID del sistema SAP in cui si trova la tabella.<br/>Utilizzare per connettersi a un server dei messaggi SAP. | No |
| `logonGroup` | Gruppo di accesso per il sistema SAP.<br/>Utilizzare per connettersi a un server dei messaggi SAP. | No |
| `clientId` | ID del client nel sistema SAP.<br/>Valore consentito: un numero decimale a tre cifre rappresentato come stringa. | Sì |
| `language` | Lingua utilizzata dal sistema SAP.<br/>Il valore predefinito è `EN`.| No |
| `userName` | Nome dell'utente che ha accesso al server SAP. | Sì |
| `password` | La password dell'utente. Contrassegnare questo `SecureString` campo con il tipo per archiviarlo in modo sicuro in Data Factory oppure fare riferimento a [un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| `sncMode` | L'indicatore di attivazione SNC per accedere al server SAP in cui si trova la tabella.<br/>Utilizzare questa opzione se si desidera utilizzare SNC per connettersi al server SAP.<br/>I valori `0` consentiti sono (disattivati, predefiniti) o `1` (on). | No |
| `sncMyName` | Nome SNC dell'infinitore per accedere al server SAP in cui si trova la tabella.<br/>Si `sncMode` applica quando è attivata. | No |
| `sncPartnerName` | Nome SNC del partner di comunicazione per accedere al server SAP in cui si trova la tabella.<br/>Si `sncMode` applica quando è attivata. | No |
| `sncLibraryPath` | Libreria del prodotto di sicurezza esterna per accedere al server SAP in cui si trova la tabella.<br/>Si `sncMode` applica quando è attivata. | No |
| `sncQop` | Livello di qualità della protezione SNC da applicare.<br/>Si `sncMode` applica quando è attivato. <br/>I valori `1` consentiti `2` sono `3` (Autenticazione), (Integrità), (Privacy), `8` (Predefinito), `9` (Massimo). | No |
| `connectVia` | Runtime [di integrazione](concepts-integration-runtime.md) da utilizzare per la connessione all'archivio dati. È necessario un runtime di integrazione self-hosted, come indicato in precedenza in [Prerequisiti](#prerequisites). |Sì |

**Esempio 1: Connettersi a un server applicazioni SAPExample 1: Connect to an SAP application server**

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

### <a name="example-2-connect-to-an-sap-message-server"></a>Esempio 2: Connettersi a un server dei messaggi SAPExample 2: Connect to an SAP message server

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

### <a name="example-3-connect-by-using-snc"></a>Esempio 3: Connessione tramite SNCExample 3: Connect by using SNC

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

Per un elenco completo delle sezioni e delle proprietà per la definizione dei dataset, vedere [Dataset](concepts-datasets-linked-services.md). Nella sezione seguente viene fornito un elenco delle proprietà supportate dal set di dati della tabella SAP.

Per copiare i dati da e nel servizio collegato SAP BW Open Hub, sono supportate le seguenti proprietà:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| `type` | La proprietà `type` deve essere impostata su `SapTableResource`. | Sì |
| `tableName` | Nome della tabella SAP da cui copiare i dati. | Sì |

### <a name="example"></a>Esempio

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà per la definizione delle attività, vedere [Pipelines](concepts-pipelines-activities.md). Nella sezione seguente viene fornito un elenco delle proprietà supportate dall'origine della tabella SAP.

### <a name="sap-table-as-source"></a>Tabella SAP come origine

Per copiare dati da una tabella SAP, sono supportate le proprietà seguenti:To copy data from an SAP table, the following properties are supported:

| Proprietà                         | Descrizione                                                  | Obbligatoria |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | La proprietà `type` deve essere impostata su `SapTableSource`.         | Sì      |
| `rowCount`                         | Numero di righe da recuperare.                              | No       |
| `rfcTableFields`                   | I campi (colonne) da copiare dalla tabella SAP. Ad esempio: `column0, column1`. | No       |
| `rfcTableOptions`                  | Opzioni per filtrare le righe in una tabella SAP. Ad esempio: `COLUMN0 EQ 'SOMEVALUE'`. Vedere anche la tabella dell'operatore di query SAP più avanti in questo articolo. | No       |
| `customRfcReadTableFunctionModule` | Modulo funzione RFC personalizzato che può essere utilizzato per leggere i dati da una tabella SAP.<br>È possibile utilizzare un modulo funzione RFC personalizzato per definire la modalità di recupero dei dati dal sistema SAP e restituiti a Data Factory.You can use a custom RFC function module to define how the data is retrieved from your SAP system and returned to Data Factory. Il modulo funzione personalizzato deve avere un'interfaccia implementata (importazione, esportazione, tabelle) simile a `/SAPDS/RFC_READ_TABLE2`, che è l'interfaccia predefinita utilizzata da Data Factory. | No       |
| `partitionOption`                  | Meccanismo di partizione per la lettura da una tabella SAP. Le opzioni supportate includono: <ul><li>`None`</li><li>`PartitionOnInt`(valori interi o interi normali con `0000012345`spaziatura interna zero a sinistra, ad esempio )</li><li>`PartitionOnCalendarYear`(4 cifre nel formato "AAAA")</li><li>`PartitionOnCalendarMonth`(6 cifre nel formato "AAAAMM")</li><li>`PartitionOnCalendarDate`(8 cifre nel formato "AAAAMMGG")</li></ul> | No       |
| `partitionColumnName`              | Nome della colonna utilizzata per partizionare i dati.                | No       |
| `partitionUpperBound`              | Valore massimo della colonna `partitionColumnName` specificata in che verrà utilizzata per continuare con il partizionamento. | No       |
| `partitionLowerBound`              | Valore minimo della colonna `partitionColumnName` specificata in che verrà utilizzato per continuare con il partizionamento. (Nota: `partitionLowerBound` non può essere "0" quando l'opzione di partizione è `PartitionOnInt`) | No       |
| `maxPartitionsNumber`              | Numero massimo di partizioni in cui dividere i dati.     | No       |

>[!TIP]
>Se la tabella SAP include un volume elevato di `partitionOption` dati, ad esempio diversi miliardi di righe, utilizzare e `partitionSetting` suddividere i dati in partizioni più piccole. In questo caso, i dati vengono letti per partizione e ogni partizione di dati viene recuperata dal server SAP tramite una singola chiamata RFC.<br/>
<br/>
>Prendendo `partitionOption` `partitionOnInt` come esempio, il numero di righe in ogni partizione viene `partitionUpperBound` calcolato `partitionLowerBound`con`maxPartitionsNumber`questa formula: (righe totali che rientrano tra e )/ .<br/>
<br/>
>Per caricare le partizioni di dati in parallelo per [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) velocizzare la copia, il grado parallelo è controllato dall'impostazione dell'attività di copia. Ad esempio, se `parallelCopies` si imposta su quattro, Data Factory genera ed esegue contemporaneamente quattro query in base all'opzione e alle impostazioni di partizione specificate e ogni query recupera una parte di dati dalla tabella SAP. È consigliabile `maxPartitionsNumber` creare un multiplo `parallelCopies` del valore della proprietà. Quando si copiano dati nell'archivio dati basato su file, è anche necessario scrivere in una cartella come più file (specificare solo il nome della cartella), nel qual caso le prestazioni sono migliori rispetto alla scrittura in un singolo file.

In `rfcTableOptions`, è possibile utilizzare i seguenti operatori di query SAP comuni per filtrare le righe:

| Operatore | Descrizione |
| :------- | :------- |
| `EQ` | Uguale a |
| `NE` | Diverso da |
| `LT` | Minore di |
| `LE` | Minore o uguale a |
| `GT` | Maggiore di |
| `GE` | Maggiore o uguale a |
| `IN` | Come in`TABCLASS IN ('TRANSP', 'INTTAB')` |
| `LIKE` | Come in`LIKE 'Emma%'` |

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
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>Mapping dei tipi di dati per una tabella SAPData type mappings for an SAP table

Quando si copiano dati da una tabella SAP, i mapping seguenti vengono usati dai tipi di dati della tabella SAP ai tipi di dati provvisori di Azure Data Factory.When you're copying data from an SAP table, the following mappings are used from the SAP table data types to the Azure Data Factory interim data types. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo SAP ABAP | Tipo di dati provvisorio di Data Factory |
|:--- |:--- |
| `C`(Stringa) | `String` |
| `I`(Numero intero) | `Int32` |
| `F`(Float) | `Double` |
| `D`(Data) | `String` |
| `T`(Tempo) | `String` |
| `P`(BCD Packed, Valuta, Decimale, Qtà) | `Decimal` |
| `N`(Numerico) | `String` |
| `X`(binario e non elaborato) | `String` |

## <a name="lookup-activity-properties"></a>Proprietà dell'attività di ricerca

Per informazioni dettagliate sulle proprietà, selezionare [Attività di ricerca](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere [Archivi dati supportati.](copy-activity-overview.md#supported-data-stores-and-formats)
