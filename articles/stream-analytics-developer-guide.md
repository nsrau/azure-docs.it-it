<properties 
	pageTitle="Guida per gli sviluppatori di Analisi dei flussi | Azure" 
	description="Informazioni su come sviluppare applicazioni Analisi dei flussi di Azure" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="2/17/2015" 
	ms.author="jgao"/>


# Guida per gli sviluppatori di Analisi dei flussi di Azure 

[La presente documentazione non è definitiva ed è soggetta a modifiche nelle versioni future.] 

Analisi dei flussi di Azure è un servizio completamente gestito che consente l'elaborazione di eventi complessi con bassa latenza, elevata disponibilità e scalabilità per lo streaming di dati nel cloud. Nella versione di anteprima, Analisi dei flussi consente ai clienti di impostare processi di streaming per analizzare flussi di dati nonché di condurre operazioni di analisi quasi in tempo reale.  

Scenari di destinazione per Analisi dei flussi:

- Elaborazione di eventi complessi su dati ad elevato volume e alta velocità.   
- Raccolta di dati sugli eventi da risorse o apparecchiature distribuite globalmente, quali infrastrutture energetiche o veicoli connessi 
- Elaborazione di dati di telemetria per monitoraggio e diagnostica quasi in tempo reale 
- Acquisizione e archiviazione di eventi in tempo reale per elaborazione successiva

Per altre informazioni, vedere [Introduzione ad Analisi dei flussi di Azure][stream.analytics.introduction]. 

I processi di Analisi dei flussi sono definibili come una o più origini di input, una query sui dati di flusso in ingresso e una destinazione di output.  


##Contenuto dell'articolo

+ [Input](#inputs) 
+ [Query](#query)
+ [Output](#output)
+ [Ridimensionare i processi](#scale)
+ [Monitorare e risolvere i problemi correlati ai processi](#monitor)
+ [Gestire i processi](#manage)
+ [Passaggi successivi](#nextsteps)



##<a name="inputs"></a>Input

### Flusso dei dati

Ogni definizione di processo di Analisi dei flussi deve contenere almeno un'origine di input del flusso dei dati per uso e trasformazione da parte del processo stesso.  [Archiviazione BLOB di Azure][azure.blob.storage] e [Hub di notifica del bus di servizio di Azure][azure.event.hubs] sono supportati come origini di input del flusso dei dati.  Le origini di input dell'hub eventi vengono usate per la raccolta di flussi di eventi da diversi dispositivi e servizi, mentre l'archiviazione BLOB può essere usata come origine di input per l'inserimento di grandi quantità di dati.  Poiché i BLOB non inviano dati in streaming, i processi di Analisi dei flussi sui BLOB non saranno di natura temporale, a meno che i record nel BLOB non contengano timestamp.

### Dati di riferimento

Analisi dei flussi supporta anche un secondo tipo di origine di input, ovvero i dati di riferimento.  Si tratta di dati ausiliari usati per eseguire correlazioni e ricerche e in questo caso si tratta generalmente di dati statici o modificati con scarsa frequenza.  Nella versione di anteprima l'archiviazione BLOB è l'unica origine di input supportata per i dati di riferimento.

### Serializzazione
Per assicurare il corretto funzionamento delle query, Analisi dei flussi deve riconoscere il formato di serializzazione usato nei flussi di dati in ingresso. I formati supportati attualmente sono JSON, CSV e Avro per lo streaming dei dati e CSV per i dati di riferimento.

### Proprietà generate
A seconda del tipo di input usato nel processo, verranno generati alcuni campi aggiuntivi con i metadati degli eventi.  Questi campi possono essere sottoposti a query analogamente alle altre colonne di input.  Se un evento esistente presenta un campo con lo stesso nome di una delle seguenti proprietà, verrà sovrascritto con i metadati di input.

<table border="1">
	<tr>
		<th></th>
		<th>Proprietà</th>
		<th>Descrizione</th>
	</tr>
	<tr>
		<td rowspan="4" valign="top"><strong>BLOB</strong></td>
		<td>BlobName</td>
		<td>Il nome del BLOB di input da cui proviene l'evento</td>
	</tr>
	<tr>
		<td>EventProcessedUtcTime</td>
		<td>Data e ora di elaborazione del record del BLOB</td>
	</tr>
	<tr>
		<td>BlobLastModifiedUtcTime</td>
		<td>Data e ora dell'ultima modifica del BLOB</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>ID di partizione in base zero per l'adattatore di input</td>
	</tr>
	<tr>
		<td rowspan="3" valign="top"><b>Hub eventi</b></td>
		<td>EventProcessedUtcTime</td>
		<td>Data e ora di elaborazione dell'evento</td>
	</tr>
	<tr>
		<td>EventEnqueuedUtcTime</td>
		<td>Data e ora di ricezione dell'evento da parte dell'hub eventi</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>ID di partizione in base zero per l'adattatore di input</td>
	</tr>
</table>



###Risorse aggiuntive
Per informazioni sulla creazione di origini di input, vedere [Guida per gli sviluppatori di Hub eventi di Azure][azure.event.hubs.developer.guide] e [Usare l'archiviazione BLOB di Azure][azure.blob.storage.use].  



##<a name="query"></a>Query
La logica per filtrare, gestire ed elaborare i dati in ingresso è definita nella query per i processi di Analisi dei flussi.  Le query vengono scritte usando il linguaggio di query di Analisi dei flussi, un linguaggio simile a SQL che è sostanzialmente un sottoinsieme della sintassi T-SQL standard, con alcune estensioni specifiche per le query temporali.

###Windowing
Le estensioni AWE (Address Windowing Extensions) consentono aggregazioni e calcoli da eseguire su subset di eventi che rientrano in un determinato periodo di tempo. Le funzioni di windowing vengono richiamate tramite l'istruzione GROUP BY. Ad esempio, la query seguente conta gli eventi ricevuti al secondo: 

	SELECT Count(*) 
	FROM Input1 
	GROUP BY TumblingWindow(second, 1) 

###Passaggi di esecuzione
Per query più complesse, è possibile usare la clausola SQL standard WITH per specificare un set di risultati denominato temporaneo.  Ad esempio, questa query usa WITH per eseguire una trasformazione con due passaggi di esecuzione:
 
	WITH step1 AS ( 
		SELECT Avg(Reading) as avr 
		FROM temperatureInput1 
		GROUP BY Building, TumblingWindow(hour, 1) 
	) 

	SELECT Avg(avr) AS campus_Avg 
	FROM step1 
	GROUP BY TumblingWindow (day, 1) 

Per altre informazioni sul linguaggio di query, vedere [Informazioni di riferimento sul linguaggio di Query di Analisi dei flussi di Azure][stream.analytics.query.language.reference]. 

##<a name="output"></a>Output
L'origine dell'output è l'ubicazione in cui verranno scritti i risultati del processo di Analisi dei flussi. I risultati vengono scritti continuamente nell'origine dell'output man mano che il processo elabora gli eventi di input.  Sono supportate le origini di output seguenti:

- Hub eventi del bus di servizio di Azure: scegliere Hub eventi come origine di output per scenari in cui più pipeline di streaming devono essere combinate insieme, ad esempio per l'invio di comandi ai dispositivi.
- BLOB di archiviazione di Azure: usare un archivio BLOB per l'archiviazione a lungo termine dell'output o per archiviare dati per un'elaborazione successiva.
- Database SQL di Azure: Questa origine di output è adatta per dati di natura relazionale o per applicazioni che dipendono dal contenuto ospitato in un database.


##<a name="scale"></a>Ridimensionare i processi

Un processo di Analisi dei flussi può essere ridimensionato mediante la configurazione di unità di streaming, che definiscono la quantità di potenza di elaborazione assegnata ai processi. Ogni unità di streaming corrisponde a circa 1 MB al secondo di velocità effettiva. Ogni sottoscrizione possiede una quota pari a 12 unità di streaming per ogni area da allocare tra i processi in tale area.

Per informazioni dettagliate, vedere [Ridimensionare i processi di Analisi dei flussi di Azure][stream.analytics.scale.jobs].


##<a name="monitor"></a>Monitorare e risolvere i problemi correlati ai processi

###Account di archiviazione per il monitoraggio a livello di area

Per abilitare il monitoraggio dei processi, è necessario designare un account di archiviazione di Azure per il monitoraggio dei dati in ogni area che contiene processi di Analisi dei flussi.  Tale account va configurato al momento della creazione del processo.  

###Metrica
Le seguenti metriche sono disponibili per il monitoraggio dell'uso e delle prestazioni dei processi di Analisi dei flussi:

- Velocità effettiva in ingresso: quantità di dati ricevuta dal processo di Analisi dei flussi, in termini di conteggio degli eventi.
- Velocità effettiva in uscita: quantità di dati inviata dal processo di Analisi dei flussi all'origine di output, in termini di conteggio degli eventi.
- Conteggio errori: numero di messaggi di errore rilevato da un processo di Analisi dei flussi.

###Log delle operazioni
L'approccio migliore per il debug o la risoluzione dei problemi relativi a un processo di Analisi dei flussi è l'uso dei log operazioni di Azure.  I log operazioni sono accessibili nella sezione Servizi di gestione del portale.  Per controllare i log di un processo, impostare il tipo di servizio su "Analisi dei flussi" e il nome del servizio sul nome del processo desiderato.


##<a name="manage"></a>Gestire i processi 

###Avviare e arrestare i processi
Nella versione di anteprima di Analisi dei flussi, in seguito all'arresto di un processo, non viene mantenuto alcuno stato sugli ultimi eventi usati dal processo.  Di conseguenza, il riavvio di un processo interrotto può generare eventi eliminati o dati duplicati.  Se è necessario arrestare temporaneamente un processo, la procedura consigliata è esaminare l'output e usare l'ora di inserimento dell'ultimo record per un'approssimazione del momento in cui il processo si è arrestato.  Specificare quindi questo orario nell'impostazione per l'avvio dell'output, nella scheda Configura, prima del riavvio del processo.
Si tratta di una limitazione temporanea, poiché l'abilitazione della possibilità di avviare e arrestare processi senza perdita di dati ha al momento una priorità molto alta tra le correzioni da apportare in futuro.  

###Configurare i processi
È possibile modificare le seguenti impostazioni di livello superiore per un processo di Analisi dei flussi:

- Avvio output: specifica quando questo processo inizierà a produrre output come risultato. Se la query associata include una finestra, il processo inizierà la selezione di input dalle relative origini all'inizio della durata richiesta della finestra, in modo da produrre il primo evento di output all'ora specificata. Sono disponibili due opzioni, Ora inizio processo e Personalizzata. L'impostazione predefinita è l'ora di inizio del processo. Per l'opzione personalizzata, è necessario specificare una data e un'ora. Questa impostazione è utile per specificare la quantità di dati cronologici nelle origini di input da usare o per il prelievo di dati da inserire a partire da un momento specifico, ad esempio dal momento in cui un processo è stato arrestato. 
- Criteri mancato ordinamento: impostazioni per la gestione degli eventi che non arrivano al processo di Analisi dei flussi in modo sequenziale. È possibile designare una soglia di tempo per riordinare gli eventi internamente specificando una finestra di tolleranza, nonché determinare un'azione da intraprendere per gli eventi al di fuori di questa finestra. Tale azione può consistere nell'eliminazione o nella modifica.  L'eliminazione rimuoverà tutti gli eventi senza ordine ricevuti, mentre la modifica modificherà il System.Timestamp degli eventi non ordinati impostandolo sul timestamp dell'evento ordinato ricevuto più recente.  
- Impostazioni locali: Usare questa impostazione per specificare le preferenze internazionali per il processo di Analisi dei flussi. Anche se i timestamp dei dati sono indipendenti dalle impostazioni locali, questa impostazione avrà influenza sulle modalità con cui il processo analizzerà, confronterà e ordinerà i dati. Per la versione di anteprima è supportata solo l'impostazione en-US.


##<a name="support"></a>Supporto
Per ulteriore supporto, vedere il [forum dedicato ad Analisi dei flussi di Azure][stream.analytics.forum]. 


##<a name="nextsteps"></a>Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure][stream.analytics.introduction]
- [Introduzione all'uso di Analisi dei flussi di Azure][stream.analytics.get.started]
- [Ridimensionare i processi di Analisi dei flussi di Azure][stream.analytics.scale.jobs]
- [Limitazioni e problemi noti di Analisi dei flussi di Azure][stream.analytics.limitations]
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure][stream.analytics.query.language.reference]
- [Informazioni di riferimento sulle API REST di gestione di Analisi dei flussi di Azure][stream.analytics.rest.api.reference] 



<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!--HONumber=46--> 

<!--HONumber=46--> 
