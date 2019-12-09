---
title: Copiare dati da SAP Business Warehouse tramite l'hub aperto
description: Informazioni su come copiare dati da SAP Business Warehouse (BW) tramite Open Hub in archivi dati sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: 84098901d58e2087c7ece77049e445bb5c76f2a9
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74923790"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Copiare dati da SAP Business Warehouse tramite Open Hub usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da SAP Business Warehouse (BW) tramite Open Hub. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

>[!TIP]
>Per informazioni sul supporto generale di ADF sullo scenario di integrazione dei dati SAP, vedere l'articolo relativo all' [integrazione dei dati SAP con Azure Data Factory whitepaper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) con informazioni dettagliate introduttive, comparsing e linee guida.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo SAP Business Warehouse tramite connettore Open Hub è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da SAP Business Warehouse tramite Open Hub in qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore SAP Business Warehouse Open Hub supporta:

- SAP Business Warehouse **versione 7,01 o successiva (in uno stack del pacchetto di supporto SAP recente rilasciato dopo l'anno 2015)** .
- Copia dei dati tramite tabella locale Open Hub Destination che può essere DSO, InfoCube, MultiProvider, DataSource e così via.
- La copia di dati usando l'autenticazione di base.
- Connessione al server applicazioni.

## <a name="sap-bw-open-hub-integration"></a>Integrazione SAP BW Open Hub 

Il [servizio SAP BW Open Hub](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) consente di estrarre in modo efficiente dati da SAP BW. Il diagramma seguente mostra uno dei flussi tipici che i clienti possono osservare nel proprio sistema SAP, con flusso di dati da SAP ECC -> PSA -> DSO -> Cubo.

SAP BW Open Hub Destination (OHD) definisce la destinazione di inoltro dei dati SAP. Qualsiasi oggetto supportato da SAP Trasferimento dati Process (DTP) può essere usato come origini dati Open Hub, ad esempio DSO, InfoCube, DataSource e così via. Tipo di destinazione dell'hub aperto, in cui sono archiviati i dati inoltrati, possono essere tabelle di database (locale o remota) e file flat. Questo connettore SAP BW Open Hub supporta la copia dei dati dalla tabella locale OHD in BW. Se si usano altri tipi, è possibile connettersi direttamente al database o al file system usando altri connettori.

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Flusso di estrazione Delta

ADF SAP BW connettore Hub aperto offre due proprietà facoltative: `excludeLastRequest` e `baseRequestId` che possono essere usate per gestire il carico Delta dall'hub aperto. 

- **excludeLastRequestId**: indica se escludere i record dell'ultima richiesta. Il valore predefinito è true. 
- **baseRequestId**: ID della richiesta per il caricamento Delta. Una volta impostato, verranno recuperati solo i dati con RequestId superiori al valore di questa proprietà. 

In generale, l'estrazione da SAP InfoProviders a Azure Data Factory (ADF) è costituita da due passaggi: 

1. **Processo di Trasferimento dati SAP BW (DTP)** Questo passaggio copia i dati da un SAP BW InfoProvider a una tabella SAP BW Open Hub 

1. **Copia dei dati ADF** In questo passaggio la tabella degli hub aperti viene letta dal connettore ADF 

![Flusso di estrazione Delta](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

Nel primo passaggio viene eseguito un DTP. Ogni esecuzione crea un nuovo ID richiesta SAP. L'ID richiesta viene archiviato nella tabella Hub aperto e viene quindi usato dal connettore ADF per identificare il Delta. I due passaggi vengono eseguiti in modo asincrono: il DTP viene attivato da SAP e la copia dei dati ADF viene attivata tramite ADF. 

Per impostazione predefinita, ADF non legge il Delta più recente dalla tabella Hub aperta (l'opzione "Escludi ultima richiesta" è true). A questo proposito, i dati in ADF non sono aggiornati al 100% con i dati nella tabella dell'hub aperto (manca l'ultimo Delta). In restituzione, questa procedura assicura che nessuna riga venga persa causata dall'estrazione asincrona. Funziona correttamente anche quando ADF legge la tabella dell'hub aperto mentre il DTP sta ancora scrivendo nella stessa tabella. 

In genere l'ID richiesta massimo copiato viene archiviato nell'ultima esecuzione di ADF in un archivio dati di staging, ad esempio BLOB di Azure nel diagramma precedente. La stessa richiesta non viene pertanto letta una seconda volta da ADF nell'esecuzione successiva. Nel frattempo, si noti che i dati non vengono eliminati automaticamente dalla tabella dell'hub aperto.

Per una corretta gestione Delta non è consentito avere ID richiesta da DTPs diversi nella stessa tabella dell'hub aperta. Pertanto, non è necessario creare più di un DTP per ogni destinazione di hub aperto (OHD). Quando è necessaria l'estrazione completa e Delta dallo stesso InfoProvider, è necessario creare due OHDs per lo stesso InfoProvider. 

## <a name="prerequisites"></a>Prerequisiti

Per usare il connettore SAP Business Warehouse Open Hub, è necessario:

- Configurare un runtime di integrazione self-hosted con la versione 3.13 o successive. Per i dettagli, vedere l'articolo [Runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md).

- Scaricare il **[connettore SAP .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) a 64 bit** dal sito Web di SAP e installarlo nel computer di runtime di integrazione self-hosted. Durante l'installazione, nella finestra della procedura di configurazione facoltativa verificare che sia selezionata l'opzione **Install Assemblies to GAC** (Installa assembly nella Global Assembly Cache) come illustrato nell'immagine seguente. 

    ![Installare il connettore SAP .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- L'utente SAP nel connettore BW di Data Factory deve disporre delle autorizzazioni seguenti: 

    - Autorizzazione per RFC e SAP BW. 
    - Autorizzazioni per l'attività "Execute" dell'oggetto di autorizzazione "S_SDSAUTH".

- Creare il tipo SAP Open Hub Destination come **Database Table** (Tabella di database) con l'opzione "Technical Key" (Chiave tecnica) selezionata.  È inoltre consigliabile lasciare deselezionata l'opzione di eliminazione dei dati dalla tabella, anche se questa impostazione non è obbligatoria. Sfruttare DTP (esecuzione diretta o integrazione nella catena di processo esistente) per il trasferimento dei dati dall'oggetto di origine scelto (ad esempio il cubo) nella tabella di destinazione dell'hub aperto.

## <a name="getting-started"></a>Inizia ora

> [!TIP]
>
> Per una procedura dettagliata sull'uso di SAP BW connettore Open Hub, vedere [caricare dati da SAP Business Warehouse (BW) con Azure Data Factory](load-sap-bw-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore SAP Business Warehouse Open Hub.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di SAP Business Warehouse Open Hub sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type deve essere impostata su: **SapOpenHub** | SÌ |
| server | Nome del server in cui si trova l'istanza di SAP BW. | SÌ |
| systemNumber | Numero del sistema SAP BW.<br/>Valore consentito: numero decimale a due cifre rappresentato come stringa. | SÌ |
| clientId | ID del client nel sistema SAP BW.<br/>Valore consentito: numero decimale a tre cifre rappresentato come stringa. | SÌ |
| Lingua | Lingua usata dal sistema SAP. | No (valore predefinito: **EN**)|
| userName | Nome dell'utente che ha accesso al server SAP. | SÌ |
| password | Password per l'utente. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | SÌ |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È necessario un runtime di integrazione self-hosted come indicato in [Prerequisiti](#prerequisites). |SÌ |

**Esempio:**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
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

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati SAP BW Apri Hub.

Per copiare dati da e in SAP BW Open Hub, impostare la proprietà type del set di dati su **SapOpenHubTable**. Sono supportate le proprietà seguenti.

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **SapOpenHubTable**.  | SÌ |
| openHubDestinationName | Il nome di Open Hub Destination da cui copiare i dati. | SÌ |

Se si imposta `excludeLastRequest` e `baseRequestId` nel set di dati, è ancora supportata così com'è, mentre si consiglia di usare il nuovo modello in origine attività in futuro.

**Esempio:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine SAP BW Open Hub.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW Open Hub come origine

Per copiare dati da SAP BW Hub aperto, nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** dell'origine dell'attività di copia deve essere impostata su **SapOpenHubSource**. | SÌ |
| excludeLastRequest | Se escludere i record dell'ultima richiesta. | No (il valore predefinito è **true**) |
| baseRequestId | L'ID della richiesta per il caricamento differenziale. Una volta impostata questa proprietà, verranno recuperati solo i dati con requestId **maggiore** del valore di questa proprietà.  | No |

>[!TIP]
>Se ad esempio la tabella Open Hub contiene solo dati generati in base a ID di richiesta singola, si esegue sempre il caricamento completo e si sovrascrivono i dati esistenti nella tabella oppure si esegue DTP una sola volta per il test, ricordarsi di deselezionare l'opzione "excludeLastRequest" per la copia dei dati.

Per velocizzare il caricamento dei dati, è possibile impostare [`parallelCopies`](copy-activity-performance.md#parallel-copy) sull'attività di copia per caricare i dati da SAP BW Hub aperto in parallelo. Se, ad esempio, si imposta `parallelCopies` su quattro, Data Factory esegue contemporaneamente quattro chiamate RFC e ogni chiamata RFC recupera una parte di dati dalla SAP BW tabella dell'hub aperta partizionata in base all'ID richiesta e all'ID del pacchetto di impaginazione. Questa condizione si applica quando il numero di ID richiesta di DTP univoco + ID pacchetto è maggiore del valore di `parallelCopies`. Quando si copiano dati in un archivio dati basato su file, viene anche riordinato di scrivere in una cartella come più file (specificare solo il nome della cartella), nel qual caso le prestazioni sono migliori rispetto alla scrittura in un singolo file.

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
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
                "type": "SapOpenHubSource",
                "excludeLastRequest": true
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Mapping dei tipi di dati per SAP BW Open Hub

Quando si copiano dati da SAP BW Open Hub, vengono usati i mapping seguenti tra i tipi di dati di SAP BW e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo SAP ABAP | Tipo di dati provvisori di Data Factory |
|:--- |:--- |
| C (String) | Stringa |
| I (integer) | Int32 |
| F (Float) | DOUBLE |
| D (Date) | Stringa |
| T (Time) | Stringa |
| P (BCD Packed, Currency, Decimal, Qty) | DECIMAL |
| N (Numc) | Stringa |
| X (Binary e Raw) | Stringa |

## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
