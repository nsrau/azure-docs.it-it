---
title: Copiare dati da una tabella SAP usando Azure Data Factory
description: Informazioni su come copiare dati da una tabella SAP in archivi dati di sink supportati usando un'attività di copia in una pipeline Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 9c4e22e997b4ad8c36d8aaf84d1bb8aacb5c5529
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680232"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Copiare dati da una tabella SAP usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da una tabella SAP. Per altre informazioni, vedere [Cenni preliminari sull'attività di copia](copy-activity-overview.md).

>[!TIP]
>Per informazioni sul supporto generale di ADF sullo scenario di integrazione dei dati SAP, vedere l'articolo relativo all' [integrazione dei dati SAP con Azure Data Factory whitepaper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) con informazioni dettagliate introduttive, comparsing e linee guida.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore della tabella SAP è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da una tabella SAP in qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats) .

In particolare, questo connettore di tabella SAP supporta:

- Copia di dati da una tabella SAP in:

  - SAP ERP Central Component (SAP ECC) versione 7,01 o successiva (in uno stack del pacchetto di supporto SAP recente rilasciato dopo 2015).
  - SAP Business Warehouse (SAP BW) versione 7,01 o successiva (in uno stack di pacchetti di supporto SAP recente rilasciato dopo 2015).
  - SAP S/4HANA.
  - Altri prodotti in SAP Business Suite versione 7,01 o successiva (in uno stack del pacchetto di supporto SAP recente rilasciato dopo 2015).

- Copia di dati da una tabella di SAP trasparente, una tabella in pool, una tabella cluster e una vista.
- La copia dei dati tramite l'autenticazione di base o le comunicazioni di rete sicure (SNC), se è configurata la SNC.
- Connessione a un server applicazioni SAP o a un server di messaggi SAP.

## <a name="prerequisites"></a>Prerequisiti

Per usare questo connettore di tabella SAP, è necessario:

- Configurare un runtime di integrazione self-hosted (versione 3,17 o successiva). Per altre informazioni, vedere [creare e configurare un runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md).

- Scaricare il [connettore SAP a 64 bit per Microsoft .NET 3,0](https://support.sap.com/en/product/connectors/msnet.html) dal sito Web di SAP e installarlo nel computer del runtime di integrazione self-hosted. Durante l'installazione, assicurarsi di selezionare l'opzione **Installa assembly per GAC** nella finestra **passaggi di installazione facoltativi** .

  ![Installare SAP Connector per .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- L'utente SAP usato nel connettore Data Factory SAP TABLE deve avere le autorizzazioni seguenti:

  - Autorizzazione per l'utilizzo di destinazioni della chiamata di funzione remota (RFC).
  - Autorizzazioni per l'attività Execute dell'oggetto di autorizzazione S_SDSAUTH.

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire le entità Data Factory specifiche del connettore della tabella SAP.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato SAP BW Hub aperto sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| `type` | È necessario impostare la proprietà `type` su `SapTable`. | Sì |
| `server` | Nome del server in cui si trova l'istanza di SAP.<br/>Usare per connettersi a un server applicazioni SAP. | No |
| `systemNumber` | Il numero di sistema del sistema SAP.<br/>Usare per connettersi a un server applicazioni SAP.<br/>Valore consentito: numero decimale A due cifre rappresentato come stringa. | No |
| `messageServer` | Nome host del server dei messaggi SAP.<br/>Usare per connettersi a un server di messaggi SAP. | No |
| `messageServerService` | Nome del servizio o numero di porta del server dei messaggi.<br/>Usare per connettersi a un server di messaggi SAP. | No |
| `systemId` | ID del sistema SAP in cui si trova la tabella.<br/>Usare per connettersi a un server di messaggi SAP. | No |
| `logonGroup` | Il gruppo di accesso per il sistema SAP.<br/>Usare per connettersi a un server di messaggi SAP. | No |
| `clientId` | ID del client nel sistema SAP.<br/>Valore consentito: numero decimale A tre cifre rappresentato come stringa. | Sì |
| `language` | Linguaggio utilizzato dal sistema SAP.<br/>Il valore predefinito è `EN`.| No |
| `userName` | Nome dell'utente che ha accesso al server SAP. | Sì |
| `password` | Password dell'utente. Contrassegnare questo campo con il tipo di `SecureString` per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| `sncMode` | Indicatore di attivazione di SNC per accedere al server SAP in cui si trova la tabella.<br/>Usare se si vuole usare SNC per connettersi al server SAP.<br/>I valori consentiti sono `0` (disattivato, valore predefinito) o `1` (on). | No |
| `sncMyName` | Nome della SNC dell'Initiator per accedere al server SAP in cui si trova la tabella.<br/>Si applica quando `sncMode` è on. | No |
| `sncPartnerName` | Nome della SNC del partner di comunicazione per accedere al server SAP in cui si trova la tabella.<br/>Si applica quando `sncMode` è on. | No |
| `sncLibraryPath` | Libreria del prodotto di sicurezza esterna per accedere al server SAP in cui si trova la tabella.<br/>Si applica quando `sncMode` è on. | No |
| `sncQop` | Qualità SNC del livello di protezione da applicare.<br/>Si applica quando `sncMode` è on. <br/>I valori consentiti sono `1` (autenticazione), `2` (integrità), `3` (privacy), `8` (impostazione predefinita), `9` (massimo). | No |
| `connectVia` | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Un runtime di integrazione self-hosted è obbligatorio, come indicato in precedenza in [prerequisiti](#prerequisites). |Sì |

**Esempio 1: connettersi a un server applicazioni SAP**

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

### <a name="example-2-connect-to-an-sap-message-server"></a>Esempio 2: connettersi a un server di messaggi SAP

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

### <a name="example-3-connect-by-using-snc"></a>Esempio 3: connettersi tramite SNC

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

Per un elenco completo delle sezioni e delle proprietà per la definizione dei set di impostazioni, vedere [Datasets](concepts-datasets-linked-services.md). Nella sezione seguente viene fornito un elenco delle proprietà supportate dal set di dati della tabella SAP.

Per copiare dati da e verso il SAP BW servizio collegato Hub aperto, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| `type` | È necessario impostare la proprietà `type` su `SapTableResource`. | Sì |
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

Per un elenco completo delle sezioni e delle proprietà per la definizione delle attività, vedere [pipeline](concepts-pipelines-activities.md). Nella sezione seguente viene fornito un elenco delle proprietà supportate dall'origine della tabella SAP.

### <a name="sap-table-as-source"></a>Tabella SAP come origine

Per copiare dati da una tabella SAP, sono supportate le proprietà seguenti:

| Proprietà                         | Descrizione                                                  | Obbligatorio |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | È necessario impostare la proprietà `type` su `SapTableSource`.         | Sì      |
| `rowCount`                         | Numero di righe da recuperare.                              | No       |
| `rfcTableFields`                   | Campi (colonne) da copiare dalla tabella SAP. Ad esempio, `column0, column1`. | No       |
| `rfcTableOptions`                  | Opzioni per filtrare le righe in una tabella SAP. Ad esempio, `COLUMN0 EQ 'SOMEVALUE'`. Vedere anche la tabella degli operatori di query SAP più avanti in questo articolo. | No       |
| `customRfcReadTableFunctionModule` | Un modulo della funzione RFC personalizzato che può essere usato per leggere i dati da una tabella SAP.<br>È possibile usare un modulo della funzione RFC personalizzato per definire il modo in cui i dati vengono recuperati dal sistema SAP e restituiti a Data Factory. Il modulo della funzione personalizzata deve disporre di un'interfaccia implementata (importazione, esportazione, tabelle) simile a `/SAPDS/RFC_READ_TABLE2`, ovvero l'interfaccia predefinita utilizzata da Data Factory. | No       |
| `partitionOption`                  | Meccanismo di partizione per la lettura da una tabella SAP. Le opzioni supportate includono: <ul><li>`None`</li><li>`PartitionOnInt` (valori integer o Integer normali con riempimento zero a sinistra, ad esempio `0000012345`)</li><li>`PartitionOnCalendarYear` (4 cifre nel formato "aaaa")</li><li>`PartitionOnCalendarMonth` (6 cifre nel formato "YYYYMM")</li><li>`PartitionOnCalendarDate` (8 cifre nel formato "AAAAMMGG")</li></ul> | No       |
| `partitionColumnName`              | Nome della colonna utilizzata per partizionare i dati.                | No       |
| `partitionUpperBound`              | Valore massimo della colonna specificata in `partitionColumnName` che verrà utilizzato per continuare con il partizionamento. | No       |
| `partitionLowerBound`              | Valore minimo della colonna specificata in `partitionColumnName` che verrà utilizzato per continuare con il partizionamento. | No       |
| `maxPartitionsNumber`              | Numero massimo di partizioni in cui dividere i dati.     | No       |

>[!TIP]
>Se la tabella SAP include un volume elevato di dati, ad esempio diversi miliardi di righe, usare `partitionOption` e `partitionSetting` per suddividere i dati in partizioni più piccole. In questo caso, i dati vengono letti per partizione e ogni partizione di dati viene recuperata dal server SAP tramite una singola chiamata RFC.<br/>
<br/>
>Prendendo `partitionOption` come `partitionOnInt` ad esempio, il numero di righe in ogni partizione viene calcolato con la formula seguente: (Totale righe che rientrano tra `partitionUpperBound` e `partitionLowerBound`)/`maxPartitionsNumber`.<br/>
<br/>
>Per caricare le partizioni di dati in parallelo per velocizzare la copia, il grado di parallelismo viene controllato dall'impostazione [`parallelCopies`](copy-activity-performance.md#parallel-copy) sull'attività di copia. Se, ad esempio, si imposta `parallelCopies` su quattro, Data Factory genera ed esegue in modo simultaneo quattro query basate sull'opzione di partizione e sulle impostazioni specificate e ogni query recupera una parte di dati dalla tabella SAP. Si consiglia vivamente di `maxPartitionsNumber` un multiplo del valore della proprietà `parallelCopies`. Quando si copiano dati in un archivio dati basato su file, viene anche riordinato di scrivere in una cartella come più file (specificare solo il nome della cartella), nel qual caso le prestazioni sono migliori rispetto alla scrittura in un singolo file.

In `rfcTableOptions`, è possibile usare gli operatori di query SAP comuni seguenti per filtrare le righe:

| Operatore | Descrizione |
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
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>Mapping dei tipi di dati per una tabella SAP

Quando si copiano dati da una tabella SAP, vengono usati i mapping seguenti tra i tipi di dati della tabella SAP e i tipi di dati Azure Data Factory interim. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo SAP ABAP | Tipo di dati provvisorio di Data Factory |
|:--- |:--- |
| `C` (stringa) | `String` |
| `I` (Integer) | `Int32` |
| `F` (float) | `Double` |
| `D` (Data) | `String` |
| `T` (ora) | `String` |
| `P` (BCD compresso, Currency, Decimal, Qty) | `Decimal` |
| `N` (numerico) | `String` |
| `X` (binario e non elaborato) | `String` |

## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
