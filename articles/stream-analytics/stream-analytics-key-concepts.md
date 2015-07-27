<properties 
	pageTitle="Informazioni sui concetti chiave dell&#39;analisi di flusso | Microsoft Azure" 
	description="Informazioni sui concetti fondamentali di un processo di analisi di flusso, inclusi input e output supportati, la configurazione del processo e le metriche." 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="07/01/2015" 
	ms.author="jeffstok" />


# Concetti fondamentali dell'analisi di flusso: guida ai concetti di base di un processo di analisi di flusso 

Analisi di flusso di Azure è un servizio completamente gestito che consente l'elaborazione di eventi complessi con bassa latenza, elevata disponibilità e scalabilità sul flusso di dati nel cloud. Analisi di flusso consente ai clienti di configurare processi di flusso per analizzare i flussi di dati e di condurre operazioni di analisi quasi in tempo reale. In questo articolo vengono illustrati i concetti chiave di un processo di analisi di flusso.

## Cosa si può fare nell'analisi di flusso?
Con l'analisi di flusso è possibile:

- Eseguire l'elaborazione di eventi complessi su flussi di dati ad elevato volume e alta velocità.   
- Raccogliere dati evento da risorse o apparecchiature distribuite globalmente, come infrastrutture energetiche o veicoli connessi. 
- Elaborare dati di telemetria per il monitoraggio e la diagnostica quasi in tempo reale. 
- Acquisizione e archiviazione di eventi in tempo reale per elaborazione successiva

Per altre informazioni, vedere l'articolo relativo all'[introduzione ad Analisi di flusso di Azure](stream-analytics-introduction.md).

Un processo di analisi di flusso include quanto segue: - Uno o più origini di input - Una query su un flusso di dati in ingresso - Una destinazione di output.


## Input

### Flusso dei dati

Ogni definizione di processo di Analisi dei flussi deve contenere almeno un'origine di input del flusso dei dati per uso e trasformazione da parte del processo stesso. [Archiviazione BLOB di Azure](http://azure.microsoft.com/documentation/services/storage/) e [Hub eventi di Azure](http://azure.microsoft.com/services/event-hubs/) sono supportati come origini di input del flusso di dati. Le origini di input di Hub eventi vengono usate per la raccolta di flussi di eventi da diversi dispositivi e servizi, mentre l'archiviazione BLOB può essere usata come origine di input per l'inserimento di grandi quantità di dati. Poiché i BLOB non inviano dati in streaming, i processi di Analisi dei flussi sui BLOB non saranno di natura temporale, a meno che i record nel BLOB non contengano timestamp.

### Dati di riferimento
Analisi dei flussi supporta anche un secondo tipo di origine di input, ovvero i dati di riferimento. Si tratta di dati ausiliari usati per eseguire correlazioni e ricerche e in questo caso si tratta generalmente di dati statici o modificati con scarsa frequenza. [Archiviazione BLOB di Azure](http://azure.microsoft.com/documentation/services/storage/) è l'unica origine di input supportata per i dati di riferimento. Le dimensioni dei BLOB di origine dei dati di riferimento non possono superare i 50 MB.

Per abilitare il supporto per l'aggiornamento dei dati di riferimento, l'utente deve specificare un elenco di BLOB nella configurazione di input usando i token {data} e {ora} nel modello di percorso. Il processo caricherà il BLOB corrispondente in base alla data e all'ora codificate nei nomi BLOB usando il fuso orario UTC.

Se il processo dispone ad esempio di un input di riferimento configurato nel portale con un modello di percorso come /esempio/{data}/{ora}/prodotti.csv, dove il formato della data è "AAAA-MM-GG" e il formato dell'ora è "HH: mm", il processo selezionerà un file denominato /esempio/2015-04-16/17:30/prodotti.csv alle ore 17:30 del 16 aprile 2015 fuso orario UTC, che equivale alle 10:30 su del 16 aprile 2015 con fuso orario PST.


### Serializzazione
Per assicurare il corretto funzionamento delle query, Analisi dei flussi deve riconoscere il formato di serializzazione usato nei flussi di dati in ingresso. I formati attualmente supportati sono JSON, CSV e Avro per il flusso di dati e CSV o JSON per i dati di riferimento.

### Proprietà generate
A seconda del tipo di input usato nel processo, verranno generati alcuni campi aggiuntivi con i metadati degli eventi. Questi campi possono essere sottoposti a query analogamente alle altre colonne di input. Se un evento esistente presenta un campo con lo stesso nome di una delle seguenti proprietà, verrà sovrascritto con i metadati di input.

<table border="1">
	<tr>
		<th></th>
		<th>Proprietà</th>
		<th>Descrizione</th>
	</tr>
	<tr>
		<td rowspan="4" valign="top"><strong>BLOB</strong></td>
		<td>BlobName</td>
		<td>Nome del BLOB di input da cui proviene l'evento.</td>
	</tr>
	<tr>
		<td>EventProcessedUtcTime</td>
		<td>Data e ora di elaborazione del record del BLOB.</td>
	</tr>
	<tr>
		<td>BlobLastModifiedUtcTime</td>
		<td>Data e ora dell'ultima modifica del BLOB.</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>ID di partizione in base zero per l'adattatore di input.</td>
	</tr>
	<tr>
		<td rowspan="3" valign="top"><b>Hub eventi</b></td>
		<td>EventProcessedUtcTime</td>
		<td>Data e ora di elaborazione dell'evento.</td>
	</tr>
	<tr>
		<td>EventEnqueuedUtcTime</td>
		<td>Data e ora di ricezione dell'evento da parte di Hub eventi.</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>ID di partizione in base zero per l'adattatore di input.</td>
	</tr>
</table>

###Partizioni con dati lenti o non di input
Durante la lettura da origini di input con più partizioni, se e una o più partizioni accumulano ritardo o non contengono dati, il processo di streaming deve decidere come gestire la situazione per garantire il flusso di eventi attraverso il sistema. L'impostazione di input "Massimo ritardo di recapito consentito" controlla tale comportamento ed è configurata per impostazione predefinita per attendere i dati per un tempo illimitato. Di conseguenza, i timestamp degli eventi non verranno modificati e il flusso di eventi si baserà sulla partizione di input più lenta e si arresterà se una o più partizioni di input non contengono dati. Ciò risulta utile se i dati vengono distribuiti uniformemente tra le partizioni di input ed è importante garantire uniformità temporale tra gli eventi.

L'utente può anche decidere di attendere solo per un periodo di tempo limitato. L'impostazione "Massimo ritardo di recapito consentito" determina il periodo di tempo, trascorso il quale, il processo continuerà, tralasciando le partizioni di input in ritardo e agendo sugli eventi in base all'impostazione "Azione per gli eventi tardivi", eliminandoli o modificandone i timestamp se i dati arrivano in un secondo momento. Ciò è utile se la latenza è un elemento molto importante e se lo spostamento dei timestamp è tollerato, ma l'input potrebbe non essere distribuito in modo uniforme.

###Partizioni con eventi non ordinati
Quando la query del processo di streaming usa la parola chiave TIMESTAMP BY, l'ordine in cui verranno recapitati gli eventi all'input non è garantito. Alcuni eventi nella stessa partizione di input potrebbero essere in ritardo, il parametro "Massimo disordine consentito all'interno di un input" fa sì che il processo di streaming agisca sugli eventi al di fuori della tolleranza d'ordine in base all'impostazione "Azione per gli eventi tardivi", eliminandoli o modificandone i timestamp.

### Risorse aggiuntive
Per informazioni sulla creazione di origini di input, vedere gli articoli relativi alla [guida per sviluppatori di Hub di eventi di Azure](http://msdn.microsoft.com/library/azure/dn789972.aspx) e all'[uso di Archiviazione BLOB di Azure](../storage/storage-dotnet-how-to-use-blobs.md).



## Query
La logica per filtrare, gestire ed elaborare i dati in ingresso è definita nella query per i processi di analisi di flusso. Le query vengono scritte nel linguaggio di query di Analisi dei flussi, un linguaggio simile a SQL che è sostanzialmente un sottoinsieme della sintassi Transact-SQL standard, con alcune estensioni specifiche per le query temporali.

### Windowing
Le estensioni AWE (Address Windowing Extensions) consentono aggregazioni e calcoli da eseguire su subset di eventi che rientrano in un determinato periodo di tempo. Le funzioni di windowing vengono richiamate tramite l'istruzione **GROUP BY**. Ad esempio, la query seguente conta gli eventi ricevuti al secondo:

	SELECT Count(*) 
	FROM Input1 
	GROUP BY TumblingWindow(second, 1) 

### Passaggi di esecuzione
Per query più complesse, è possibile usare la clausola SQL standard **WITH** per specificare un set di risultati denominato temporaneo. Ad esempio, questa query usa **WITH** per eseguire una trasformazione con due passaggi di esecuzione:
 
	WITH step1 AS ( 
		SELECT Avg(Reading) as avr 
		FROM temperatureInput1 
		GROUP BY Building, TumblingWindow(hour, 1) 
	) 

	SELECT Avg(avr) AS campus_Avg 
	FROM step1 
	GROUP BY TumblingWindow (day, 1) 

Per altre informazioni sul linguaggio di query, vedere l'articolo relativo alle [informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](http://go.microsoft.com/fwlink/?LinkID=513299).

## Output
La destinazione di output è l'ubicazione in cui verranno scritti i risultati del processo di Analisi dei flussi. I risultati vengono scritti continuamente nella destinazione di output man mano che il processo elabora gli eventi di input. Sono supportate le destinazioni di output seguenti:

- Hub eventi di Azure: scegliere Hub eventi come destinazione di output per scenari in cui più pipeline di streaming devono essere combinate insieme, ad esempio per l'invio di comandi ai dispositivi.
- Archiviazione BLOB di Azure: usare un archivio BLOB per l'archiviazione a lungo termine dell'output o per archiviare dati per un'elaborazione successiva.
- Archiviazione tabelle di Azure: Archiviazione tabelle di Azure è un archivio dati strutturato con meno vincoli nello schema. Le entità con schemi e tipi diversi possono essere archiviate nella stessa tabella di Azure. Archivio tabelle di Azure consente di archiviare i dati per il salvataggio permanente e il recupero efficiente. Per altre informazioni, vedere l'articolo relativo all'[introduzione all'archiviazione di Azure](../storage/storage-introduction.md) e [Progettazione di una strategia di partizionamento scalabile per l'archiviazione tabelle di Azure](https://msdn.microsoft.com/library/azure/hh508997.aspx).
- Database SQL di Azure: questa destinazione di output è appropriata per dati di natura relazionale o per applicazioni che dipendono dal contenuto ospitato in un database.

## Unità di streaming ##
Allo scopo di offrire ai clienti un'esperienza di prestazioni prevedibili, Analisi di flusso di Azure usa le unità di streaming per rappresentare le risorse e le funzionalità dedicate all'esecuzione di un processo. Le unità di streaming descrivono la capacità relativa di elaborazione di eventi in base a una misurazione combinata di CPU, memoria e frequenze di lettura e scrittura. Ogni unità di streaming corrisponde a circa 1 MB al secondo di velocità effettiva. Ogni processo di Analisi di flusso di Azure richiede almeno un'unità di streaming, che è l'impostazione predefinita per tutti i processi. Per altre informazioni sulla selezione del numero adatto di unità di streaming per un processo, vedere [Ridimensionare i processi di Analisi di flusso di Azure](stream-analytics-scale-jobs.md)

## Ridimensionare i processi

La metrica % utilizzo unità di streaming definita di seguito è un indicatore della necessità di ridimensionare un processo di Analisi di flusso di Azure. Una % utilizzo unità di streaming elevata può essere il risultato di una finestra di grandi dimensioni in una query, di eventi di grandi dimensioni nell'input, di finestra di tolleranza elementi non in ordine di grandi dimensioni o una combinazione di questi elementi. Il partizionamento della query o la suddivisione della query in più passaggi e l'aggiunta di altre unità di streaming dalla scheda Scalabilità sono strategie per evitare una situazione di questo genere.

Si può osservare un utilizzo delle risorse di base anche senza eventi di input, perché il sistema utilizza una certa quantità di risorse. La quantità di risorse utilizzata dal sistema di può inoltre variare nel tempo.

Per informazioni dettagliate, vedere l'articolo relativo alla [scalabilità dei processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md).


## Monitorare e risolvere i problemi correlati ai processi

### Account di archiviazione per il monitoraggio a livello di area

Per abilitare il monitoraggio dei processi, è necessario designare un account di archiviazione di Azure per il monitoraggio dei dati in ogni area che contiene processi di Analisi dei flussi. Tale account va configurato al momento della creazione del processo.

### Metrica
Le seguenti metriche sono disponibili per il monitoraggio dell'uso e delle prestazioni dei processi di Analisi dei flussi:

- % utilizzo unità di streaming: indicatore della capacità relativa di elaborazione di eventi per uno o più passaggi della query. Se questo indicatore raggiunge l'80% o più, esiste una forte probabilità che l'elaborazione di eventi possa essere ritardata o interrotta.
- Errori: numero di messaggi di errore rilevato da un processo di Analisi dei flussi.
- Eventi di input: quantità di dati ricevuta dal processo di Analisi dei flussi, in termini di conteggio degli eventi.
- Eventi di output: quantità di dati inviata dal processo di Analisi dei flussi alla destinazione di output, in termini di conteggio degli eventi.
- Eventi non ordinati: numero di eventi non ordinati ricevuti che sono stati eliminati o a cui è stato assegnato un timestamp modificato, in base ai criteri non ordinati.
- Errori di conversione dati: numero di errori di conversione dei dati rilevati da un processo di Analisi dei flussi.

### Log delle operazioni
L'approccio migliore per il debug o la risoluzione dei problemi relativi a un processo di Analisi dei flussi è l'uso dei registri operazioni di Azure. I registri operazioni sono accessibili nella sezione **Servizi di gestione** del portale. Per controllare i log di un processo, impostare **Tipo di servizio** su **Analisi dei flussi** e **Nome del servizio** sul nome del processo desiderato.


## Gestire i processi 

### Avviare e arrestare processi
Quando si avvia un processo, viene richiesto di specificare un valore di **Avvio output**, che determina quando il processo verrà avviato restituendo un output. Se la query associata include una finestra, il processo inizierà la selezione di input dalle relative origini all'inizio della durata richiesta della finestra, in modo da produrre il primo evento di output all'ora specificata. Sono disponibili tre opzioni: **Ora avvio processo**, **Personalizzato** e **Ora ultimo arresto**. L'impostazione predefinita è **Ora avvio processo**. Se un processo è stato arrestato temporaneamente, è consigliabile scegliere **Ora ultimo arresto** per il valore **Avvio output** per riprendere il processo dall'ora dell'ultimo output ed evitare perdite di dati. Per l'opzione **Personalizzato** è necessario specificare una data e un'ora. Questa impostazione è utile per specificare la quantità di dati cronologici nelle origini di input da utilizzare o per il prelievo di dati da inserire a partire da un momento specifico.

### Configurare i processi
È possibile modificare le seguenti impostazioni di livello superiore per un processo di Analisi dei flussi:

- **Avvio output**: usare questa impostazione per specificare quando il processo verrà avviato restituendo un output. Se la query associata include una finestra, il processo inizierà la selezione di input dalle relative origini all'inizio della durata richiesta della finestra, in modo da produrre il primo evento di output all'ora specificata. Sono disponibili due opzioni: **Ora avvio processo** e **Personalizzato**. L'impostazione predefinita è **Ora avvio processo**. Per l'opzione **Personalizzato** è necessario specificare una data e un'ora. Questa impostazione è utile per specificare la quantità di dati cronologici nelle origini di input da usare o per il prelievo di dati da inserire a partire da un momento specifico, ad esempio dal momento in cui un processo è stato arrestato. 
- **Criteri non ordinati**: impostazioni per la gestione degli eventi che non arrivano al processo di Analisi dei flussi in modo sequenziale. È possibile designare una soglia di tempo per riordinare gli eventi internamente specificando una finestra di tolleranza, nonché determinare un'azione da intraprendere per gli eventi al di fuori di questa finestra: **Elimina** o **Modifica**. **Rimuovi** consente di eliminare tutti gli eventi ricevuti in disordine, mentre **Regola** consente di cambiare il sistema. Timestamp degli eventi in disordine rispetto al timestamp dell'evento ordinato ricevuto più di recente. 
- **Criteri di recapito tardivo**: durante la lettura da origini di input con più partizioni, se e una o più partizioni accumulano ritardo o non contengono dati, il processo di streaming deve decidere come gestire la situazione per garantire il flusso di eventi attraverso il sistema. L'impostazione di input "Massimo ritardo di recapito consentito" controlla tale comportamento ed è configurata per impostazione predefinita per attendere i dati per un tempo illimitato. Di conseguenza, i timestamp degli eventi non verranno modificati e il flusso di eventi si baserà sulla partizione di input più lenta e si arresterà se una o più partizioni di input non contengono dati. Ciò risulta utile se i dati vengono distribuiti uniformemente tra le partizioni di input ed è importante garantire uniformità temporale tra gli eventi. L'utente può anche decidere di attendere solo per un periodo di tempo limitato. L'impostazione "Massimo ritardo di recapito consentito" determina il periodo di tempo trascorso il quale il processo continuerà, tralasciando le partizioni di input in ritardo e agendo sugli eventi in base all'impostazione "Azione per gli eventi tardivi", eliminandoli o modificandone i timestamp se i dati arrivano in un secondo momento. Ciò è utile se la latenza è un elemento molto importante e se lo spostamento dei timestamp è tollerato, ma l'input potrebbe non essere distribuito in modo uniforme.
- **Impostazioni locali**: usare questa impostazione per specificare le preferenze internazionali per il processo di Analisi dei flussi. Anche se i timestamp dei dati sono indipendenti dalle impostazioni locali, questa impostazione avrà influenza sulle modalità con cui il processo analizzerà, confronterà e ordinerà i dati. Per la versione di anteprima è supportata solo l'impostazione **en-US**.

### Stato

Lo stato dei processi di Analisi dei flussi può essere esaminato nel portale di Azure. I processi in esecuzione possono trovarsi in uno di questi due stati: **In esecuzione** o **Danneggiato**. Di seguito è riportata la definizione di ognuno di questi stati:

- **In esecuzione**: il processo è stato allocato, è in corso l'elaborazione dell'input oppure è in attesa dell'elaborazione dell'input. Se lo stato di un processo è In esecuzione ma non viene generato alcun output, è probabile che la finestra temporale di elaborazione dati sia ampia o che la logica di query sia complessa. È anche possibile che al momento non siano stati inviati dati al processo.
- **Danneggiato**: questo stato indica che in un processo di Analisi dei flussi si sta verificando uno degli errori seguenti: errori di comunicazione input/output, errori di query o errori reversibili di runtime. Per distinguere il tipo di errore che si sta verificando nel processo, visualizzare i registri operazioni.


## Supporto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## Passaggi successivi

A questo punto, si conoscono i concetti fondamentali dell'analisi di flusso ed è possibile provare:

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=July15_HO2-->