<properties 
	pageTitle="Creare input di analisi di flusso | Microsoft Azure" 
	description="Informazioni su come effettuare la connessione e configurare le origini di input per le soluzioni di analisi di flusso."
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="08/05/2015" 
	ms.author="jeffstok"/>

# Creare input di analisi di flusso

## Informazioni sugli input di analisi di flusso
---
Gli input di analisi di flusso vengono definiti come connessione a un'origine dati. Quando i dati vengono inviati a tale origine dati, vengono utilizzati dal processo di analisi di flusso ed elaborati in tempo reale. Gli input si dividono in due gruppi distinti: input del flusso dei dati e input dei dati di riferimento. L'analisi di flusso si integra perfettamente con le origini di input Hub eventi e archivio BLOB all'interno e all'esterno della sottoscrizione processo. I formati dati supportati sono Avro, CSV e JSON.

## Input del flusso dei dati
---
A un livello base, le definizioni del processo di analisi di flusso devono includere almeno un'origine di input del flusso dei dati che deve essere utilizzata e trasformata dal processo stesso. L'archivio BLOB di Azure e Hub eventi di Azure sono supportati come origini di input del flusso dei dati. Le origini di input Hub eventi di Azure vengono usate per raccogliere flussi di eventi da più dispositivi e servizi. Esempi di flussi dei dati sono i feed attività dei social media, le informazioni sulle quotazioni azionarie o i dati dei sensori.

In alternativa, si può usare l'archivio BLOB di Azure come origine di input per l'inserimento di dati in blocco. È importante osservare, che, quando si usano i BLOB di Azure, i dati sono inattivi e quindi l'analisi di flusso interpreterà tutti i dati contenuti in un BLOB come se avessero il timestamp di creazione del BLOB stesso, a meno che, ovviamente, i record nel BLOB non contengano i timestamp e non venga usata la parola chiave TIMESTAMP BY.

## Input dei dati di riferimento
---
L'analisi di flusso supporta anche un secondo tipo di origine di input, ovvero i dati di riferimento. Si tratta di dati ausiliari usati di solito per eseguire correlazioni e ricerche e in questo caso si tratta generalmente di dati statici o modificati con scarsa frequenza. L'archivio BLOB di Azure attualmente è l'unica origine di input supportata per i dati di riferimento. Le dimensioni dei BLOB di origine dei dati di riferimento non possono superare i 50 MB.

Il supporto per l'aggiornamento dei dati di riferimento può essere abilitato specificando che un modello di percorso nella configurazione di input usa i token {date} e {time}. Il processo caricherà il BLOB corrispondente in base alla data e all'ora codificate nei nomi BLOB usando il fuso orario UTC. Questa sequenza di BLOB dei dati di riferimento con data e ora codificate è necessaria per garantire la coerenza dei risultati. Se, ad esempio, si verifica un ritardo nell'elaborazione ed è necessario ricaricare un file del BLOB, ci si aspetta di trovare il file non modificato nello stesso percorso, altrimenti potrebbe venire visualizzato un output diverso. Il solo scenario supportato prevede l'aggiunta di nuovi BLOB con la data e ora future codificate nel modello di percorso.

Se il processo dispone, ad esempio, di un input di riferimento configurato nel portale con un modello di percorso come /sample/{date}/{time}/products.csv, dove il formato della data è "AAAA-MM-GG" e il formato dell'ora è "HH: mm", il processo selezionerà un file denominato /sample/2015-04-16/17:30/products.csv alle ore 17:30 del 16 aprile 2015 fuso orario UTC.


> [AZURE.NOTE]Attualmente i processi di analisi di flusso cercano i dati di aggiornamento dei BLOB di riferimento solo quando l'ora coincide con l'ora codificata nel nome del BLOB: i processi, ad esempio, cercano /sample/2015-04-16/17:30/products.csv tra le 17:30 e le 17:30:59,999999999 del 16 aprile 2015 fuso orario UTC. Quando l'orologio segna le 17:31, la ricerca di /sample/2015-04-16/17:30/products.csv smette e inizia la ricerca di /sample/2015-04-16/17:31/products.csv.

I BLOB dei dati di riferimento precedenti vengono considerati solo quando il processo viene avviato. In quel momento, il processo sta cercando il BLOB con l'ultima data/ora codificata nel nome con un valore precedente all'ora di inizio del processo, ovvero il BLOB dei dati di riferimento più recente prima dell'ora di inizio del processo. Questa operazione è necessaria per verificare che all'inizio del processo esista un set di dati di riferimento non vuoto. Se non è possibile trovarne uno, il processo non riuscirà e verrà visualizzato un avviso di diagnostica:

    “Initializing input without a valid reference data blob for UTC time <job start time>.”

 
## Creazione di un input del flusso dei dati
---
Per creare un input del flusso dei dati, andare alla scheda **Input** del processo di analisi di flusso e fare clic su **Aggiungi input** nella parte inferiore della pagina.

![Immagine1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

 Durante la creazione di un input del flusso di dati, l'utente potrà scegliere tra [**Hub eventi**](Creating-an-Event-hub-input-data-stream) o [**Archivio BLOB**](Creating-a Blob-storage-input-data-stream). Selezionare l'opzione appropriata per il tipo di flusso da elaborare.

![Immagine2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

## Creazione di un flusso di input di dati Hub eventi
---
### Panoramica degli Hub eventi
Gli Hub eventi sono un ingestor di evento estremamente scalabile e sono in genere il modo più comune per l'ingresso dei dati nell'analisi flusso. Sono progettati per raccogliere i flussi di eventi da diversi dispositivi e servizi. Gli Hub eventi, insieme all'analisi di flusso, forniscono ai clienti una soluzione end-to-end per l'analisi in tempo reale. Gli Hub eventi consentono ai clienti di inserire gli eventi in Azure in tempo reale. Inoltre, i processi di analisi di flusso possono essere elaborati in tempo reale. Ad esempio, i clienti possono inviare le selezioni effettuate nel Web, le letture dei sensori, gli eventi del registro online per gli Hub eventi e creare processi di analisi di flusso per usare gli Hub eventi come flussi dei dati di input per l'applicazione di filtri, l'aggregazione e il join in tempo reale. Gli Hub eventi possono essere utilizzati anche per dati in uscita. Un potenziale uso degli Hub eventi come output si verifica quando l'output di un processo di analisi di flusso rappresenta l'input di un altro processo di flusso. Per altri dettagli sugli Hub eventi, vedere la documentazione di [Hub eventi](https://azure.microsoft.com/services/event-hubs/ "Hub eventi").

### Gruppi di utenti
Ogni input del processo di analisi di flusso deve essere configurato in modo tale da disporre del proprio gruppo di utenti dell'Hub eventi. Quando un processo contiene self-join o più input, alcuni input potrebbero essere letti da più di un lettore downstream. Ciò determina il superamento del limite di 5 lettori per gruppo di utenti in un singolo gruppo di utenti. In questo caso, la query dovrà essere suddivisa in più query e i risultati intermedi devono essere instradati tramite gli Hub eventi aggiuntivi. Si noti che è presente anche un limite di 20 gruppi di utenti per Hub eventi. Per informazioni dettagliate, vedere [Guida alla programmazione di Hub eventi](https://msdn.microsoft.com/library/azure/dn789972.aspx "Guida alla programmazione di Hub eventi").

## Esempio di creazione di un input Hub eventi nel portale di Azure
---
Di seguito è riportata una procedura dettagliata per configurare un Hub eventi come input. Per iniziare a usare un input Hub eventi, è necessario che l'utente raccolga le informazioni seguenti sull'Hub eventi:

1. Alias di input: nome descrittivo dell'alias di input a cui si farà riferimento nella query del processo.
2. Nome dello spazio dei nomi del bus di servizio. 
3. Nome dell'Hub eventi.
3. Nome criterio Hub eventi.
4. Facoltativo: nome del gruppo di consumer dell'Hub eventi.
	- Il gruppo di utenti per acquisire dati dall'Hub eventi. Se non specificato, i processi di analisi del flusso utilizzano il gruppo di utenti predefinito per acquisire dati dall'Hub eventi. È consigliabile usare un gruppo di consumer distinto per ogni processo di analisi di flusso.
5. Formato di serializzazione utilizzato per il flusso dei dati (Avro, CSV, JSON).

Selezionare prima **AGGIUNGI INPUT** nella pagina Input del processo di analisi di flusso.

![Immagine1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

Selezionare quindi Hub eventi come input.

![Immagine6](./media/stream-analytics-connect-data-event-inputs/06-stream-analytics-create-inputs.png)

Inserire poi le informazioni nei campi per l'Hub eventi, come mostrato di seguito.

![Immagine7](./media/stream-analytics-connect-data-event-inputs/07-stream-analytics-create-inputs.png)

Convalidare quindi la correttezza del formato di serializzazione degli eventi per il flusso dei dati.

![Immagine8](./media/stream-analytics-connect-data-event-inputs/08-stream-analytics-create-inputs.png)

Selezionare quindi **Completato** e l'input del flusso dei dati Hub eventi verrà creato.

## Creazione di un flusso dei dati di input di archiviazione BLOB
---
Per gli scenari che dispongono di grandi quantità di dati non strutturati da archiviare nel cloud, l'archivio BLOB offre una soluzione conveniente e scalabile. Questi dati sono in genere considerati "inattivi". Uno scenario in cui un BLOB può essere utile per un input di flusso dei dati è l'analisi dei log in cui i log sono già stati acquisiti dai sistemi e devono essere analizzati per estrarne i dati significativi. Un altro può essere l'analisi dei dati inattivi del data warehouse. Per altre informazioni sull'archivio BLOB, vedere la documentazione di [Archivio BLOB](http://azure.microsoft.com/services/storage/blobs/).

Di seguito è riportata una procedura dettagliata per configurare l'archivio BLOB come input. Per iniziare a usare un input di archiviazione BLOB di Azure, sono necessarie le informazioni seguenti:

1. Alias di input: nome descrittivo dell'alias di input a cui si farà riferimento nella query del processo.
2. Se l'account di archiviazione è in una sottoscrizione diversa da quella del processo di flusso, saranno necessari il nome dell'account di archiviazione e la chiave dell'account di archiviazione.
3. Nome del contenitore.
4. Prefisso del nome file.
5. Formato di serializzazione utilizzato per i dati (Avro, CSV, JSON).

Nella scheda degli input del processo di analisi di flusso fare clic su **AGGIUNGI INPUT** e quindi selezionare l'opzione predefinita, **Flusso dati**. ![Immagine1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

Selezionare quindi **Archivio BLOB**

![Immagine2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

Inserire poi le informazioni nei campi per l'account di archiviazione, come mostrato di seguito.

![Immagine3](./media/stream-analytics-connect-data-event-inputs/03-stream-analytics-create-inputs.png)

> [AZURE.NOTE]Se si fa clic sulla casella Configura impostazioni avanzate, viene visualizzata un'opzione per specificare lo schema prefisso percorso per i BLOB delle letture in un percorso personalizzato. Se questo campo non viene specificato, l'analisi di flusso leggerà tutti i BLOB nel contenitore.

![Immagine4](./media/stream-analytics-connect-data-event-inputs/04-stream-analytics-create-inputs.png)

Scegliere quindi le impostazioni di serializzazione corrette per i dati. Le opzioni sono JSON, CSV e Avro.

![Immagine5](./media/stream-analytics-connect-data-event-inputs/05-stream-analytics-create-inputs.png)

Selezionare quindi **Completato** e l'input del flusso dei dati di archiviazione BLOB verrà creato.

## Creazione di un input dei dati di riferimento di archiviazione BLOB
---
Per utilizzare le funzionalità dei dati di riferimento, è possibile sfruttare l'archivio BLOB.

Di seguito è riportata una procedura dettagliata per configurare l'archivio BLOB come input dei dati di riferimento. Per iniziare, sono necessarie le informazioni seguenti:

1. Alias di input: nome descrittivo dell'alias di input a cui si farà riferimento nella query del processo.
2. Se l'account di archiviazione è in una sottoscrizione diversa da quella del processo di flusso, saranno necessari il nome dell'account di archiviazione e la chiave dell'account di archiviazione.
3. Nome del contenitore.
4. Prefisso del nome file.
5. Formato di serializzazione utilizzato per i dati (CSV, JSON).
6. Modello di percorso. Percorso del file usato per individuare i BLOB nel contenitore specificato. All'interno del percorso è possibile scegliere di specificare una o più istanze delle 2 variabili seguenti: {date} e {time}.


Nella scheda degli input del processo di analisi di flusso fare clic su **AGGIUNGI INPUT** e quindi selezionare l'opzione predefinita, **Dati di riferimento**.

![Immagine9](./media/stream-analytics-connect-data-event-inputs/09-stream-analytics-create-inputs.png)

Inserire poi le informazioni nei campi per l'archivio BLOB e l'account di archiviazione, come mostrato di seguito.

![Immagine10](./media/stream-analytics-connect-data-event-inputs/10-stream-analytics-create-inputs.png)

Assicurarsi di scorrere verso il basso per specificare lo schema del prefisso per la gerarchia di percorsi contenente il BLOB, oltre al formato dei campi di data e ora.

![Immagine12](./media/stream-analytics-connect-data-event-inputs/12-stream-analytics-create-inputs.png)

Scegliere ora le impostazioni di serializzazione corrette per i dati. Le opzioni sono JSON, CSV e Avro.

![Immagine11](./media/stream-analytics-connect-data-event-inputs/11-stream-analytics-create-inputs.png)

Selezionare quindi **Completato** e l'input dei dati di riferimento di archiviazione BLOB verrà creato.


## Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=August15_HO6-->