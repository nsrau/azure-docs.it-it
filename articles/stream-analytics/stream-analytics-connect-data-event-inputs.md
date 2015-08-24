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
	ms.date="08/13/2015" 
	ms.author="jeffstok"/>

# Creare input di analisi di flusso

## Informazioni sugli input di analisi di flusso
---
Gli input di analisi di flusso vengono definiti come connessione a un'origine dati. L'analisi di flusso si integra perfettamente con le origini di Azure Hub eventi e archivio BLOB all'interno e all'esterno della sottoscrizione processo. Quando i dati vengono inviati a tale origine dati, vengono utilizzati dal processo di analisi di flusso ed elaborati in tempo reale. Gli input si dividono in due tipi distinti: input del flusso dei dati e input dei dati di riferimento.

## Input del flusso dei dati
---
I processi di analisi di flusso devono includere almeno un input del flusso dei dati che deve essere utilizzato e trasformato dal processo stesso. L'archivio BLOB di Azure e Hub eventi di Azure sono supportati come origini di input del flusso dei dati. Gli Hub eventi di Azure vengono usati per raccogliere i flussi di eventi da più dispositivi e servizi, ad esempio i feed attività dei social media, le informazioni sulle quotazioni azionarie o i dati dei sensori. In alternativa, si può usare l'archivio BLOB di Azure come origine di input per l'inserimento di dati in blocco.

## Input dei dati di riferimento
---
L'analisi di flusso supporta un secondo tipo di input, ovvero i dati di riferimento. Si tratta di dati ausiliari usati di solito per eseguire correlazioni e ricerche e in questo caso si tratta generalmente di dati statici o modificati con scarsa frequenza. L'archivio BLOB di Azure attualmente è l'unica origine di input supportata per i dati di riferimento. Le dimensioni dei BLOB di origine dei dati di riferimento non possono superare i 50 MB.

## Creazione di un flusso di input di dati Hub eventi
---
### Panoramica degli Hub eventi
Gli Hub eventi sono un servizio di inserimento di eventi altamente scalabile e rappresentano in genere il modo più comune per l'inserimento di dati nell'analisi di flusso. Gli Hub eventi, insieme all'analisi di flusso, forniscono ai clienti una soluzione end-to-end per l'analisi in tempo reale. Gli Hub eventi consentono ai clienti di inserire gli eventi in Azure in tempo reale. Inoltre, i processi di analisi di flusso possono essere elaborati in tempo reale. Ad esempio, i clienti possono inviare le selezioni effettuate nel Web, le letture dei sensori, gli eventi del registro online per gli Hub eventi e creare processi di analisi di flusso per usare gli Hub eventi come flussi dei dati di input per l'applicazione di filtri, l'aggregazione e il join in tempo reale. Gli Hub eventi possono essere utilizzati anche per dati in uscita. Per altri dettagli sugli Hub eventi, vedere la documentazione [Hub eventi](https://azure.microsoft.com/services/event-hubs/ "Hub eventi").

### Gruppi di utenti
Ogni input dell'Hub eventi per l'analisi di flusso deve essere configurato in modo tale da avere un proprio gruppo di consumer. Quando un processo contiene un self-join o più input, alcuni input potrebbero essere letti da più di un lettore downstream, influendo sul numero di lettori in un singolo gruppo di consumer. Per evitare di superare il limite di Hub eventi di 5 lettori per ogni gruppo di consumer per ciascuna partizione, è consigliabile definire un gruppo di consumer per ogni processo di analisi di flusso. Si noti che è presente anche un limite di 20 gruppi di utenti per Hub eventi. Per informazioni dettagliate, vedere la [Guida alla programmazione di Hub eventi](https://msdn.microsoft.com/library/azure/dn789972.aspx "Guida alla programmazione di Hub eventi").

## Creazione di un flusso di dati di input di Hub eventi
---
### Aggiunta di un Hub eventi come input del flusso di dati  ###

1. Nella scheda degli input del processo di analisi di flusso fare clic su **AGGIUNGI INPUT**, quindi selezionare l'opzione predefinita **Flusso dati** e fare clic sul pulsante a destra.

    ![Immagine1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

2. Selezionare quindi **Hub eventi**.

    ![Immagine6](./media/stream-analytics-connect-data-event-inputs/06-stream-analytics-create-inputs.png)

3. Digitare o selezionare i campi seguenti e fare clic sul pulsante a destra:

    - **Alias di input**: nome descrittivo che verrà usato nella query di processo per fare riferimento a questo input.  
    - **Spazio dei nomi del bus di servizio**: uno spazio dei nomi del bus di servizio è un contenitore per un set di entità di messaggistica. Quando si crea un nuovo Hub eventi, viene inoltre creato uno spazio dei nomi Service Bus.  
    - **Hub eventi**: nome di un input di Hub eventi.  
    - **Nome criterio hub eventi**: criteri di accesso condiviso che possono essere creati nella scheda Configura dell'hub eventi. Ogni criterio di accesso condiviso dispone di un nome, delle autorizzazioni impostate, e di tasti di scelta.  
    - **Gruppo di consumer dell'hub eventi** (facoltativo): gruppo di consumer per inserire i dati dall'Hub eventi. Se non specificato, i processi di analisi del flusso utilizzano il gruppo di utenti predefinito per acquisire dati dall'Hub eventi. È consigliabile usare un gruppo di consumer distinto per ogni processo di analisi di flusso.  

    ![Immagine7](./media/stream-analytics-connect-data-event-inputs/07-stream-analytics-create-inputs.png)

4. Specificare le seguenti impostazioni:

    - **Formato di serializzazione eventi**: per accertarsi che le query funzionino come previsto, l'analisi di flusso deve conoscere il formato di serializzazione (JSON, CSV o Avro) usato per i flussi di dati in entrata.  
    - **Codifica**: al momento UTF-8 è l'unico formato di codifica supportato.  

    ![Immagine8](./media/stream-analytics-connect-data-event-inputs/08-stream-analytics-create-inputs.png)

5. Fare clic sul pulsante con il segno di spunta per completare la procedura guidata e verificare che l'analisi di flusso possa connettersi all'Hub eventi.

## Creazione di un input del flusso di dati dell'archivio BLOB
---
Per gli scenari che dispongono di grandi quantità di dati non strutturati da archiviare nel cloud, l'archivio BLOB offre una soluzione conveniente e scalabile. I dati nell'archivio BLOB vengono generalmente considerati come dati "inattivi" ma possono essere elaborati come flusso di dati dall'analisi di flusso. Uno scenario comune per gli input dell'archivio BLOB con l'analisi di flusso è l'elaborazione dei log, dove i dati di telemetria vengono acquisiti da un sistema e devono essere analizzati ed elaborati per estrarne i dati significativi. È importante notare che il timestamp predefinito degli eventi dell'archivio BLOB nell'analisi di flusso è il timestamp con cui è stato creato il BLOB. Per elaborare i dati come flusso usando un timestamp nel payload dell'evento, è necessario usare la parola chiave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx). Per altre informazioni sull'archivio BLOB, vedere la documentazione [Archivio BLOB](http://azure.microsoft.com/services/storage/blobs/).

### Aggiunta di un archivio BLOB come input del flusso di dati  ###

1. Nella scheda degli input del processo di analisi di flusso fare clic su **AGGIUNGI INPUT**, quindi selezionare l'opzione predefinita **Flusso dati** e fare clic sul pulsante a destra.

    ![Immagine1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

2. Selezionare **Archivio BLOB**, quindi fare clic sul pulsante a destra.

    ![Immagine2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

3. Digitare o selezionare i campi seguenti:

    - **Alias di input**: nome descrittivo che verrà usato nella query di processo per fare riferimento a questo input.  
    - **Account di archiviazione**: se l'account di archiviazione è in una sottoscrizione diversa da quella del processo di flusso, saranno necessari il nome dell'account di archiviazione e la chiave dell'account di archiviazione.  
    - **Contenitore di archiviazione**: i contenitori forniscono un raggruppamento logico per gli oggetti BLOB archiviati nel servizio BLOB di Microsoft Azure. Quando si carica un oggetto BLOB nel servizio BLOB, è necessario specificare un contenitore per il BLOB.  

    ![Immagine3](./media/stream-analytics-connect-data-event-inputs/03-stream-analytics-create-inputs.png)

4. Fare clic sulla casella **Configura impostazioni avanzate** per configurare lo schema prefisso percorso per i BLOB delle letture in un percorso personalizzato. Se questo campo non viene specificato, l'analisi di flusso leggerà tutti i BLOB nel contenitore.

    ![Immagine4](./media/stream-analytics-connect-data-event-inputs/04-stream-analytics-create-inputs.png)

5. Scegliere le impostazioni seguenti:

    - **Formato di serializzazione eventi**: per accertarsi che le query funzionino come previsto, l'analisi di flusso deve conoscere il formato di serializzazione (JSON, CSV o Avro) usato per i flussi di dati in entrata.  
    - **Codifica**: al momento UTF-8 è l'unico formato di codifica supportato.  


    ![Immagine5](./media/stream-analytics-connect-data-event-inputs/05-stream-analytics-create-inputs.png)

6. Fare clic sul pulsante con il segno di spunta per completare la procedura guidata e verificare che l'analisi di flusso possa connettersi all'account di archiviazione BLOB.

## Creazione di dati di riferimento dell'archivio BLOB
---
L'archivio BLOB può essere usato per definire i dati di riferimento per un processo di analisi di flusso. Si tratta di dati statici o a modifica lenta usati per l'esecuzione di ricerche o la correlazione di dati. Il supporto per l'aggiornamento dei dati di riferimento può essere abilitato specificando un modello di percorso nella configurazione di input usando i token {date} e {time}. L'analisi di flusso aggiornerà le definizioni dei dati di riferimento in base a questo modello di percorso. Ad esempio, un modello di `"/sample/{date}/{time}/products.csv"` con un formato data di "AAAA-MM-GG" e un formato ora di "HH:mm" indica all'analisi di flusso di selezionare il BLOB aggiornato `"/sample/2015-04-16/17:30/products.csv"` alle 17:30 del 16 aprile 2015 nel fuso orario UTC.

> [AZURE.NOTE]Attualmente i processi di analisi di flusso cercano i dati di aggiornamento dei BLOB di riferimento solo quando l'ora coincide con l'ora codificata nel nome del BLOB: i processi, ad esempio, cercano /sample/2015-04-16/17:30/products.csv tra le 17:30 e le 17:30:59,999999999 del 16 aprile 2015 fuso orario UTC. Quando l'orologio segna le 17:31, la ricerca di /sample/2015-04-16/17:30/products.csv smette e inizia la ricerca di /sample/2015-04-16/17:31/products.csv.

I BLOB dei dati di riferimento precedenti vengono considerati solo quando il processo viene avviato. In quel momento, il processo sta cercando il BLOB con l'ultima data/ora codificata nel nome con un valore precedente all'ora di inizio del processo, ovvero il BLOB dei dati di riferimento più recente prima dell'ora di inizio del processo. Questa operazione è necessaria per verificare che all'inizio del processo esista un set di dati di riferimento non vuoto. Se non è possibile trovarne uno, il processo non riuscirà e verrà visualizzato un avviso di diagnostica:

### Aggiunta dell'archivio BLOB come dati di riferimento  ###

1. Nella scheda degli input del processo di analisi di flusso fare clic su **AGGIUNGI INPUT**, quindi selezionare **Dati di riferimento** e fare clic sul pulsante a destra.

    ![Immagine9](./media/stream-analytics-connect-data-event-inputs/09-stream-analytics-create-inputs.png)

2.	Digitare o selezionare i campi seguenti:

    - **Alias di input**: nome descrittivo che verrà usato nella query di processo per fare riferimento a questo input.  
    - **Account di archiviazione**: se l'account di archiviazione è in una sottoscrizione diversa da quella del processo di flusso, saranno necessari il nome dell'account di archiviazione e la chiave dell'account di archiviazione.  
    - **Contenitore di archiviazione**: i contenitori forniscono un raggruppamento logico per gli oggetti BLOB archiviati nel servizio BLOB di Microsoft Azure. Quando si carica un oggetto BLOB nel servizio BLOB, è necessario specificare un contenitore per il BLOB.  
    - **Modello percorso**: percorso del file usato per individuare i BLOB nel contenitore specificato. All'interno del percorso è possibile scegliere di specificare una o più istanze delle 2 variabili seguenti: {date}, {time}.  

    ![Immagine10](./media/stream-analytics-connect-data-event-inputs/10-stream-analytics-create-inputs.png)

3. Scegliere le impostazioni seguenti:

    - **Formato di serializzazione eventi**: per accertarsi che le query funzionino come previsto, l'analisi di flusso deve conoscere il formato di serializzazione (JSON, CSV o Avro) usato per i flussi di dati in entrata.  
    - **Codifica**: al momento UTF-8 è l'unico formato di codifica supportato.  

    ![Immagine12](./media/stream-analytics-connect-data-event-inputs/12-stream-analytics-create-inputs.png)

4.	Fare clic sul pulsante con il segno di spunta per completare la procedura guidata e verificare che l'analisi di flusso possa connettersi all'account di archiviazione BLOB.

    ![Immagine11](./media/stream-analytics-connect-data-event-inputs/11-stream-analytics-create-inputs.png)


## Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=August15_HO7-->