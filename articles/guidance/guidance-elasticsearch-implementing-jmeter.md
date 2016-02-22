<properties
   pageTitle="Considerazioni per l'implementazione di un piano di test di JMeter per Elasticsearch | Microsoft Azure"
   description="Come eseguire test delle prestazioni per Elasticsearch con JMeter."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/05/2016"
   ms.author="mabsimms"/>
   
# Considerazioni per l'implementazione di un piano di test di JMeter per Elasticsearch

Questo articolo fa [parte di una serie](guidance-elasticsearch-introduction.md).

I test delle prestazioni eseguiti in Elasticsearch sono stati implementati usando i piani di test di JMeter e il codice Java incorporato come test JUnit per l'esecuzione di attività come il caricamento di dati nel cluster. I piani di test e il codice JUnit sono descritti nel documento relativo all'ottimizzazione delle prestazioni di inserimento dati con Elasticsearch in Azure e in quello relativo alle prestazioni delle query con Elasticsearch in Azure.

Lo scopo di questo documento è riepilogare l'esperienza acquisita durante la creazione e l'esecuzione di questi piani di test. La pagina delle [Procedure consigliate di JMeter](http://jmeter.apache.org/usermanual/best-practices.html) nel sito Web di Apache JMeter contiene consigli più generalizzati sull'uso efficace di JMeter.

## Implementazione di un piano di test di JMeter

Nell'elenco seguente vengono riepilogati gli elementi da considerare quando si crea un piano di test di JMeter:

- Creare un gruppo di thread separato per ogni test da eseguire. Un test può includere più passaggi, che comprendono controller di logica, timer, preprocessori e postprocessori, campionatori e listener.

- Evitare di creare troppi thread in un gruppo di thread. Un numero eccessivo di thread provocherà un errore di JMeter con eccezioni di tipo "Memoria insufficiente". È preferibile aggiungere più server JMeter subordinati, in ognuno dei quali verrà eseguito un numero minore di thread, anziché tentare di eseguire un numero elevato di thread in un singolo server JMeter.

![](./media/guidance-elasticsearch-jmeter-testing1.png)

- Per valutare le prestazioni del cluster, incorporare il plug-in [Agente di raccolta metriche Perfmon](http://jmeter-plugins.org/wiki/PerfMon/) nel piano di test. Si tratta di un listener di JMeter disponibile come uno dei plug-in standard di JMeter. Salvare i dati non elaborati sulle prestazioni in un set di file in formato csv ed elaborarli al termine del test. Questo metodo è più efficiente e comporta un minore impegno per JMeter rispetto al tentativo di elaborare i dati al momento dell'acquisizione. È possibile usare uno strumento come Excel per importare i dati e generare un intervallo di grafici per scopi analitici.

![](./media/guidance-elasticsearch-jmeter-testing2.png)

È consigliabile acquisire le informazioni seguenti:

- L'utilizzo della CPU per ogni nodo del cluster Elasticsearch.

- Il numero di byte letti al secondo dal disco per ogni nodo.

- Se possibile, la percentuale di tempo di CPU impiegato in attesa dell'esecuzione delle operazioni di I/O in ciascun nodo. Non è sempre possibile ottenere questo dato per le VM Windows, ma per Linux è possibile creare una metrica personalizzata (una metrica EXEC) che esegue il comando della shell seguente per richiamare *vmstat* in un nodo:

```Shell
sh:-c:vmstat 1 5 | awk 'BEGIN { line=0;total=0;}{line=line+1;if(line&gt;1){total=total+\$16;}}END{print total/4}'
```

Il campo 16 nell'output di *vmstat* contiene il tempo di CPU impiegato in attesa delle operazioni di I/O. Per altre informazioni sul funzionamento di questa istruzione, vedere il [comando vmstat](http://linuxcommand.org/man_pages/vmstat8.html).

- Il numero di byte inviati e ricevuti in rete per ogni nodo.

- Usare listener per rapporti di aggregazione separati per registrare le prestazioni e la frequenza delle operazioni riuscite e non riuscite. Acquisire i dati relativi alle operazioni riuscite e non riuscite in file diversi.

![](./media/guidance-elasticsearch-jmeter-testing3.png)

- Semplificare ogni test case di JMeter per consentire all'utente di correlare direttamente le prestazioni con azioni di test specifiche. Per i test case che richiedono una logica complessa, incapsulare questa logica in un test JUnit e usare il campionatore di richiesta JUnit in JMeter per eseguire il test.

- Usare il campionatore di richiesta HTTP per eseguire operazioni HTTP, ad esempio GET, POST, PUT o DELETE. È possibile ad esempio eseguire ricerche Elasticsearch tramite una query POST e fornendo i dettagli di query nella casella *Body Data*:

![](./media/guidance-elasticsearch-jmeter-testing4.png)

- Per facilitare la ripetibilità e il riutilizzo, impostare i parametri per i piani di test di JMeter. È quindi possibile usare lo scripting per automatizzare l'esecuzione di piani di test.

## Implementazione di un test JUnit

È possibile incorporare un codice complesso in un piano di test di JMeter mediante la creazione di uno o più test JUnit. È possibile scrivere un test JUnit usando un ambiente IDE Java, ad esempio Eclipse. Il documento relativo alla procedura di creazione e distribuzione di un campionatore JUnit di JMeter per il test delle prestazioni di Elasticsearch contiene informazioni su come configurare un ambiente di sviluppo appropriato.

Nell'elenco seguente vengono riepilogate alcune procedure consigliate da seguire per scrivere il codice per un test JUnit:

- Usare il costruttore della classe di test per passare i parametri di inizializzazione nel test. JMeter può usare un costruttore che accetta un solo argomento stringa. Nel costruttore analizzare questo argomento nei singoli elementi, come illustrato nell'esempio di codice seguente:

```Java
private String hostName = "";
private String indexName = "";
private String typeName = "";
private int port = 0;
private String clusterName = "";
private int itemsPerBatch = 0;

/* JUnit test class constructor */
public ElasticSearchLoadTest2(String params) {
	/* params is a string containing a set of comma separated values for:
		hostName
		indexName
		typeName
		port
		clustername
		itemsPerBatch
	*/

    /* Parse the parameter string into an array of string items */
	String delims = "[ ]*,[ ]*"; // comma surrounded by zero or more spaces
	String[] items = params.split(delims);

    /* Note: Parameter validation code omitted */

	/* Use the parameters to populate variables used by the test */
	hostName = items[0];
	indexName = items[1];
	typeName = items[2];
	port = Integer.parseInt(items[3]);
	clusterName = items[4];
	itemsPerBatch = Integer.parseInt(items[5]);

	if(itemsPerBatch == 0)
		itemsPerBatch = 1000;
}
```

- Evitare operazioni costose o di I/O nel costruttore o nella classe di test di installazione, in quanto vengono eseguite a ogni esecuzione del test JUnit (lo stesso test JUnit può essere eseguito diverse migliaia di volte per ogni test delle prestazioni eseguito da JMeter).

- È consigliabile optare per una installazione unica per l'inizializzazione di test case costosi.

- Se il test richiede un numero elevato di parametri di input, archiviare le informazioni di configurazione del test in un file di configurazione separato e passare il percorso del file nel costruttore.

- Evitare di impostare come hardcoded i percorsi dei file nel codice del test di carico. Questi percorsi possono provocare errori a causa delle differenze tra i sistemi operativi, ad esempio Windows e Linux.

- Usare le asserzioni per indicare errori nei metodi di test JUnit in modo da poterne tenere traccia con JMeter e poterli usare come metriche di business. Se possibile, passare nuovamente le informazioni riguardanti la causa dell'errore, come mostrato in grassetto nell'esempio di codice seguente:

```Java
@Test
public void bulkInsertTest() throws IOException {
	...
	BulkResponse bulkResponse = bulkRequest.execute().actionGet();
	assertFalse(
		bulkResponse.buildFailureMessage(), bulkResponse.hasFailures());
		...
}
```

<!---HONumber=AcomDC_0211_2016-->