<properties title="Scale Azure Stream Analytics jobs" pageTitle="Scalabilità dei processi di Analisi dei flussi | Azure" description="Learn how to scale Stream Analytics jobs" metaKeywords="" services="stream-analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="10/28/2014" ms.author="jgao" />

# Scalabilità dei processi di Analisi dei flussi di Azure 

Informazioni su come calcolare le *unità di streaming* per un processo di Analisi dei flussi e come scalare i processi di questo tipo configurando partizioni di input, ottimizzando la definizione di query e impostando le unità di streaming del processo.

Una definizione del processo di Analisi dei flussi include input, query e output. Gli input sono l'origine da cui il processo legge il flusso di dati, l'output è la destinazione a cui il processo invia i risultati e la query viene usata per trasformare il flusso di input.  Un processo richiede almeno un'origine di input del flusso di dati. L'origine di input del flusso di dati può essere un hub eventi del bus di servizio di Azure o un archivio BLOB di Azure. Per altre informazioni, vedere [Introduzione ad Analisi dei flussi di Azure][stream.analytics.introduction], [Introduzione all'uso di Analisi dei flussi di Azure][stream.analytics.get.started] e [Guida per gli sviluppatori di Analisi dei flussi di Azure][stream.analytics.developer.guide]. 

La risorsa disponibile per l'elaborazione di processi di Analisi dei flussi è misurata da un'unità di streaming. Ogni unità di streaming può fornire una velocità effettiva fino a 1 MB/secondo. Ogni processo richiede almeno un'unità di streaming, che è l'impostazione predefinita per tutti i processi. È possibile impostare fino a 12 unità di streaming per un processo di Analisi dei flussi mediante il portale di gestione di Azure. Ogni sottoscrizione di Azure può avere solo un massimo di 12 unità di streaming per tutti i processi in un'area specifica. Per aumentare le unità di streaming per la sottoscrizione fino a 100 unità, contattare il [Supporto Microsoft][microsoft.support].

Il numero di unità di streaming che un processo può usare dipende dalla configurazione delle partizioni sugli input e dalla query definita per il processo. Questo articolo illustra come calcolare e ottimizzare la query per incrementare la velocità effettiva.

##Contenuto dell'articolo
+ [Calcolare il numero massimo di unità di streaming per un processo](#calculate)
+ [Configurare la partizione del processo di Analisi dei flussi](#configure)
+ [Monitorare le prestazioni del processo di Analisi dei flussi](#monitor)
+ [Passaggi successivi](#nextstep)


##<a name="calculate"></a>Calcolare il numero massimo di unità di streaming di un processo
Il numero totale di unità di streaming che possono essere usate da un processo di Analisi dei flussi dipende dal numero di passaggi nella query definita per il processo e dal numero di partizioni per ogni passaggio.

### Passaggi di una query
Una query può includere uno o più passaggi. Ogni passaggio è una sottoquery definita mediante la parola chiave WITH. L'unica query all'esterno della parola chiave WITH viene contata anch'essa come passaggio. Ad esempio, l'istruzione SELECT nella query seguente:

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId 
		GROUP BY TumblingWindow(minute, 3), TollBoothId
	) 

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1 
	GROUP BY TumblingWindow(minute,3), TollBoothId

La query precedente include 2 passaggi. 

> [WACOM.NOTE] Questa query di esempio verrà illustrata più avanti in questo articolo.

### Partizionamento di un passaggio

Il partizionamento di un passaggio richiede le condizioni seguenti:

- L'origine di input deve essere partizionata. Vedere la [Guida per gli sviluppatori di Analisi dei flussi di Azure][stream.analytics.developer.guide] e [Guida per gli sviluppatori di Hub eventi di Azure][azure.event.hubs.developer.guide].
- L'istruzione SELECT della query deve leggere da un'origine di input partizionata. 
- La query all'interno del passaggio deve avere la parola chiave Partition By 

Quando una query è partizionata, gli eventi di input vengono elaborati e aggregati in gruppi di partizioni separati e vengono generati eventi di output per ognuno dei gruppi. Se è preferibile un aggregato combinato, è necessario creare un secondo passaggio non partizionato da aggregare.

La versione di anteprima di Analisi dei flussi di Azure non supporta il partizionamento in base ai nomi di colonna. È possibile partizionare solo in base al campo PartitionId, un campo predefinito nella query. Il campo ParitionId indica la partizione del flusso di dati di origine da cui proviene l'evento.  Per altre informazioni, vedere [Limitazioni e problemi noti di Analisi dei flussi di Azure][stream.analytics.limitations].

### Calcolare il numero massimo di unità di streaming per un processo

Tutti i passaggi non partizionati insieme possono arrivare fino a 6 unità di streaming per un processo di Analisi dei flussi. Per aggiungere ulteriori unità di streaming, è necessario partizionare un passaggio. Ogni partizione può includere 6 unità di streaming.

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
<li>La query contiene due passaggi.</li>
<li>L'istruzione SELECT legge dall'input partizionato.</li>
</ul>
</td>
<td>24 (18 per il passaggio partizionato + 6 per il passaggio non partizionato)</td></tr>
</table>

###Esempio di scalabilità
La query seguente calcola il numero di automobili che passano per una stazione di pedaggio con tre caselli in una finestra temporale di 3 minuti. Questa query può essere aumentata fino a 6 unità di streaming.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 
	GROUP BY TumblingWindow(minute, 3), TollBoothId

Per usare più unità di streaming per la query, è necessario che l'input del flusso di dati e la query siano partizionati. Se la partizione del flusso di dati è impostata su 3, la query modificata seguente può essere aumentata fino a 18 unità di streaming.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 Partition By PartitionId
	GROUP BY TumblingWindow(minute, 3), TollBoothId

Quando una query è partizionata, gli eventi di input vengono elaborati e aggregati in gruppi di partizioni separati e vengono anche generati eventi di output per ognuno dei gruppi. Il partizionamento può causare risultati imprevisti quando il campo group-by non è la chiave di partizione nell'input del flusso di dati. Ad esempio, il campo TollBoothId nella query di esempio precedente non è la chiave di partizione di Input1. I dati provenienti dal casello 1 possano essere distribuiti in più partizioni. Le singole partizioni Input1 verranno elaborata separatamente da Analisi dei flussi e verranno creati più record del conteggio del passaggio di automobili dello stesso casello nella stessa finestra a cascata. Nel caso in cui la chiave di partizione di input non possa essere modificata, è possibile risolvere il problema aggiungendo un ulteriore passaggio non di partizione. Ad esempio:

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId
	) 

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1 
	GROUP BY TumblingWindow(minute, 3), TollBoothId

Questa query può essere aumentata fino a 24 unità di streaming. 

>[WACOM.NOTE] Se si crea un join di due flussi, assicurarsi che i flussi siano partizionati dalla chiave di partizione della colonna usata per i join e che in entrambi i flussi sia presente lo stesso numero di partizioni.


##<a name="configure"></a>Configurare la partizione del processo di Analisi dei flussi

**Per regolare l'unità di streaming di un processo**

1. Accedere al [portale di gestione][azure.management.portal].
2. Fare clic su **Analisi dei flussi** a sinistra.
3. Fare clic sul processo di Analisi dei flussi da scalare.
4. Fare clic su **SCALABILITÀ** nella parte superiore della pagina.

![Azure Stream Analytics configure job scale][img.stream.analytics.configure.scale]


##<a name="monitor"></a>Monitorare le prestazioni del processo

Nel portale di gestione è possibile rilevare la velocità effettiva di un processo, espressa in eventi al secondo:

![Azure Stream Analytics monitor jobs][img.stream.analytics.monitor.job]
 
Calcolare la velocità effettiva prevista del carico di lavoro in eventi al secondo. Se la velocità effettiva è minore del previsto, ottimizzare la partizione di input, ottimizzare la query e aggiungere unità di streaming al processo.

##<a name="nextstep"></a> Passaggi successivi
In questo articolo si è appreso come calcolare le unità di streaming e come scalare un processo di Analisi dei flussi. Per altre informazioni su Analisi dei flussi, vedere:

- [Introduzione ad Analisi dei flussi di Azure][stream.analytics.introduction]
- [Introduzione all'uso di Analisi dei flussi di Azure][stream.analytics.get.started]
- [Guida per gli sviluppatori di Analisi dei flussi di Azure][stream.analytics.developer.guide]
- [Limitazioni e problemi noti di Analisi dei flussi di Azure][stream.analytics.limitations]
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure][stream.analytics.query.language.reference]
- [Informazioni di riferimento sulle API REST di gestione di Analisi dei flussi di Azure][stream.analytics.rest.api.reference]

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/it-it/library/azure/dn789972.aspx

[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


