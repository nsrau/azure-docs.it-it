
<properties
   pageTitle="Esecuzione dei test automatici delle prestazioni di Elasticsearch | Microsoft Azure"
   description="Descrizione di come è possibile eseguire i test delle prestazioni nel proprio ambiente."
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
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Esecuzione dei test automatici delle prestazioni di Elasticsearch

Questo articolo fa [parte di una serie](guidance-elasticsearch.md).

I documenti [Ottimizzazione delle prestazioni di inserimento dei dati per Elasticsearch in Azure] e [Ottimizzazione dell'aggregazione dei dati e delle prestazioni delle query per Elasticsearch in Azure] descrivono alcuni test delle prestazioni eseguiti su un cluster Elasticsearch di esempio.

I test sono stati inseriti in uno script per consentirne l'esecuzione automatica. Questo documento descrive come è possibile ripetere i test nel proprio ambiente.

## Prerequisiti

I test automatici richiedono gli elementi seguenti:

-  Un cluster Elasticsearch.

- La configurazione dell'ambiente JMeter, come descritto nel documento [Creazione di un ambiente di test delle prestazioni per Elasticsearch in Azure].

- Il software seguente installato solo nella VM master di JMeter.

    [Python 3.5.1](https://www.python.org/downloads/release/python-351/)


## Funzionamento dei test
I test vengono eseguiti con JMeter. Un server master JMeter carica un piano di test e lo passa a un set di server secondari di JMeter che eseguono effettivamente i test. Il server master JMeter coordina i relativi server secondari e accumula i risultati.

Vengono forniti i piani di test seguenti.

* [elasticsearchautotestplan3nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan3nodes.jmx). Questo piano di test esegue il test di inserimento in un cluster di 3 nodi.

* [elasticsearchautotestplan6nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodes.jmx). Questo piano di test esegue il test di inserimento in un cluster di 6 nodi.

* [elasticsearchautotestplan6qnodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6qnodes.jmx). Questo piano di test esegue il test di inserimento e query in un cluster di 6 nodi.

* [elasticsearchautotestplan6nodesqueryonly.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodesqueryonly.jmx). Questo piano di test esegue il test solo di query in un cluster di 6 nodi.


È possibile usare questi piani di test come base per i propri scenari, se sono necessari più o meno più nodi.

I piani di test usano un campionatore JUnit Request per generare e caricare i dati di test. Il piano di test di JMeter crea ed esegue il campionatore e monitora i dati sulle prestazioni di ogni nodo Elasticsearch. Per altre informazioni, vedere le appendici dei documenti[Ottimizzazione delle prestazioni di inserimento dei dati per Elasticsearch in Azure] e [Ottimizzazione dell'aggregazione dei dati e delle prestazioni delle query per Elasticsearch in Azure].

## Creazione e distribuzione del file JAR e delle dipendenze di JUnit
Prima di eseguire i test di resilienza, è consigliabile scaricare, compilare e distribuire i test JUnit disponibili nella cartella performance/junitcode. A questi test fa riferimento il piano di test di JMeter. Per altre informazioni, vedere la procedura relativa all'importazione di un progetto di test JUnit esistente in Eclipse nel documento [Distribuzione di un campionatore JUnit per JMeter per il test delle prestazioni di Elasticsearch].

Esistono due versioni dei test JUnit:- [Elasticsearch1.73](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch1.73). Usare questo codice per eseguire i test di inserimento. Questi test usano Elasticsearch 1.73 - [Elasticsearch2](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch2). Usare questo codice per eseguire i test di query. Questi test usano Elasticsearch 2.1 e versioni successive.

Copiare il file JAR appropriato insieme al resto delle dipendenze nelle macchine JMeter. Il processo è descritto in [Distribuzione di un campionatore JUnit per JMeter per il test delle prestazioni di Elasticsearch][].

> **Importante** Dopo la distribuzione di un test JUnit, usare JMeter per caricare e configurare i piani di test che fanno riferimento a questo test JUnit e assicurarsi che il gruppo di thread *BulkInsertLarge* faccia riferimento al file JAR, al nome della classe JUnit e al metodo di test corretti:
> 
> ![](./media/guidance-elasticsearch/performance-tests-image1.png)
> 
> Salvare i piani di test aggiornati prima di eseguire i test.

## Creazione degli indici dei test
Ogni test esegue operazioni di inserimento e/o di query su un singolo indice specificato quando si esegue il test. È necessario creare l'indice usando gli schemi descritti nelle appendici dei documenti [Ottimizzazione delle prestazioni di inserimento dei dati per Elasticsearch in Azure] e [Ottimizzazione dell'aggregazione dei dati e delle prestazioni delle query per Elasticsearch in Azure] e configurarli in base al proprio scenario di test, ovvero valori dei documenti (doc\_values) abilitati/disabilitati, più repliche e così via. Si noti che i piani di test presuppongono che l'indice includa un singolo tipo denominato *ctip*.

## Configurazione dei parametri di script dei test
Copiare i file di parametri degli script di test seguenti nel computer server JMeter:

* [run.properties](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/run.properties). Questo file specifica il numero dei thread di test di JMeter da usare, la durata del test (in secondi), l'indirizzo IP di un nodo, o un servizio di bilanciamento del carico, nel cluster Elasticsearch e il nome del cluster:

  ```ini
  nthreads=3
  duration=300
  elasticip=<IP Address or DNS Name Here>
  clustername=<Cluster Name Here>
  ```
  
  Modificare questo file e specificare i valori appropriati per i test e il cluster.

* [query-config-win.ini](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-win.ini) e [query-config-nix.ini](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-nix.ini). Questi due file contengono le stesse informazioni. Il file *win* è formattato per i nomi file e i percorsi di Windows e il file *nix* è formattato per i nomi file e i percorsi di Linux:

  ```ini
  [DEFAULT]
  debug=true #se true mostra i log della console.

  [RUN]
  pathreports=C:\\Users\\administrator1\\jmeter\\test-results\\ #percorso in cui vengono salvati i risultati dei test.
  jmx=C:\\Users\\administrator1\\testplan.jmx #percorso del piano di test JMeter.
  machines=10.0.0.1,10.0.02,10.0.0.3 #indirizzi IP dei nodi dati di Elasticsearch separati da virgole.
  reports=aggr,err,tps,waitio,cpu,network,disk,response,view #Nomi dei report separati da virgole. tests=idx1 #nome dell'indice di Elasticsearch da testare. properties=run.properties #Nome del file delle proprietà.
  ```

  Modificare questo file per specificare i percorsi dei risultati del test, il nome del piano di test di JMeter da eseguire, gli indirizzi IP dei nodi dati di Elasticsearch, i report che contengono i dati sulle prestazioni non elaborati che saranno generati e il nome o i nomi dell'indice o degli indici da testare. Se il file *run.properties* si trova in una cartella o una directory diversa, specificare il percorso completo del file.

## Esecuzione dei test

* Copiare il file [query test.py](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-test.py) nel computer server JMeter, nella stessa cartella dei file *run.properties* e *query-config-win.ini* (*query-config-nix.ini*).

* Assicurarsi che *jmeter.bat* (Windows) o *jmeter.sh* (Linux) si trovino nel percorso dell'eseguibile per l'ambiente locale.

* Eseguire lo script *query test.py* dalla riga di comando per eseguire i test:

  ```cmd
  py query-test.py
  ```

* Quando il test è stato completato, i risultati vengono archiviati come set di file CSV specificato nel file *query-config-Win* (*query-config-nix.ini*). È possibile usare Excel per analizzare i dati e creare un grafico.


[Ottimizzazione delle prestazioni di inserimento dei dati per Elasticsearch in Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Ottimizzazione dell'aggregazione dei dati e delle prestazioni delle query per Elasticsearch in Azure]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Creazione di un ambiente di test delle prestazioni per Elasticsearch in Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
[Distribuzione di un campionatore JUnit per JMeter per il test delle prestazioni di Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md

<!-----HONumber=AcomDC_0224_2016-->