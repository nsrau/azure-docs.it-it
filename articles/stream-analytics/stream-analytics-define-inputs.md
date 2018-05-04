---
title: Trasmettere dati come input in Analisi di flusso di Azure
description: Informazioni sulla configurazione di una connessione dati in Analisi di flusso di Azure. Gli input includono un flusso di dati dagli eventi, oltre a dati di riferimento.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/19/2018
ms.openlocfilehash: 5ebf2d1025c8f9469a83a408cb79e3d944a601bc
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Trasmettere dati come input in Analisi di flusso

Analisi di flusso accetta i dati in ingresso da diversi tipi di origini evento. La connessione dati fornita come input a un processo di Analisi di flusso viene definita *input* del processo. 

Analisi di flusso si integra perfettamente con i flussi dei dati di Azure come input da tre tipi di risorse:
- [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/)
- [Hub IoT Azure](https://azure.microsoft.com/services/iot-hub/) 
- [Archivio BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/) 

Queste risorse di input possono trovarsi nella stessa sottoscrizione di Azure del processo di Analisi di flusso o in un'altra sottoscrizione.

## <a name="compare-stream-and-reference-inputs"></a>Confrontare gli input del flusso e di riferimento
Quando sono inviati tramite push a un'origine, i dati vengono utilizzati dal processo di Analisi di flusso ed elaborati in tempo reale. Gli input si dividono in due tipi: input del flusso dei dati e input dei dati di riferimento.

### <a name="data-stream-input"></a>Input del flusso dei dati
Un flusso dei dati è una sequenza non associata di eventi che accadono nel tempo. I processi di Analisi di flusso devono includere almeno un input del flusso dei dati. Gli hub eventi, l'hub IoT e l'archiviazione BLOB sono supportati come origini di input del flusso dei dati. Gli hub eventi vengono usati per raccogliere flussi di eventi da più dispositivi e servizi, ad esempio i feed attività dei social media, le informazioni sulle quotazioni azionarie o i dati di sensori. Gli hub IoT sono ottimizzati per raccogliere dati dai dispositivi connessi in scenari Internet of Things (IoT).  L'archiviazione BLOB può essere usata come origine di input per l'inserimento di dati in blocco come flusso, ad esempio i file di log.  

### <a name="reference-data-input"></a>Input dei dati di riferimento
Analisi di flusso supporta anche l'input noto come *dati di riferimento*. Si tratta di dati ausiliari che sono statici o cambiano lentamente I dati di riferimento vengono usati in genere per l'esecuzione di correlazione e ricerche. È ad esempio possibile unire i dati nell'input del flusso dei dati con quelli inclusi nei dati di riferimento, proprio come si esegue un join SQL per cercare valori statici. L'archiviazione BLOB di Azure è attualmente l'unica origine di input supportata per i dati di riferimento. I BLOB di origine dei dati di riferimento non possono avere una dimensione superiore a 100 MB.

Per informazioni su come creare input dei dati di riferimento, vedere [Uso dei dati di riferimento](stream-analytics-use-reference-data.md).  

### <a name="compression"></a>Compressione
Analisi di flusso supporta la compressione tra tutte le origini di input del flusso dei dati. I tipi di riferimento attualmente supportati sono: Nessuno, GZip e Deflate. Il supporto per la compressione non è disponibile per i dati di riferimento. Se il formato di input è dati Avro compressi, viene gestito in modo trasparente. Non è necessario specificare il tipo di compressione con la serializzazione Avro. 

## <a name="create-or-edit-inputs"></a>Creare o modificare input
Per creare nuovi input ed elencare o modificare input esistenti nel processo di streaming, è possibile usare il portale di Azure:
1. Aprire il [portale di Azure](https://portal.azure.com) per individuare e selezionare il processo di Analisi di flusso.
2. Selezionare l'opzione **Input** sotto l'intestazione **IMPOSTAZIONI**. 
4. La pagina **Input** elenca tutti gli input esistenti. 
5. Nella pagina **Input** selezionare **Aggiungi input del flusso** o **Aggiungi input di riferimento** per aprire la pagina dei dettagli.
6. Selezionare un input esistente per modificare i dettagli e selezionare **Salva** per modificare un input esistente.
7. Selezionare **Test** nella pagina dei dettagli di input per verificare che le opzioni di connessione siano valide e funzionanti. 
8. Fare clic con il pulsante destro del mouse sul nome di un input esistente e selezionare **Campiona dati da input** in base alle esigenze per ulteriori test.

È anche possibile usare [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.streamanalytics/New-AzureRmStreamAnalyticsInput), l'[API .Net](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), l'[API REST](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-input) e [Visual Studio](stream-analytics-tools-for-visual-studio.md) per creare, modificare e testare gli input del processo di Analisi di flusso.

## <a name="stream-data-from-event-hubs"></a>Trasmettere dati da Hub eventi

Hub eventi di Azure offre una tecnologia di inserimento altamente scalabile per eventi di pubblicazione-sottoscrizione. Un hub eventi può raccogliere milioni di eventi al secondo per consentire di elaborare e analizzare enormi quantità di dati generati dalle applicazioni e dai dispositivi connessi. Hub eventi e Analisi di flusso forniscono una soluzione end-to-end per l'analisi in tempo reale. Hub eventi consente di inserire eventi in Azure in tempo reale e i processi di Analisi di flusso sono in grado di elaborare tali eventi in tempo reale. È ad esempio possibile inviare clic sul Web, letture di sensori o eventi di log online agli hub eventi e quindi creare processi di Analisi di flusso per usare gli hub eventi come flussi dei dati di input per il filtraggio, l'aggregazione e la correlazione in tempo reale.

Il timestamp predefinito degli eventi provenienti dagli hub eventi in Analisi di flusso è il timestamp in cui l'evento è giunto nell'hub, ovvero `EventEnqueuedUtcTime`. Per elaborare i dati come flusso usando un timestamp nel payload dell'evento, è necessario usare la parola chiave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

### <a name="consumer-groups"></a>Gruppi di consumer
È necessario configurare uno specifico gruppo di consumer per ogni input degli hub eventi di Analisi di flusso. Quando un processo contiene un self-join o ha più input, è possibile che qualche input venga letto da più lettori downstream. Questa situazione influisce sul numero di lettori in un singolo gruppo di consumer. Per evitare di superare il limite di cinque lettori imposto da Hub eventi per ogni gruppo di consumer di ciascuna partizione, è consigliabile definire un gruppo di consumer per ogni processo di Analisi di flusso. È definito anche un limite di 20 gruppi di consumer per ogni hub eventi. Per altre informazioni, vedere [Guida alla programmazione di Hub eventi](../event-hubs/event-hubs-programming-guide.md).

### <a name="stream-data-from-event-hubs"></a>Trasmettere dati da Hub eventi
La tabella seguente contiene la descrizione delle proprietà disponibili nella pagina **Nuovo input** del portale di Azure per la trasmissione dell'input dei dati da Hub eventi:

| Proprietà | DESCRIZIONE |
| --- | --- |
| **Alias di input** |Nome descrittivo che viene usato nella query del processo per fare riferimento a questo input. |
| **Sottoscrizione** | Scegliere la sottoscrizione in cui esiste la risorsa di Hub eventi. | 
| **Spazio dei nomi dell'hub eventi** | Lo spazio dei nomi dell'hub eventi è un contenitore per un set di entità di messaggistica. Quando si crea un nuovo hub eventi, viene creato anche lo spazio dei nomi. |
| **Nome hub eventi** | Nome dell'hub eventi da usare come input. |
| **Nome criteri hub eventi** | Criteri di accesso condiviso che consentono di accedere all'hub eventi. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. Il valore di questa opzione viene inserito automaticamente, a meno che non si selezioni l'opzione per specificare le impostazioni dell'hub eventi manualmente.|
| **Gruppo di consumer dell'hub eventi** (consigliata) | È vivamente consigliato usare un gruppo di consumer distinto per ogni processo di Analisi di flusso. Questa stringa identifica il gruppo di consumer da usare per l'inserimento di dati dall'hub eventi. Se non è specificato alcun gruppo di consumer, il processo di Analisi di flusso usa il gruppo di consumer $Default.  |
| **Formato di serializzazione eventi** | Formato di serializzazione (JSON, CSV o Avro) del flusso dei dati in ingresso. |
| **Codifica** | L'unico formato di codifica attualmente supportato è UTF-8. |
| **Tipo di compressione eventi** | Il tipo di compressione usato per leggere il flusso dei dati in ingresso, ad esempio Nessuno (predefinito), GZip o Deflate. |

Quando i dati provengono da un input del flusso di Hub eventi, è possibile accedere ai campi di metadati seguenti nella query di Analisi di flusso:

| Proprietà | DESCRIZIONE |
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
> Quando si usa Hub eventi come un endpoint delle route dell'hub IoT, è possibile accedere ai metadati dell'hub IoT usando la funzione [GetMetadataPropertyValue](https://msdn.microsoft.com/en-us/library/azure/mt793845.aspx).
> 

## <a name="stream-data-from-iot-hub"></a>Dati del flusso dall'hub IoT
Hub IoT di Azure è un servizio di inserimento di eventi di pubblicazione-sottoscrizione altamente scalabile ottimizzato per scenari IoT.

Il timestamp predefinito degli eventi provenienti da un hub IoT in Analisi di flusso è il timestamp in cui l'evento è giunto nell'hub IoT, ovvero `EventEnqueuedUtcTime`. Per elaborare i dati come flusso usando un timestamp nel payload dell'evento, è necessario usare la parola chiave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

> [!NOTE]
> Solo i messaggi inviati con una proprietà `DeviceClient` possono essere elaborati.
> 

### <a name="consumer-groups"></a>Gruppi di consumer
È necessario configurare uno specifico gruppo di consumer per ogni input dell'hub IoT di Analisi di flusso. Quando un processo contiene un self-join o ha più input, è possibile che qualche input venga letto da più lettori downstream. Questa situazione influisce sul numero di lettori in un singolo gruppo di consumer. Per evitare di superare il limite di cinque lettori imposto da Hub IoT di Azure per ogni gruppo di consumer di ciascuna partizione, è consigliabile definire un gruppo di consumer per ogni processo di Analisi di flusso.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Configurare un hub IoT come input del flusso dei dati
La tabella seguente contiene la descrizione delle proprietà disponibili nella pagina **Nuovo input** del portale di Azure quando si configura un hub IoT come input del flusso.

| Proprietà | DESCRIZIONE |
| --- | --- |
| **Alias di input** | Nome descrittivo che viene usato nella query del processo per fare riferimento a questo input.|
| **Sottoscrizione** | Scegliere la sottoscrizione in cui esiste la risorsa dell'hub IoT. | 
| **Hub IoT** | Nome dell'hub IoT da usare come input. |
| **Endpoint** | Endpoint per l'hub IoT.|
| **Nome criteri di accesso condiviso** | Criteri di accesso condiviso che consentono di accedere all'hub IoT. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. |
| **Chiave criteri di accesso condiviso** | Chiave di accesso condiviso usata per autorizzare l'accesso all'hub IoT.  Il valore di questa opzione viene inserito automaticamente, a meno che non si selezioni l'opzione per specificare le impostazioni dell'hub IoT manualmente. |
| **Gruppo di consumer** | È vivamente consigliato usare un gruppo di consumer differente per ogni processo di Analisi di flusso. Il gruppo di consumer viene usato per inserire dati dall'hub IoT. Analisi di flusso usa il gruppo di consumer $Default se non diversamente specificato.  |
| **Formato di serializzazione eventi** | Formato di serializzazione (JSON, CSV o Avro) del flusso dei dati in ingresso. |
| **Codifica** | L'unico formato di codifica attualmente supportato è UTF-8. |
| **Tipo di compressione eventi** | Il tipo di compressione usato per leggere il flusso dei dati in ingresso, ad esempio Nessuno (predefinito), GZip o Deflate. |


Quando si usano i dati di flusso provenienti da un hub IoT, è possibile accedere ai campi di metadati seguenti nella query di Analisi di flusso:

| Proprietà | DESCRIZIONE |
| --- | --- |
| **EventProcessedUtcTime** | Data e ora di elaborazione dell'evento. |
| **EventEnqueuedUtcTime** | Data e ora di ricezione dell'evento da parte dell'hub IoT. |
| **PartitionId** | ID di partizione in base zero per l'adattatore di input. |
| **IoTHub.MessageId** | ID usato per correlare la comunicazione bidirezionale nell'hub IoT. |
| **IoTHub.CorrelationId** | ID usato nelle risposte dei messaggi e nei feedback nell'hub IoT. |
| **IoTHub.ConnectionDeviceId** | ID di autenticazione usato per inviare questo messaggio. Questo valore viene riportato nei messaggi servicebound dall'hub IoT. |
| **IoTHub.ConnectionDeviceGenerationId** | ID di generazione del dispositivo autenticato che è stato usato per inviare questo messaggio. Questo valore viene riportato nei messaggi servicebound dall'hub IoT. |
| **IoTHub.EnqueuedTime** | Ora in cui il messaggio è stato ricevuto dall'hub IoT. |
| **IoTHub.StreamId** | Proprietà di evento personalizzato aggiunta dal dispositivo mittente. |


## <a name="stream-data-from-blob-storage"></a>Trasmettere dati dall'archiviazione BLOB
Per gli scenari con grandi quantità di dati non strutturati da archiviare nel cloud, l'archiviazione BLOB di Azure offre una soluzione scalabile e conveniente. I dati nell'archiviazione BLOB sono in genere considerati dati inattivi, ma i dati BLOB possono essere elaborati come flusso dei dati da Analisi di flusso. 

L'elaborazione dei log è uno scenario di uso comune per l'uso degli input di archiviazione BLOB con Analisi di flusso. In questo scenario i file di dati di telemetria sono stati acquisiti da un sistema e devono essere analizzati ed elaborati per estrarre dati significativi.

Il timestamp predefinito degli eventi dell'archiviazione BLOB in Analisi di flusso è il timestamp dell'ultima modifica del BLOB, ovvero `BlobLastModifiedUtcTime`. Per elaborare i dati come flusso usando un timestamp nel payload dell'evento, è necessario usare la parola chiave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

Gli input in formato CSV *richiedono* una riga di intestazione per definire i campi per il set di dati e tutti i campi della riga di intestazione devono essere univoci.

Analisi di flusso attualmente non supporta la deserializzazione di messaggi AVRO generati dall'endpoint personalizzato del contenitore di Archiviazione di Azure dell'hub IoT o di acquisizione di Hub eventi.

> [!NOTE]
> Analisi di flusso di Azure non supporta l'aggiunta di contenuto a un file di BLOB esistente. Analisi di flusso di Azure visualizza ogni file una sola volta e tutte le modifiche apportate al file dopo che il processo ha letto i dati non vengono elaborate. La procedura consigliata consiste nel caricare simultaneamente tutti i dati per un file di BLOB e quindi aggiungere gli altri eventi più nuovi in un nuovo file di BLOB diverso.
> 

### <a name="configure-blob-storage-as-a-stream-input"></a>Configurare l'archiviazione BLOB come input del flusso 

La tabella seguente contiene la descrizione delle proprietà disponibili nella pagina **Nuovo input** del portale di Azure quando si configura l'archiviazione BLOB come input del flusso.

| Proprietà | DESCRIZIONE |
| --- | --- |
| **Alias di input** | Nome descrittivo che viene usato nella query del processo per fare riferimento a questo input. |
| **Sottoscrizione** | Scegliere la sottoscrizione in cui esiste la risorsa dell'hub IoT. | 
| **Account di archiviazione** | Nome dell'account di archiviazione in cui si trovano i file BLOB. |
| **Chiave dell'account di archiviazione** | Chiave privata associata all'account di archiviazione. Il valore di questa opzione viene inserito automaticamente, a meno che non si selezioni l'opzione per specificare le impostazioni dell'archiviazione BLOB manualmente. |
| **Contenitore** | Contenitore per l'input del servizio BLOB. I contenitori forniscono un raggruppamento logico per gli oggetti BLOB archiviati nel servizio BLOB di Microsoft Azure. Quando si carica un oggetto BLOB nel servizio Archiviazione BLOB di Azure, è necessario specificare un contenitore per tale BLOB. È possibile scegliere il contenitore **Usa esistente** o **Crea nuovo** per creare un nuovo contenitore.|
| **Modello percorso** (facoltativa) | Percorso del file usato per trovare gli oggetti BLOB nel contenitore specificato. All'interno del percorso è possibile specificare una o più istanze delle tre variabili seguenti: `{date}`, `{time}` o `{partition}`<br/><br/>Esempio 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Esempio 2: `cluster1/logs/{date}`<br/><br/>Il carattere `*` non è un valore consentito per il prefisso del percorso. Sono consentiti solo <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Caratteri BLOB di Azure</a> validi. |
| **Formato data** (facoltativa) | Formato della data in base al quale vengono organizzati i file, se si usa la variabile date nel percorso. Esempio: `YYYY/MM/DD` |
| **Formato ora** (facoltativa) |  Formato dell'ora in base al quale vengono organizzati i file, se si usa la variabile time nel percorso. Al momento, l'unico valore supportato è `HH` per le ore. |
| **Formato di serializzazione eventi** | Formato di serializzazione (JSON, CSV o Avro) per i flussi dei dati in ingresso. |
| **Encoding** | Per CSV e JSON, l'unico formato di codifica attualmente supportato è UTF-8. |
| **Compressione** | Il tipo di compressione usato per leggere il flusso dei dati in ingresso, ad esempio Nessuno (predefinito), GZip o Deflate. |

Quando i dati provengono da un'origine di archiviazione BLOB, è possibile accedere ai campi di metadati seguenti nella query di Analisi di flusso:

| Proprietà | DESCRIZIONE |
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
Sono state apprese le opzioni di connessione dei dati in Azure per i processi di Analisi di flusso. Per altre informazioni su Analisi di flusso, vedere:

* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
