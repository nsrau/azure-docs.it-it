<properties
	pageTitle="Connessione dati: input di flussi di dati da un flusso di eventi | Microsoft Azure"
	description="Informazioni sulla configurazione di una connessione dati ad Analisi di flusso denominata 'input'. Gli input includono un flusso di dati dagli eventi, oltre a dati di riferimento."
	keywords="flusso di dati, connessione dati, flusso di eventi"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="07/13/2016"
	ms.author="jeffstok"/>

# Connessione dati: informazioni sugli input del flusso di dati dagli eventi ad Analisi di flusso

La connessione dati ad Analisi di flusso è un flusso di dati di eventi da un'origine dati. Questo viene detto "input". Analisi di flusso si integra perfettamente con origini di flussi di dati di Azure come hub eventi, hub IoT e archivi BLOB appartenenti alla stessa sottoscrizione di Azure del processo di analisi o a una sottoscrizione diversa.

## Tipi di input di dati: flusso di dati e dati di riferimento
Quando i dati vengono inviati a un'origine dati, vengono usati dal processo di Analisi di flusso ed elaborati in tempo reale. Gli input si dividono in due tipi distinti: input del flusso dei dati e input dei dati di riferimento.

### Input del flusso dei dati
Un flusso di dati è una sequenza non associata di eventi che si verificano nel tempo. I processi di analisi di flusso devono includere almeno un input del flusso dei dati che deve essere utilizzato e trasformato dal processo stesso. L'archivio BLOB, gli Hub eventi e gli hub IoT sono supportati come origini di input del flusso dei dati. Gli Hub eventi vengono usati per raccogliere i flussi di eventi da più dispositivi e servizi, ad esempio i feed attività dei social media, le informazioni sulle quotazioni azionarie o i dati dei sensori. Gli hub IoT sono ottimizzati per raccogliere dati dai dispositivi connessi in scenari Internet of Things (IoT). Si può usare l'archivio BLOB come origine di input per l'inserimento di dati in blocco come flusso.

### Dati di riferimento
L'analisi di flusso supporta un secondo tipo di input, ovvero i dati di riferimento. Si tratta di dati ausiliari, che sono statici o cambiano lentamente, usati di solito per eseguire correlazioni e ricerche. L'archivio BLOB di Azure attualmente è l'unica origine di input supportata per i dati di riferimento. Le dimensioni dei BLOB di origine dei dati di riferimento non possono superare i 100 MB. Per informazioni su come creare input dei dati di riferimento, vedere [Usare i dati di riferimento](stream-analytics-use-reference-data.md)

## Creare un input del flusso di dati con un hub eventi

Gli [hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/) sono un ingestor eventi di pubblicazione-sottoscrizione altamente scalabile. Può raccogliere milioni di eventi al secondo in modo che sia possibile elaborare e analizzare enormi quantità di dati generati dalle applicazioni e dai dispositivi connessi. È uno degli input più usati per l'analisi di flusso. Gli Hub eventi, insieme all'analisi di flusso, forniscono ai clienti una soluzione end-to-end per l'analisi in tempo reale. Gli Hub eventi consentono ai clienti di inserire gli eventi in Azure in tempo reale. I processi di analisi di flusso possono anche essere elaborati in tempo reale. Ad esempio, i clienti possono inviare le selezioni effettuate nel Web, le letture dei sensori, gli eventi del registro online per gli Hub eventi e creare processi di analisi di flusso per usare gli Hub eventi come flussi dei dati di input per l'applicazione di filtri, l'aggregazione e la correlazione in tempo reale.

È importante notare che il timestamp predefinito degli eventi proveniente dagli Hub eventi nell'analisi di flusso è il timestamp con cui l'evento è giunto nell'Hub eventi, cioè EventEnqueuedUtcTime. Per elaborare i dati come flusso usando un timestamp nel payload dell'evento, è necessario usare la parola chiave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

### Gruppi di utenti

Ogni input dell'Hub eventi per l'analisi di flusso deve essere configurato in modo tale da avere un proprio gruppo di consumer. Quando un processo contiene un self-join o più input, alcuni input potrebbero essere letti da più di un lettore downstream, influendo sul numero di lettori in un singolo gruppo di consumer. Per evitare di superare il limite di Hub eventi di 5 lettori per ogni gruppo di consumer per ciascuna partizione, è consigliabile definire un gruppo di consumer per ogni processo di analisi di flusso. Si noti che è presente anche un limite di 20 gruppi di utenti per Hub eventi. Per informazioni dettagliate, vedere la [Guida alla programmazione di Hub eventi](../event-hubs/event-hubs-programming-guide.md).

### Configurare l'hub eventi come flusso di dati di input

La tabella seguente illustra ciascuna proprietà nella scheda di input dell'Hub eventi con la relativa descrizione:

| NOME PROPRIETÀ | DESCRIZIONE |
|------|------|
| Alias di input | Nome descrittivo che verrà usato nella query di processo per fare riferimento a questo input |
| Spazio dei nomi del bus di servizio | Uno spazio dei nomi Service Bus è un contenitore per un set di entità di messaggistica. Quando si crea un nuovo Hub eventi, viene inoltre creato uno spazio dei nomi Service Bus. |
| Hub eventi | Nome dell'input dell'hub eventi. |
| Nome criterio hub eventi | Criteri di accesso condiviso che possono essere creati nella scheda Configura dell'hub eventi. Ogni criterio di accesso condiviso dispone di un nome, delle autorizzazioni impostate, e di tasti di scelta. |
| Chiave criterio hub eventi | Chiave di accesso condivisa usata per autenticare l'accesso allo spazio dei nomi del bus di servizio. |
| Gruppo di consumer dell'hub eventi | Il gruppo di utenti per acquisire dati dall'Hub eventi. Se non specificato, i processi di analisi del flusso utilizzano il gruppo di utenti predefinito per acquisire dati dall'Hub eventi. È consigliabile usare un gruppo di consumer distinto per ogni processo di analisi di flusso. |
| Formato di serializzazione eventi | Per accertarsi che le query funzionino come previsto, l'analisi di flusso deve conoscere il formato di serializzazione (JSON, CSV o Avro) usato per i flussi di dati in entrata. |
| Codifica | Al momento UTF-8 è l'unico formato di codifica supportato. |

Quando i dati provengono da un'origine di Hub eventi, è possibile accedere ai pochi campi di metadati nella query di analisi di flusso. La tabella seguente elenca i campi e la relativa descrizione.

| PROPRIETÀ | DESCRIZIONE |
|------|------|
| EventProcessedUtcTime | Data e ora di elaborazione dell'evento in Analisi di flusso. |
| EventEnqueuedUtcTime | Data e ora di ricezione dell'evento da parte di Hub eventi. |
| PartitionId | ID di partizione in base zero per l'adattatore di input. |

Ad esempio, è possibile scrivere una query come la seguente:

````
SELECT
	EventProcessedUtcTime,
	EventEnqueuedUtcTime,
	PartitionId
FROM Input
````

## Creare un input del flusso di dati Hub IoT

Hub IoT di Azure è un servizio di inserimento di eventi di pubblicazione-sottoscrizione altamente scalabile ottimizzato per scenari IoT. È importante notare che il timestamp predefinito degli eventi proveniente dagli hub IoT nell'analisi di flusso è il timestamp con cui l'evento è giunto nell'hub IoT, cioè EventEnqueuedUtcTime. Per elaborare i dati come flusso usando un timestamp nel payload dell'evento, è necessario usare la parola chiave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

> [AZURE.NOTE] È possibile elaborare solo i messaggi inviati con una proprietà DeviceClient.

### Gruppi di utenti

Ogni input dell'hub IoT per l'analisi di flusso deve essere configurato in modo tale da avere un proprio gruppo di consumer. Quando un processo contiene un self-join o più input, alcuni input potrebbero essere letti da più di un lettore downstream, influendo sul numero di lettori in un singolo gruppo di consumer. Per evitare di superare il limite di hub IoT di 5 lettori per ogni gruppo di consumer per ciascuna partizione, è consigliabile definire un gruppo di consumer per ogni processo di analisi di flusso.

### Configurare Hub IoT come input del flusso di dati

La tabella seguente illustra ciascuna proprietà nella scheda di input dell’hub IoT con la relativa descrizione:

| NOME PROPRIETÀ | DESCRIZIONE |
|------|------|
| Alias di input | Nome descrittivo che verrà usato nella query di processo per fare riferimento a questo input. |
| Hub IoT | Un hub IoT è un contenitore per un set di entità di messaggistica. |
| Endpoint | Il nome dell'endpoint dell’hub IoT. |
| Nome del criterio di accesso condiviso | Il criterio di accesso condiviso per consentire l'accesso all'hub IoT. Ogni criterio di accesso condiviso dispone di un nome, delle autorizzazioni impostate, e di tasti di scelta. |
| Chiave del criterio di accesso condiviso | Chiave di accesso condivisa usata per autenticare l'accesso all’hub IoT. |
| Gruppo di consumer (facoltativo) | Il gruppo di utenti per acquisire dati dall'hub IoT. Se non specificato, i processi di analisi di flusso utilizzano il gruppo di utenti predefinito per acquisire dati dall'hub IoT. È consigliabile usare un gruppo di consumer distinto per ogni processo di analisi di flusso. |
| Formato di serializzazione eventi | Per accertarsi che le query funzionino come previsto, l'analisi di flusso deve conoscere il formato di serializzazione (JSON, CSV o Avro) usato per i flussi di dati in entrata. |
| Codifica | Al momento UTF-8 è l'unico formato di codifica supportato. |

Quando i dati provengono da un'origine di hub IoT, è possibile accedere ai pochi campi di metadati nella query di analisi di flusso. La tabella seguente elenca i campi e la relativa descrizione.

| PROPRIETÀ | DESCRIZIONE |
|------|------|
| EventProcessedUtcTime | Data e ora di elaborazione dell'evento. |
| EventEnqueuedUtcTime | Data e ora di ricezione dell'evento da parte dell'hub IoT. |
| PartitionId | ID di partizione in base zero per l'adattatore di input. |
| IoTHub.MessageId | Utilizzato per correlare la comunicazione bidirezionale nell’hub IoT. |
| IoTHub.CorrelationId | Utilizzato nelle risposte dei messaggi e nei feedback nell'hub IoT. |
| IoTHub.ConnectionDeviceId | L’ID utente autenticato utilizzato per inviare questo messaggio, indicato nei messaggi servicebound dall'hub IoT. |
| IoTHub.ConnectionDeviceGenerationId | Il generationID del dispositivo autenticato utilizzato per inviare questo messaggio, indicato nei messaggi servicebound dall'hub IoT. |
| IoTHub.EnqueuedTime | Ora di ricezione del messaggio da parte dell'hub IoT. |
| IoTHub.StreamId | Proprietà di evento personalizzato aggiunta dal dispositivo mittente. |

## Creare un input del flusso di dati dell'archivio BLOB

Per gli scenari che dispongono di grandi quantità di dati non strutturati da archiviare nel cloud, l'archivio BLOB offre una soluzione conveniente e scalabile. I dati nell'[archivio BLOB](https://azure.microsoft.com/services/storage/blobs/) vengono generalmente considerati come dati "inattivi", ma possono essere elaborati come flusso di dati dall'analisi di flusso. Uno scenario comune per gli input dell'archivio BLOB con l'analisi di flusso è l'elaborazione dei log, dove i dati di telemetria vengono acquisiti da un sistema e devono essere analizzati ed elaborati per estrarne i dati significativi.

È importante notare che il timestamp predefinito degli eventi dell'archivio BLOB nell'analisi di flusso sia il timestamp con cui è stato modificato l'ultima volta il BLOB, e cioè *BlobLastModifiedUtcTime*. Per elaborare i dati come flusso usando un timestamp nel payload dell'evento, è necessario usare la parola chiave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

Si noti inoltre che gli input in formato CSV **richiedono** una riga di intestazione per definire i campi per il set di dati. Altri campi di righe di intestazione devono essere tutti **univoci**.

> [AZURE.NOTE] Analisi di flusso non supporta l'aggiunta di contenuto a un BLOB esistente. Analisi di flusso si limita a visualizzare un BLOB una sola volta. Tutte le modifiche apportate successivamente a questa lettura non verranno elaborate. La procedura consigliata consiste nel caricare tutti i dati in una sola volta e non aggiungere altri eventi all'archivio BLOB.

La tabella seguente illustra ciascuna proprietà nella scheda di input dell'archivio BLOB con la relativa descrizione:

<table>
<tbody>
<tr>
<td>NOME PROPRIETÀ</td>
<td>DESCRIZIONE</td>
</tr>
<tr>
<td>Alias di input</td>
<td>Nome descrittivo che verrà usato nella query di processo per fare riferimento a questo input.</td>
</tr>
<tr>
<td>Account di archiviazione</td>
<td>Nome dell'account di archiviazione in cui si trovano i file BLOB.</td>
</tr>
<tr>
<td>Chiave dell'account di archiviazione</td>
<td>Chiave privata associata all'account di archiviazione.</td>
</tr>
<tr>
<td>Contenitore di archiviazione
</td>
<td>I contenitori forniscono un raggruppamento logico per gli oggetti BLOB archiviati nel servizio BLOB di Microsoft Azure. Quando si carica un oggetto BLOB nel servizio BLOB, è necessario specificare un contenitore per il BLOB.</td>
</tr>
<tr>
<td>Schema prefisso percorso [facoltativo]</td>
<td>Percorso del file usato per individuare i BLOB nel contenitore specificato. All'interno del percorso è possibile scegliere di specificare una o più istanze delle 3 variabili seguenti:<BR>{date}, {time},<BR>{partition}<BR>Esempio 1: cluster1/logs/{date}/{time}/{partition}<BR>Esempio 2: cluster1/logs/{date}<P>Si noti che "*" non è un valore consentito per pathprefix. Sono consentiti solo <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Caratteri BLOB di Azure</a> validi.</td>
</tr>
<tr>
<td>Formato data [facoltativo]</td>
<td>Se nel percorso di prefisso viene usato il token di data, è possibile selezionare il formato della data in cui sono organizzati i file. Esempio: AAAA/MM/GG</td>
</tr>
<tr>
<td>Formato ora [facoltativo]</td>
<td>Se nel percorso di prefisso viene usato il token dell'ora, specificare il formato dell'ora in cui sono organizzati i file. Al momento, l'unico valore supportato è HH.</td>
</tr>
<tr>
<td>Formato di serializzazione eventi</td>
<td>Per accertarsi che le query funzionino come previsto, l'analisi di flusso deve conoscere il formato di serializzazione (JSON, CSV o Avro) usato per i flussi di dati in entrata.</td>
</tr>
<tr>
<td>Codifica</td>
<td>Al momento UTF-8 è l'unico formato di codifica supportato per i formati CSV e JSON.</td>
</tr>
<tr>
<td>Delimitatore</td>
<td>Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale.</td>
</tr>
</tbody>
</table>

Quando i dati provengono da un'origine di archivio BLOB, è possibile accedere ai pochi campi di metadati nella query di analisi di flusso. La tabella seguente elenca i campi e la relativa descrizione.

| PROPRIETÀ | DESCRIZIONE |
|------|------|
| BlobName | Nome del BLOB di input da cui proviene l'evento. |
| EventProcessedUtcTime | Data e ora di elaborazione dell'evento in Analisi di flusso. |
| BlobLastModifiedUtcTime | Data e ora dell'ultima modifica del BLOB. |
| PartitionId | ID di partizione in base zero per l'adattatore di input. |

Ad esempio, è possibile scrivere una query come la seguente:

````
SELECT
	BlobName,
	EventProcessedUtcTime,
	BlobLastModifiedUtcTime
FROM Input
````


## Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=AzureStreamAnalytics)

## Passaggi successivi
Sono state apprese le opzioni di connessione dei dati in Azure per i processi di Analisi di flusso. Per altre informazioni su Analisi di flusso, vedere:

- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!---HONumber=AcomDC_0713_2016-->