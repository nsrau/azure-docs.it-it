---
title: Trasmettere dati come input in Analisi di flusso di Azure
description: Informazioni sulla configurazione di una connessione dati in Analisi di flusso di Azure. Gli input includono un flusso di dati dagli eventi, oltre a dati di riferimento.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2020
ms.openlocfilehash: 649abf6d07a95c7f20f6416f7d3155f8d115782b
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127570"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Trasmettere dati come input in Analisi di flusso

Analisi di flusso si integra perfettamente con i flussi dei dati di Azure come input da tre tipi di risorse:

- [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/)
- [Hub IoT Azure](https://azure.microsoft.com/services/iot-hub/) 
- [Archivio BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/) 
- [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 

Queste risorse di input possono trovarsi nella stessa sottoscrizione del processo di Analisi di flusso o in una sottoscrizione diversa.

### <a name="compression"></a>Compressione

Analisi di flusso supporta la compressione tra tutte le origini di input del flusso dei dati. Tipi di compressione supportati: compressione GZip, Deflate e nessuna. Il supporto per la compressione non è disponibile per i dati di riferimento. Se il formato di input è dati Avro compressi, viene gestito in modo trasparente. Non è necessario specificare il tipo di compressione con la serializzazione Avro. 

## <a name="create-edit-or-test-inputs"></a>Creare, modificare o testare gli input

È possibile usare il [portale di Azure](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md) e [Visual Studio Code](quick-create-visual-studio-code.md) per aggiungere e visualizzare o modificare gli input esistenti nel processo di streaming. È anche possibile testare le connessioni di input e [le query di test](stream-analytics-manage-job.md#test-your-query) da dati di esempio dal portale di Azure, [Visual Studio](stream-analytics-vs-tools-local-run.md) e [Visual Studio Code](visual-studio-code-local-run.md). Quando si scrive una query, si elencano gli input nella clausola FROM. È possibile ottenere l'elenco degli input disponibili dalla pagina **Query** del portale. Per usare più input, è possibile usare il comando `JOIN` per unirli oppure scrivere più query `SELECT`.


## <a name="stream-data-from-event-hubs"></a>Trasmettere dati da Hub eventi

Hub eventi di Azure offre investitori di eventi di pubblicazione-sottoscrizione altamente scalabili. Un hub eventi può raccogliere milioni di eventi al secondo per consentire di elaborare e analizzare enormi quantità di dati generati dalle applicazioni e dai dispositivi connessi. Hub eventi e Analisi di flusso in combinazione offrono una soluzione end-to-end per l'analisi in tempo reale. Hub eventi consente di inserire eventi in Azure in tempo reale e i processi di Analisi di flusso sono in grado di elaborare tali eventi in tempo reale. È ad esempio possibile inviare clic sul Web, letture di sensori o eventi di log online agli hub eventi e quindi creare processi di Analisi di flusso per usare gli hub eventi come flussi dei dati di input per il filtraggio, l'aggregazione e la correlazione in tempo reale.

Il timestamp `EventEnqueuedUtcTime` si riferisce all'arrivo di un evento nell'hub eventi ed è il timestamp predefinito degli eventi provenienti da Hub eventi verso Analisi di flusso. Per elaborare i dati come flusso usando un timestamp nel payload dell'evento, è necessario usare la parola chiave [TIMESTAMP BY](/stream-analytics-query/timestamp-by-azure-stream-analytics).

### <a name="event-hubs-consumer-groups"></a>Gruppo di consumer dell'hub eventi

È necessario configurare uno specifico gruppo di consumer per ogni input degli hub eventi di Analisi di flusso. Quando un processo contiene un self-join o ha più input, è possibile che alcuni input vengano letti da più lettori downstream. Questa situazione influisce sul numero di lettori in un singolo gruppo di consumer. Per evitare di superare il limite di cinque lettori imposto da Hub eventi per ogni gruppo di consumer di ciascuna partizione, è consigliabile definire un gruppo di consumer per ogni processo di Analisi di flusso. È definito anche un limite di 20 gruppi di consumer per un hub eventi di livello Standard. Per altre informazioni, vedere [Risolvere i problemi delle connessioni di input](stream-analytics-troubleshoot-input.md).

### <a name="create-an-input-from-event-hubs"></a>Creare un input da Hub eventi

La tabella seguente descrive le proprietà disponibili nella pagina **Nuovo input** del portale di Azure per lo streaming dell'input dei dati da un hub eventi:

| Proprietà | Descrizione |
| --- | --- |
| **Alias di input** |Nome descrittivo che viene usato nella query del processo per fare riferimento a questo input. |
| **Sottoscrizione** | Scegliere la sottoscrizione in cui esiste la risorsa di Hub eventi. | 
| **Spazio dei nomi dell'hub eventi** | Lo spazio dei nomi dell'hub eventi è un contenitore per un set di entità di messaggistica. Quando si crea un nuovo hub eventi, viene creato anche lo spazio dei nomi. |
| **Nome hub eventi** | Nome dell'hub eventi da usare come input. |
| **Nome criteri hub eventi** | Criteri di accesso condiviso che consentono di accedere all'hub eventi. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. Il valore di questa opzione viene inserito automaticamente, a meno che non si selezioni l'opzione per specificare le impostazioni dell'hub eventi manualmente.|
| **Gruppo di consumer dell'hub eventi** (consigliata) | È vivamente consigliato usare un gruppo di consumer distinto per ogni processo di Analisi di flusso. Questa stringa identifica il gruppo di consumer da usare per l'inserimento di dati dall'hub eventi. Se non è specificato alcun gruppo di consumer, il processo di Analisi di flusso usa il gruppo di consumer $Default.  |
| **Chiave di partizione** | Si tratta di un campo facoltativo disponibile solo se il processo è configurato per l'uso del [livello di compatibilità](./stream-analytics-compatibility-level.md) 1,2 o superiore. Se l'input è partizionato da una proprietà, è possibile aggiungere il nome di questa proprietà qui. Viene usato per migliorare le prestazioni della query se include una clausola PARTITION BY o GROUP BY in questa proprietà. Se questo processo usa il livello di compatibilità 1,2 o versione successiva, il valore predefinito di questo campo è "PartitionId". |
| **Formato di serializzazione eventi** | Il formato di serializzazione (JSON, CSV, Avro o [altro (Protobuf, XML, proprietario...)](custom-deserializer.md)) del flusso di dati in ingresso.  Assicurarsi che il formato JSON sia allineato alla specifica e non includa uno 0 iniziale per i numeri decimali. |
| **Encoding** | L'unico formato di codifica attualmente supportato è UTF-8. |
| **Tipo di compressione eventi** | Il tipo di compressione usato per leggere il flusso dei dati in ingresso, ad esempio Nessuno (predefinito), GZip o Deflate. |

Quando i dati provengono da un input del flusso di Hub eventi, è possibile accedere ai campi di metadati seguenti nella query di Analisi di flusso:

| Proprietà | Descrizione |
| --- | --- |
| **EventProcessedUtcTime** |Data e ora di elaborazione dell'evento in Analisi di flusso. |
| **EventEnqueuedUtcTime** |Data e ora di ricezione dell'evento da parte di Hub eventi. |
| **PartitionId** |ID di partizione in base zero per l'adattatore di input. |

Ad esempio, tramite questi campi, è possibile scrivere una query simile alla seguente:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Quando si usa Hub eventi come un endpoint delle route dell'hub IoT, è possibile accedere ai metadati dell'hub IoT usando la [funzione GetMetadataPropertyValue](/stream-analytics-query/getmetadatapropertyvalue).
> 

## <a name="stream-data-from-iot-hub"></a>Dati del flusso dall'hub IoT

Hub IoT di Azure è un servizio di inserimento di eventi di pubblicazione-sottoscrizione altamente scalabile ottimizzato per scenari IoT.

Il timestamp predefinito degli eventi provenienti da un hub IoT in Analisi di flusso è il timestamp in cui l'evento è giunto nell'hub IoT, ovvero `EventEnqueuedUtcTime`. Per elaborare i dati come flusso usando un timestamp nel payload dell'evento, è necessario usare la parola chiave [TIMESTAMP BY](/stream-analytics-query/timestamp-by-azure-stream-analytics).

### <a name="iot-hub-consumer-groups"></a>Gruppo di consumer dell'hub IoT

È necessario configurare uno specifico gruppo di consumer per ogni input dell'hub IoT di Analisi di flusso. Quando un processo contiene un self-join o ha più input, è possibile che qualche input venga letto da più lettori downstream. Questa situazione influisce sul numero di lettori in un singolo gruppo di consumer. Per evitare di superare il limite di cinque lettori imposto da Hub IoT di Azure per ogni gruppo di consumer di ciascuna partizione, è consigliabile definire un gruppo di consumer per ogni processo di Analisi di flusso.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Configurare un hub IoT come input del flusso dei dati

La tabella seguente contiene la descrizione delle proprietà disponibili nella pagina **Nuovo input** del portale di Azure quando si configura un hub IoT come input del flusso.

| Proprietà | Descrizione |
| --- | --- |
| **Alias di input** | Nome descrittivo che viene usato nella query del processo per fare riferimento a questo input.|
| **Sottoscrizione** | Scegliere la sottoscrizione in cui esiste la risorsa dell'hub IoT. | 
| **Hub IoT** | Nome dell'hub IoT da usare come input. |
| **Endpoint** | Endpoint per l'hub IoT.|
| **Nome criteri di accesso condiviso** | Criteri di accesso condiviso che consentono di accedere all'hub IoT. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. |
| **Chiave criteri di accesso condiviso** | Chiave di accesso condiviso usata per autorizzare l'accesso all'hub IoT.  Il valore di questa opzione viene inserito automaticamente, a meno che non si selezioni l'opzione per specificare le impostazioni dell'hub IoT manualmente. |
| **Gruppo di consumer** | È vivamente consigliato usare un gruppo di consumer differente per ogni processo di Analisi di flusso. Il gruppo di consumer viene usato per inserire dati dall'hub IoT. Analisi di flusso usa il gruppo di consumer $Default se non diversamente specificato.  |
| **Chiave di partizione** | Si tratta di un campo facoltativo disponibile solo se il processo è configurato per l'uso del [livello di compatibilità](./stream-analytics-compatibility-level.md) 1,2 o superiore. Se l'input è partizionato da una proprietà, è possibile aggiungere il nome di questa proprietà qui. Viene usato per migliorare le prestazioni della query se include una clausola PARTITION BY o GROUP BY in questa proprietà. Se questo processo usa il livello di compatibilità 1,2 o versione successiva, il valore predefinito di questo campo è "PartitionId". |
| **Formato di serializzazione eventi** | Il formato di serializzazione (JSON, CSV, Avro o [altro (Protobuf, XML, proprietario...)](custom-deserializer.md)) del flusso di dati in ingresso.  Assicurarsi che il formato JSON sia allineato alla specifica e non includa uno 0 iniziale per i numeri decimali. |
| **Encoding** | L'unico formato di codifica attualmente supportato è UTF-8. |
| **Tipo di compressione eventi** | Il tipo di compressione usato per leggere il flusso dei dati in ingresso, ad esempio Nessuno (predefinito), GZip o Deflate. |


Quando si usano i dati di flusso provenienti da un hub IoT, è possibile accedere ai campi di metadati seguenti nella query di Analisi di flusso:

| Proprietà | Descrizione |
| --- | --- |
| **EventProcessedUtcTime** | Data e ora di elaborazione dell'evento. |
| **EventEnqueuedUtcTime** | Data e ora di ricezione dell'evento da parte dell'hub IoT. |
| **PartitionId** | ID di partizione in base zero per l'adattatore di input. |
| **IoTHub.MessageId** | ID usato per correlare la comunicazione bidirezionale nell'hub IoT. |
| **IoTHub.CorrelationId** | ID usato nelle risposte dei messaggi e nei feedback nell'hub IoT. |
| **IoTHub.ConnectionDeviceId** | ID di autenticazione usato per inviare questo messaggio. Questo valore viene riportato nei messaggi servicebound dall'hub IoT. |
| **IoTHub.ConnectionDeviceGenerationId** | ID di generazione del dispositivo autenticato che è stato usato per inviare questo messaggio. Questo valore viene riportato nei messaggi servicebound dall'hub IoT. |
| **IoTHub.EnqueuedTime** | Ora in cui il messaggio è stato ricevuto dall'hub IoT. |


## <a name="stream-data-from-blob-storage-or-data-lake-storage-gen2"></a>Trasmettere dati da un archivio BLOB o Data Lake Storage Gen2
Per gli scenari con grandi quantità di dati non strutturati da archiviare nel cloud, l'archiviazione BLOB di Azure o Azure Data Lake Storage Gen2 (ADLS Gen2) offre una soluzione conveniente e scalabile. I dati nell'archivio BLOB o ADLS Gen2 vengono in genere considerati dati inattivi; Tuttavia, questi dati possono essere elaborati come flusso di dati da analisi di flusso. 

L'elaborazione dei log è uno scenario comunemente usato per l'uso di tali input con l'analisi di flusso. In questo scenario i file di dati di telemetria sono stati acquisiti da un sistema e devono essere analizzati ed elaborati per estrarre dati significativi.

Il timestamp predefinito di un archivio BLOB o di un evento ADLS Gen2 in analisi di flusso è il timestamp in cui è stata apportata l'ultima modifica, ovvero `BlobLastModifiedUtcTime` . Se un BLOB viene caricato in un account di archiviazione a 13:00 e il processo di analisi di flusso di Azure viene avviato con l'opzione *ora* a 13:01, non verrà prelevato perché l'ora modificata non rientra nel periodo di esecuzione del processo.

Se un BLOB viene caricato in un contenitore dell'account di archiviazione alle 13:00 e il processo di Analisi di flusso di Azure viene avviato con *Ora personalizzata* alle 13:00 o prima, il BLOB verrà prelevato quando l'ora modificata rientra nel periodo di esecuzione del processo.

Se un processo di Analisi di flusso di Azure viene avviato con *Adesso* alle 13:00 e un BLOB viene caricato nel contenitore dell'account di archiviazione alle 13:01, Analisi di flusso di Azure preleverà il BLOB. Il timestamp assegnato a ogni BLOB è basato solo su `BlobLastModifiedTime`. La cartella in cui si trova il BLOB non ha alcuna relazione con il timestamp assegnato. Ad esempio, se è presente un BLOB *2019/10-01/00/b1. txt* con un `BlobLastModifiedTime` di 2019-11-11, il timestamp assegnato a questo BLOB è 2019-11-11.

Per elaborare i dati come flusso usando un timestamp nel payload dell'evento, è necessario usare la parola chiave [TIMESTAMP BY](/stream-analytics-query/stream-analytics-query-language-reference). Un processo di analisi di flusso estrae i dati dall'archiviazione BLOB di Azure o ADLS Gen2 input ogni secondo se il file BLOB è disponibile. Se il file BLOB non è disponibile, è presente un backoff esponenziale con un ritardo massimo di 90 secondi.

Gli input in formato CSV richiedono una riga di intestazione per definire i campi per il set di dati e tutti i campi della riga di intestazione devono essere univoci.

> [!NOTE]
> Analisi di flusso di Azure non supporta l'aggiunta di contenuto a un file di BLOB esistente. Analisi di flusso di Azure visualizza ogni file una sola volta e tutte le modifiche apportate al file dopo che il processo ha letto i dati non vengono elaborate. La procedura consigliata consiste nel caricare simultaneamente tutti i dati per un file di BLOB e quindi aggiungere gli altri eventi più nuovi in un nuovo file di BLOB diverso.

Negli scenari in cui molti BLOB vengono aggiunti continuamente e l'analisi di flusso elabora i BLOB Man mano che vengono aggiunti, è possibile che alcuni BLOB vengano ignorati in rari casi a causa della granularità di `BlobLastModifiedTime` . Per ovviare a questo problema, caricare i BLOB almeno a distanza di due secondi. Se questa opzione non è percorribile, è possibile usare Hub eventi per trasmettere grandi volumi di eventi.

### <a name="configure-blob-storage-as-a-stream-input"></a>Configurare l'archiviazione BLOB come input del flusso 

La tabella seguente contiene la descrizione delle proprietà disponibili nella pagina **Nuovo input** del portale di Azure quando si configura l'archiviazione BLOB come input del flusso.

| Proprietà | Descrizione |
| --- | --- |
| **Alias di input** | Nome descrittivo che viene usato nella query del processo per fare riferimento a questo input. |
| **Sottoscrizione** | Scegliere la sottoscrizione in cui esiste la risorsa di archiviazione. | 
| **Account di archiviazione** | Nome dell'account di archiviazione in cui si trovano i file BLOB. |
| **Chiave dell'account di archiviazione** | Chiave privata associata all'account di archiviazione. Questa opzione viene popolata automaticamente in a meno che non si selezioni l'opzione per specificare manualmente le impostazioni. |
| **Contenitore** | I contenitori forniscono un raggruppamento logico per i BLOB. È possibile scegliere il contenitore **Usa esistente** o **Crea nuovo** per creare un nuovo contenitore.|
| **Modello percorso** (facoltativa) | Percorso del file usato per trovare gli oggetti BLOB nel contenitore specificato. Se si desidera leggere i BLOB dalla radice del contenitore, non impostare un modello di percorso. All'interno del percorso è possibile specificare una o più istanze delle tre variabili seguenti: `{date}`, `{time}` o `{partition}`<br/><br/>Esempio 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Esempio 2: `cluster1/logs/{date}`<br/><br/>Il carattere `*` non è un valore consentito per il prefisso del percorso. Sono consentiti solo <a HREF="/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata">Caratteri BLOB di Azure</a> validi. Non includere i nomi di contenitori o di file. |
| **Formato data** (facoltativa) | Formato della data in base al quale vengono organizzati i file, se si usa la variabile date nel percorso. Esempio: `YYYY/MM/DD` <br/><br/> Quando l'input del BLOB ha `{date}` o `{time}` nel percorso, le cartelle vengono esaminate in ordine di tempo crescente.|
| **Formato ora** (facoltativa) |  Formato dell'ora in base al quale vengono organizzati i file, se si usa la variabile time nel percorso. Al momento, l'unico valore supportato è `HH` per le ore. |
| **Chiave di partizione** | Si tratta di un campo facoltativo disponibile solo se il processo è configurato per l'uso del [livello di compatibilità](./stream-analytics-compatibility-level.md) 1,2 o superiore. Se l'input è partizionato da una proprietà, è possibile aggiungere il nome di questa proprietà qui. Viene usato per migliorare le prestazioni della query se include una clausola PARTITION BY o GROUP BY in questa proprietà. Se questo processo usa il livello di compatibilità 1,2 o versione successiva, il valore predefinito di questo campo è "PartitionId". |
| **Conteggio delle partizioni di input** | Questo campo è presente solo quando {Partition} è presente nel modello di percorso. Il valore di questa proprietà è un numero intero >= 1. Laddove {Partition} viene visualizzato in pathPattern, viene usato un numero compreso tra 0 e il valore di questo campo-1. |
| **Formato di serializzazione eventi** | Il formato di serializzazione (JSON, CSV, Avro o [altro (Protobuf, XML, proprietario...)](custom-deserializer.md)) del flusso di dati in ingresso.  Assicurarsi che il formato JSON sia allineato alla specifica e non includa uno 0 iniziale per i numeri decimali. |
| **Encoding** | Per CSV e JSON, l'unico formato di codifica attualmente supportato è UTF-8. |
| **Compressione** | Il tipo di compressione usato per leggere il flusso dei dati in ingresso, ad esempio Nessuno (predefinito), GZip o Deflate. |

Quando i dati provengono da un'origine di archiviazione BLOB, è possibile accedere ai campi di metadati seguenti nella query di Analisi di flusso:

| Proprietà | Descrizione |
| --- | --- |
| **BlobName** |Nome del BLOB di input da cui proviene l'evento. |
| **EventProcessedUtcTime** |Data e ora di elaborazione dell'evento in Analisi di flusso. |
| **BlobLastModifiedUtcTime** |Data e ora dell'ultima modifica del BLOB. |
| **PartitionId** |ID di partizione in base zero per l'adattatore di input. |

Ad esempio, tramite questi campi, è possibile scrivere una query simile alla seguente:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Avvio rapido: Creare un processo di Analisi di flusso tramite il portale di Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/