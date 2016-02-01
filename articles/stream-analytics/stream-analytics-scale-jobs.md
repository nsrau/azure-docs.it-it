<properties
	pageTitle="Ridimensionare i processi di Analisi di flusso per aumentare la velocità effettiva | Microsoft Azure"
	description="Informazioni su come ridimensionare i processi di Analisi di flusso configurando partizioni di input, ottimizzando la definizione di query e impostando le unità di streaming del processo."
	keywords="flusso di dati, elaborazione del flusso di dati, ottimizzare analisi"
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
	ms.date="12/16/2015"
	ms.author="jeffstok"/>

# Aumentare la velocità effettiva di elaborazione dati con la scalabilità dei processi di analisi di flusso #

Informazioni su come ottimizzare i processi di analisi e calcolare le *unità di streaming* per un processo di Analisi di flusso e su come ridimensionare i processi di questo tipo configurando partizioni di input, ottimizzando la definizione di query e impostando le unità di streaming del processo.

## Quali sono le parti di un processo di Analisi di flusso? ##
Una definizione del processo di Analisi di flusso include input, query e output. Gli input sono l'origine da cui il processo legge il flusso di dati, la query viene usata per trasformare il flusso di input dei dati e l'output è la destinazione a cui il processo invia i risultati.

Un processo richiede almeno un'origine di input per il flusso dei dati. L'origine di input del flusso di dati può essere archiviata in un hub eventi del bus di servizio di Azure o un archivio BLOB di Azure. Per altre informazioni, vedere [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md), [Introduzione all’uso di Analisi dei flussi di Azure](stream-analytics-get-started.md) e [Guida per gli sviluppatori di Analisi dei flussi di Azure](../stream-analytics-developer-guide.md).

## Configurazione delle unità di streaming ##
Le unità di streaming rappresentano le risorse e le funzionalità dedicate all'esecuzione di un processo di Analisi di flusso di Azure. Le unità di streaming descrivono la capacità relativa di elaborazione di eventi in base a una misurazione combinata di CPU, memoria e frequenze di lettura e scrittura. Ogni unità di streaming corrisponde a circa 1 MB al secondo di velocità effettiva.

Il numero di unità di streaming necessarie per un particolare processo dipende dalla configurazione della partizione per gli input e dalla query definita per il processo. È possibile selezionare un numero massimo pari alla quota di unità di streaming per un processo mediante il portale di Azure. Per impostazione predefinita, ogni sottoscrizione di Azure ha una quota di un massimo di 50 unità di streaming per tutti i processi di analisi in un'area specifica. Per aumentare le unità di streaming per le sottoscrizioni, contattare il [Supporto tecnico Microsoft](http://support.microsoft.com).

Il numero di unità di streaming che un processo può usare dipende dalla configurazione delle partizioni per gli input e dalla query definita per il processo. Si noti inoltre che è necessario usare un valore valido per le unità di streaming. I valori validi iniziano da 1, 3, 6 e poi a salire con incrementi di 6, come illustrato di seguito.

![Scalabilità delle unità di streaming di Analisi di flusso di Azure][img.stream.analytics.streaming.units.scale]

In questo articolo viene illustrato come calcolare e ottimizzare la query per incrementare la velocità effettiva per i processi di analisi.

## Calcolare il numero massimo di unità di streaming di un processo ##
Il numero totale di unità di streaming che possono essere usate da un processo di Analisi dei flussi dipende dal numero di passaggi nella query definita per il processo e dal numero di partizioni per ogni passaggio.

### Passaggi in una query ###
Una query può includere uno o più passaggi. Ogni passaggio è una sottoquery definita mediante la parola chiave WITH. Anche l’unica query esterna alla parola chiave WITH viene contata come passaggio, ad esempio, l’istruzione SELECT nella query seguente:

	WITH Step1 AS (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
	)

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1
	GROUP BY TumblingWindow(minute,3), TollBoothId

La query precedente include due passaggi.

> [AZURE.NOTE]Questa query di esempio verrà illustrata più avanti in questo articolo.

### Partizionamento di un passaggio ###

Il partizionamento di un passaggio richiede le condizioni seguenti:

- L'origine di input deve essere partizionata. Per ulteriori informazioni, vedere [Guida per sviluppatori di Analisi dei flussi di Azure](../stream-analytics-developer-guide.md) e [Guida alla programmazione di Hub eventi](../event-hubs/event-hubs-programming-guide.md).
- L'istruzione SELECT della query deve leggere da un'origine di input partizionata.
- Nella query all’interno del passaggio deve essere presente la parola chiave **Partition By**

Quando una query è partizionata, gli eventi di input vengono elaborati e aggregati in gruppi di partizioni separati e vengono generati eventi di output per ognuno dei gruppi. Se è preferibile un aggregato combinato, è necessario creare un secondo passaggio non partizionato da aggregare.

### Calcolare il numero massimo di unità di streaming per un processo ###

Tutti i passaggi non partizionati insieme possono arrivare fino a sei unità di streaming per un processo di Analisi dei flussi. Per aggiungere ulteriori unità di streaming, è necessario partizionare un passaggio. Ogni partizione può includere sei unità di streaming.

<table border="1">
<tr><th>Query di un processo</th><th>Numero massimo di unità di streaming per il processo</th></td>

<tr><td>
<ul>
<li>La query contiene un unico passaggio.</li>
<li>Il passaggio non è partizionato.</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>Il flusso di dati di input è suddiviso in 3 partizioni.</li>
<li>La query contiene un unico passaggio.</li>
<li>Il passaggio è partizionato.</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>La query contiene due passaggi.</li>
<li>Nessuno dei passaggi è partizionato.</li>
</ul>
</td>
<td>6</td></tr>



<tr><td>
<ul>
<li>L'input del flusso di dati è suddiviso in 3 partizioni.</li>
<li>La query contiene due passaggi. Il passaggio di input viene partizionato, al contrario del secondo passaggio.</li>
<li>L'istruzione SELECT legge dall'input partizionato.</li>
</ul>
</td>
<td>24 (18 per i passaggi partizionati + 6 per i passaggi non partizionati)</td></tr>
</table>

### Esempio di scalabilità ###
La query seguente calcola il numero di automobili che passano per una stazione di pedaggio con tre caselli in una finestra temporale di tre minuti. Questa query può essere aumentata fino a sei unità di streaming.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1
	GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Per usare più unità di streaming per la query, è necessario che l'input del flusso di dati e la query siano entrambi partizionati. Se la partizione del flusso di dati è impostata su 3, la query modificata seguente può essere aumentata fino a 18 unità di streaming:

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 Partition By PartitionId
	GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Quando una query è partizionata, gli eventi di input vengono elaborati e aggregati in gruppi di partizioni separati e vengono anche generati eventi di output per ognuno dei gruppi. Il partizionamento può causare risultati imprevisti quando il campo **Group-by** non è la chiave di partizione nell’input del flusso di dati. Ad esempio, il campo TollBoothId nella query di esempio precedente non è la chiave di partizione di Input1. I dati provenienti dal casello 1 possono essere distribuiti in più partizioni.

Le singole partizioni Input1 verranno elaborate separatamente da Analisi dei flussi e verranno creati più record del conteggio del passaggio di automobili dello stesso casello nella stessa finestra a cascata. Nel caso in cui la chiave di partizione di input non possa essere modificata, è possibile risolvere il problema aggiungendo un ulteriore passaggio non di partizione, ad esempio:

	WITH Step1 AS (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
	)

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1
	GROUP BY TumblingWindow(minute, 3), TollBoothId

Questa query può essere aumentata fino a 24 unità di streaming.

>[AZURE.NOTE]Se si crea un join di due flussi, assicurarsi che i flussi siano partizionati dalla chiave di partizione della colonna usata per i join e che in entrambi i flussi sia presente lo stesso numero di partizioni.


## Configurare la partizione del processo di Analisi dei flussi ##

**Per regolare l'unità di streaming di un processo**

1. Accedere al [portale di gestione](https://manage.windowsazure.com).
2. Fare clic su **Analisi dei flussi** nel riquadro a sinistra.
3. Fare clic sul processo di Analisi dei flussi da scalare.
4. Nella parte superiore della pagina fare clic su **RIDIMENSIONA**.

![Scalabilità delle unità di streaming di Analisi di flusso di Azure][img.stream.analytics.streaming.units.scale]

Nel portale di anteprima di Azure è possibile accedere alle impostazioni di scala in Impostazioni:

![Configurazione del processo di analisi di flusso del portale di anteprima di Azure][img.stream.analytics.preview.portal.settings.scale]

## Monitorare le prestazioni del processo ##

Nel portale di gestione è possibile rilevare la velocità effettiva di un processo, espressa in eventi al secondo:

![Processi di monitoraggio di Analisi dei flussi di Azure][img.stream.analytics.monitor.job]

Calcolare la velocità effettiva prevista del carico di lavoro in eventi al secondo. Se la velocità effettiva è inferiore al previsto, ottimizzare la partizione di input, ottimizzare la query e aggiungere unità di streaming al processo.

## Velocità effettiva ASA su larga scala - Scenario Raspberry Pi ##


Per comprendere come ASA garantisce la scalabilità in uno scenario tipico in termini di velocità effettiva di elaborazione tra più unità di streaming, viene riportato un esperimento nel corso del quale i dati del sensore (client) vengono inviati a Hub di eventi. ASA li elabora e invia avvisi o le statistiche come output a un altro Hub di eventi.

Il client sta inviando i dati del sensore sintetizzati ad Hub eventi in formato JSON per ASA e anche l’output dei dati è in formato JSON. Di seguito viene mostrato l’aspetto che avranno i dati di esempio

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

Query: "Invia un avviso quando viene spenta la luce"

    SELECT AVG(lght),
	 “LightOff” as AlertText
	FROM input TIMESTAMP
	BY devicetime
	 WHERE
		lght< 0.05 GROUP BY TumblingWindow(second, 1)

Misurazione della velocità effettiva: la velocità effettiva in questo contesto è la quantità di dati di input elaborata da ASA in determinato tempo (10 minuti). Per ottenere la velocità effettiva di elaborazione ottimale per i dati di input, i dati di flusso di input e la query devono essere entrambi partizionati. Anche COUNT() è incluso nella query per misurare il numero di eventi di input che sono stati elaborati. Per essere certi che ASA non sia semplicemente in attesa dell’arrivo degli eventi, ciascuna partizione dell'Hub eventi di input è stata precaricata con dati di input sufficienti (circa 300 MB).

Di seguito sono riportati i risultati con un numero crescente di unità di streaming e i conteggi corrispondenti delle partizioni in Hub eventi.

<table border="1">
<tr><th>Partizioni di input</th><th>Partizioni di output</th><th>Unità di streaming</th><th>Velocità effettiva sostenuta
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4,06 MB/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8,06 MB/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38,32 MB/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172,67 MB/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454,27 MB/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609,69 MB/s</td>
</tr>
</table>

![img.stream.analytics.perfgraph][img.stream.analytics.perfgraph]

## Ottenere aiuto ##
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=AzureStreamAnalytics).


## Passaggi successivi ##

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 

<!---HONumber=AcomDC_0121_2016-->