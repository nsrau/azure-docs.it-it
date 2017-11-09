---
title: 'Connessione dati: input di flussi di dati da un flusso di eventi | Microsoft Docs'
description: Informazioni sulla configurazione di una connessione dati ad Analisi di flusso denominata 'input'. Gli input includono un flusso di dati dagli eventi, oltre a dati di riferimento.
keywords: flusso di dati, connessione dati, flusso di eventi
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 8155823c-9dd8-4a6b-8393-34452d299b68
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 07/05/2017
ms.author: samacha
ms.openlocfilehash: 652137cf7a41f8d90a56aebe9f82fd37d5e4683d
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="data-connection-learn-about-data-stream-inputs-from-events-to-stream-analytics"></a>Connessione dati: informazioni sugli input del flusso di dati dagli eventi ad Analisi di flusso
La connessione dati a un processo di Analisi di flusso è un flusso di eventi da un'origine dati, definito *input* del processo. Analisi di flusso si integra perfettamente con le origini del flusso dei dati di Azure, come [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/), [Hub IoT di Azure](https://azure.microsoft.com/services/iot-hub/) e [Archiviazione BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/). Queste origini di input possono appartenere alla stessa sottoscrizione di Azure del processo di analisi o a un'altra sottoscrizione.

## <a name="data-input-types-data-stream-and-reference-data"></a>Tipi di input di dati: flusso dei dati e dati di riferimento
Quando sono inviati tramite push a un'origine, i dati vengono utilizzati dal processo di Analisi di flusso ed elaborati in tempo reale. Gli input si dividono in due tipi: input del flusso dei dati e input dei dati di riferimento.

### <a name="data-stream-inputs"></a>Input del flusso dei dati
Un flusso dei dati è una sequenza non associata di eventi che accadono nel tempo. I processi di Analisi di flusso devono includere almeno un input del flusso dei dati. Gli hub eventi, l'hub IoT e l'archiviazione BLOB sono supportati come origini di input del flusso dei dati. Gli hub eventi vengono usati per raccogliere flussi di eventi da più dispositivi e servizi, ad esempio i feed attività dei social media, le informazioni sulle quotazioni azionarie o i dati di sensori. Gli hub IoT sono ottimizzati per raccogliere dati dai dispositivi connessi in scenari di Internet delle cose (IoT).  L'archiviazione BLOB può essere usata come origine di input per l'inserimento di dati in blocco come flusso, ad esempio i file di log.  

### <a name="reference-data"></a>Dati di riferimento
Analisi di flusso supporta anche l'input noto come *dati di riferimento*. Si tratta di dati ausiliari che sono statici o cambiano lentamente e che di solito sono usati per eseguire correlazioni e ricerche. È ad esempio possibile unire i dati nell'input del flusso dei dati con quelli inclusi nei dati di riferimento, proprio come si esegue un join SQL per cercare valori statici. L'archiviazione BLOB di Azure è attualmente l'unica origine di input supportata per i dati di riferimento. I BLOB di origine dei dati di riferimento non possono avere una dimensione superiore a 100 MB.

Per informazioni su come creare input dei dati di riferimento, vedere [Uso dei dati di riferimento](stream-analytics-use-reference-data.md).  

## <a name="compression"></a>Compressione

Analisi di flusso di Azure supporta la compressione in tutte le origini di input del flusso di dati (Hub eventi, hub IoT e archiviazione BLOB). Questa funzionalità aggiunge una nuova opzione nell'elenco a discesa del pannello **Nuovo input** del portale di Azure consentendo di scegliere facoltativamente di comprimere i flussi di dati. I tipi di compressione attualmente supportati sono None, GZip e Deflate. 

La compressione non è supportata in parallelo con la serializzazione Avro e non è applicabile ai dati di riferimento. 

## <a name="create-data-stream-input-from-event-hubs"></a>Creare un input del flusso dei dati dagli hub eventi

Hub eventi di Azure offre una tecnologia di inserimento altamente scalabile per eventi di pubblicazione-sottoscrizione. Un hub eventi può raccogliere milioni di eventi al secondo per consentire di elaborare e analizzare enormi quantità di dati generati dalle applicazioni e dai dispositivi connessi. Hub eventi e Analisi di flusso offrono una soluzione end-to-end per l'analisi in tempo reale. Gli hub eventi consentono di inserire gli eventi in Azure tramite feed e i processi di Analisi di flusso sono in grado di elaborare tali eventi. Entrambe le operazioni vengono eseguite in tempo reale. È ad esempio possibile inviare clic sul Web, letture di sensori o eventi di log online agli hub eventi e quindi creare processi di Analisi di flusso per usare gli hub eventi come flussi dei dati di input per il filtraggio, l'aggregazione e la correlazione in tempo reale.

Il timestamp predefinito degli eventi provenienti dagli hub eventi in Analisi di flusso è il timestamp in cui l'evento è giunto nell'hub, ovvero `EventEnqueuedUtcTime`. Per elaborare i dati come flusso usando un timestamp nel payload dell'evento, è necessario usare la parola chiave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

### <a name="consumer-groups"></a>Gruppi di consumer
È necessario configurare uno specifico gruppo di consumer per ogni input degli hub eventi di Analisi di flusso. Quando un processo contiene un self-join o ha più input, è possibile che qualche input venga letto da più lettori downstream. Questa situazione influisce sul numero di lettori in un singolo gruppo di consumer. Per evitare di superare il limite di cinque lettori imposto da Hub eventi per ogni gruppo di consumer di ciascuna partizione, è consigliabile definire un gruppo di consumer per ogni processo di Analisi di flusso. È definito anche un limite di 20 gruppi di consumer per ogni hub eventi. Per altre informazioni, vedere [Guida alla programmazione di Hub eventi](../event-hubs/event-hubs-programming-guide.md).

### <a name="configure-an-event-hub-as-a-data-stream-input"></a>Configurare un hub eventi come input del flusso dei dati
La tabella seguente contiene la descrizione delle proprietà disponibili nel pannello **Nuovo input** del portale di Azure quando si configura un hub eventi come input.

| Proprietà | Descrizione |
| --- | --- |
| **Alias di input** |Nome descrittivo che viene usato nella query del processo per fare riferimento a questo input. |
| **Spazio dei nomi del bus di servizio** |Spazio dei nomi del bus di servizio di Azure che viene usato come contenitore per un set di entità di messaggistica. Quando si crea un nuovo hub eventi, viene creato anche uno spazio dei nomi del bus di servizio. |
| **Nome hub eventi** |Nome dell'hub eventi da usare come input. |
| **Nome criteri hub eventi** |Criteri di accesso condiviso che consentono di accedere all'hub eventi. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. |
| **Gruppo di consumer dell'hub eventi** (facoltativa) |Gruppo di consumer da usare per l'inserimento di dati dall'hub eventi. Se non è specificato alcun gruppo di consumer, il processo di Analisi di flusso usa quello predefinito. È consigliabile usare un gruppo di consumer distinto per ogni processo di Analisi di flusso. |
| **Formato di serializzazione eventi** |Formato di serializzazione (JSON, CSV o Avro) del flusso dei dati in ingresso. |
| **Codifica** | L'unico formato di codifica attualmente supportato è UTF-8. |
| **Compressione** (facoltativo) | Il tipo di compressione (None, GZip o Deflate) del flusso dei dati in ingresso. |

Quando i dati provengono da un hub eventi, è possibile accedere ai campi di metadati seguenti nella query di Analisi di flusso:

| Proprietà | Descrizione |
| --- | --- |
| **EventProcessedUtcTime** |Data e ora di elaborazione dell'evento in Analisi di flusso. |
| **EventEnqueuedUtcTime** |Data e ora di ricezione dell'evento da parte di Hub eventi. |
| **PartitionId** |ID di partizione in base zero per l'adattatore di input. |

Ad esempio, tramite questi campi, è possibile scrivere una query simile alla seguente:

````
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
````

> [!NOTE]
> Quando si usa Hub eventi come un endpoint delle route dell'hub IoT, è possibile accedere ai metadati dell'hub IoT usando la funzione [GetMetadataPropertyValue](https://msdn.microsoft.com/en-us/library/azure/mt793845.aspx).
> 

## <a name="create-data-stream-input-from-iot-hub"></a>Creare un input del flusso dei dati dall'hub IoT
Hub IoT di Azure è un servizio di inserimento di eventi di pubblicazione-sottoscrizione altamente scalabile ottimizzato per scenari IoT.

Il timestamp predefinito degli eventi provenienti da un hub IoT in Analisi di flusso è il timestamp in cui l'evento è giunto nell'hub IoT, ovvero `EventEnqueuedUtcTime`. Per elaborare i dati come flusso usando un timestamp nel payload dell'evento, è necessario usare la parola chiave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

> [!NOTE]
> Solo i messaggi inviati con una proprietà `DeviceClient` possono essere elaborati.
> 
> 

### <a name="consumer-groups"></a>Gruppi di consumer
È necessario configurare uno specifico gruppo di consumer per ogni input dell'hub IoT di Analisi di flusso. Quando un processo contiene un self-join o ha più input, è possibile che qualche input venga letto da più lettori downstream. Questa situazione influisce sul numero di lettori in un singolo gruppo di consumer. Per evitare di superare il limite di cinque lettori imposto da Hub IoT di Azure per ogni gruppo di consumer di ciascuna partizione, è consigliabile definire un gruppo di consumer per ogni processo di Analisi di flusso.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Configurare un hub IoT come input del flusso dei dati
La tabella seguente contiene la descrizione delle proprietà disponibili nel pannello **Nuovo input** del portale di Azure quando si configura un hub IoT come input.

| Proprietà | Descrizione |
| --- | --- |
| **Alias di input** |Nome descrittivo che viene usato nella query del processo per fare riferimento a questo input.|
| **Hub IoT** |Nome dell'hub IoT da usare come input. |
| **Endpoint** |Endpoint per l'hub IoT.|
| **Nome criteri di accesso condiviso** |Criteri di accesso condiviso che consentono di accedere all'hub IoT. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. |
| **Chiave criteri di accesso condiviso** |Chiave di accesso condiviso usata per autorizzare l'accesso all'hub IoT. |
| **Gruppo di consumer** (facoltativa) |Gruppo di consumer da usare per inserire dati dall'hub IoT. Se non è specificato alcun gruppo di consumer, un processo di Analisi di flusso usa quello predefinito. È consigliabile usare un gruppo di consumer diverso per ogni processo di Analisi di flusso. |
| **Formato di serializzazione eventi** |Formato di serializzazione (JSON, CSV o Avro) del flusso dei dati in ingresso. |
| **Codifica** |L'unico formato di codifica attualmente supportato è UTF-8. |
| **Compressione** (facoltativo) | Il tipo di compressione (None, GZip o Deflate) del flusso dei dati in ingresso. |

Quando i dati provengono da un hub IoT, è possibile accedere ai campi di metadati seguenti nella query di Analisi di flusso:

| Proprietà | Descrizione |
| --- | --- |
| **EventProcessedUtcTime** |Data e ora di elaborazione dell'evento. |
| **EventEnqueuedUtcTime** |Data e ora di ricezione dell'evento da parte dell'hub IoT. |
| **PartitionId** |ID di partizione in base zero per l'adattatore di input. |
| **IoTHub.MessageId** | ID usato per correlare la comunicazione bidirezionale nell'hub IoT. |
| **IoTHub.CorrelationId** |ID usato nelle risposte dei messaggi e nei feedback nell'hub IoT. |
| **IoTHub.ConnectionDeviceId** |ID di autenticazione usato per inviare questo messaggio. Questo valore viene riportato nei messaggi servicebound dall'hub IoT. |
| **IoTHub.ConnectionDeviceGenerationId** |ID di generazione del dispositivo autenticato che è stato usato per inviare questo messaggio. Questo valore viene riportato nei messaggi servicebound dall'hub IoT. |
| **IoTHub.EnqueuedTime** |Ora in cui il messaggio è stato ricevuto dall'hub IoT. |
| **IoTHub.StreamId** |Proprietà di evento personalizzato aggiunta dal dispositivo mittente. |


## <a name="create-data-stream-input-from-blob-storage"></a>Creare un input del flusso dei dati dall'archiviazione BLOB
Per gli scenari con grandi quantità di dati non strutturati da archiviare nel cloud, l'archiviazione BLOB di Azure offre una soluzione scalabile e conveniente. I dati nell'archiviazione BLOB sono in genere considerati dati inattivi, ma possono essere elaborati come flusso dei dati da Analisi di flusso. Uno scenario tipico per gli input di archiviazione BLOB con Analisi di flusso consiste nell'elaborazione dei log. In questo scenario i dati di telemetria sono stati acquisiti da un sistema e devono essere analizzati ed elaborati per estrarre dati significativi.

Il timestamp predefinito degli eventi dell'archiviazione BLOB in Analisi di flusso è il timestamp dell'ultima modifica del BLOB, ovvero `BlobLastModifiedUtcTime`. Per elaborare i dati come flusso usando un timestamp nel payload dell'evento, è necessario usare la parola chiave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

Gli input in formato CSV *richiedono* una riga di intestazione per definire i campi per il set di dati. Inoltre, tutti i campi delle righe di intestazione devono essere univoci.

> [!NOTE]
> Analisi di flusso di Azure non supporta l'aggiunta di contenuto a un file di BLOB esistente. Analisi di flusso di Azure visualizza ogni file una sola volta e tutte le modifiche apportate al file dopo che il processo ha letto i dati non vengono elaborate. La procedura consigliata consiste nel caricare simultaneamente tutti i dati per un file di BLOB e quindi aggiungere gli altri eventi più nuovi in un nuovo file di BLOB diverso.
> 

### <a name="configure-blob-storage-as-a-data-stream-input"></a>Configurare l'archiviazione BLOB come input del flusso dei dati

La tabella seguente contiene la descrizione delle proprietà disponibili nel pannello **Nuovo input** del portale di Azure quando si configura l'archiviazione BLOB come input.

| Proprietà | Descrizione |
| --- | --- |
| **Alias di input** | Nome descrittivo che viene usato nella query del processo per fare riferimento a questo input. |
| **Account di archiviazione** | Nome dell'account di archiviazione in cui si trovano i file BLOB. |
| **Chiave dell'account di archiviazione** | Chiave privata associata all'account di archiviazione. |
| **Contenitore** | Contenitore per l'input del servizio BLOB. I contenitori forniscono un raggruppamento logico per gli oggetti BLOB archiviati nel servizio BLOB di Microsoft Azure. Quando si carica un oggetto BLOB nel servizio Archiviazione BLOB di Azure, è necessario specificare un contenitore per tale BLOB. |
| **Modello percorso** (facoltativa) | Percorso del file usato per trovare gli oggetti BLOB nel contenitore specificato. All'interno del percorso è possibile specificare una o più istanze delle tre variabili seguenti: `{date}`, `{time}` o `{partition}`<br/><br/>Esempio 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Esempio 2: `cluster1/logs/{date}`<br/><br/>Il carattere `*` non è un valore consentito per il prefisso del percorso. Sono consentiti solo <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Caratteri BLOB di Azure</a> validi. |
| **Formato data** (facoltativa) | Formato della data in base al quale vengono organizzati i file, se si usa la variabile date nel percorso. Esempio: `YYYY/MM/DD` |
| **Formato ora** (facoltativa) |  Formato dell'ora in base al quale vengono organizzati i file, se si usa la variabile time nel percorso. L'unico valore attualmente supportato è `HH`. |
| **Formato di serializzazione eventi** | Formato di serializzazione (JSON, CSV o Avro) per i flussi dei dati in ingresso. |
| **Codifica** | Per CSV e JSON, l'unico formato di codifica attualmente supportato è UTF-8. |
| **Compressione** (facoltativo) | Il tipo di compressione (None, GZip o Deflate) del flusso dei dati in ingresso. |

Quando i dati provengono da un'origine di archiviazione BLOB, è possibile accedere ai campi di metadati seguenti nella query di Analisi di flusso:

| Proprietà | Descrizione |
| --- | --- |
| **BlobName** |Nome del BLOB di input da cui proviene l'evento. |
| **EventProcessedUtcTime** |Data e ora di elaborazione dell'evento in Analisi di flusso. |
| **BlobLastModifiedUtcTime** |Data e ora dell'ultima modifica del BLOB. |
| **PartitionId** |ID di partizione in base zero per l'adattatore di input. |

Ad esempio, tramite questi campi, è possibile scrivere una query simile alla seguente:

````
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
````

## <a name="get-help"></a>Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
Sono state apprese le opzioni di connessione dei dati in Azure per i processi di Analisi di flusso. Per altre informazioni su Analisi di flusso, vedere:

* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
