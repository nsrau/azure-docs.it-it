<properties
	pageTitle="Panoramica dell'analisi scientifica dei dati con Spark in Azure HDInsight | Microsoft Azure"
	description="Il toolkit Spark MLlib introduce importanti funzionalità di modellazione di Machine Learning nell'ambiente distribuito HDInsight."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev,deguhath,gokuma"
	manager="paulettm"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="bradsev" />

# Panoramica dell'analisi scientifica dei dati con Spark in Azure HDInsight

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

## Introduzione

[Spark](http://spark.apache.org/) è un framework open source di elaborazione parallela che supporta l'elaborazione in memoria per migliorare le prestazioni delle applicazioni analitiche di Big Data. Il motore di elaborazione Spark è costruito per la velocità, la semplicità d'uso e le analisi sofisticate. Le funzionalità di elaborazione distribuita in memoria rendono Spark uno strumento valido per l'esecuzione di algoritmi iterativi in calcoli grafici e di Machine Learning. [MLlib](http://spark.apache.org/mllib/) è la libreria scalabile per il Machine Learning di Spark che introduce funzionalità di modellazione nell'ambiente distribuito.

HDInsight Spark è l'offerta ospitata in Azure di Spark open source. Include anche il supporto per Jupyter Notebook nel cluster Spark in grado di eseguire query Spark SQL interattive per trasformare, filtrare e la visualizzare dati archiviati in BLOB di Azure (WASB).

La raccolta di argomenti collegati nel menu fornisce una dimostrazione mediante l'uso di attività di classificazione binaria e regressione su un campione di corse dei taxi di NYC e un set di dati delle tariffe del 2013 e quindi l'archiviazione del modello in WASB. I modelli creati includono la regressione logistica e lineare, foreste casuali e alberi con boosting a gradienti. Mostrano anche come usare questi modelli per classificare e valutare le prestazioni predittive dei modelli. Argomenti più avanzati illustreranno la modalità di training dei modelli con la convalida incrociata e sweep di iperparametri.

La procedura di modellazione in questi argomenti include anche codice che illustra come eseguire il training, valutare, salvare e utilizzare ogni tipo di modello. Python (PySpark) in esecuzione in Jupyter Notebook installato nei cluster Spark è stato usato per creare il codice della soluzione e visualizzare i tracciati rilevanti per la visualizzazione dei dati.

La procedura di installazione e il codice fornito in questa procedura dettagliata è per HDInsight Spark. Tuttavia, il codice è generico e funzionerà in qualsiasi cluster Spark. Se non si usa HDInsight Spark, i passaggi di configurazione e gestione del cluster possono essere leggermente diversi rispetto a quanto illustrato qui.

## Prerequisiti

1\.Prima di iniziare a esaminare questi argomenti, è necessario avere una sottoscrizione di Azure. Se non è già disponibile, vedere l'articolo che illustra [come ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2\.Per creare il cluster HDInsight Spark, versione Spark 1.5.2 (HDI 3.3), vedere [Introduzione: creare cluster Apache Spark in Azure HDInsight](../hdinsight/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)

>[AZURE.NOTE] Il kernel python2 usato dai notebook e dal codice in questa procedura attualmente deve usare Spark (anteprima) -> Spark versione 1.5.2 (HDI 3.3).

![](./media/machine-learning-data-science-spark-overview/spark-cluster-on-portal.png)


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## Dati dei taxi di NYC 2013

I dati relativi alle corse dei taxi di NYC sono rappresentati come file csv compressi e di dimensione pari a 20 GB (48 GB non compressi); questi comprendono informazioni su oltre 173 milioni di corse singole e sulle tariffe pagate. Il record di ogni corsa include la località di partenza e di arrivo, il numero di patente anonimo (del tassista) e il numero di licenza (ID univoco del taxi). I dati sono relativi a tutte le corse per l'anno 2013 e vengono forniti nei due set di dati seguenti per ciascun mese:

1. I file con estensione csv "trip\_data" contengono i dettagli delle corse, ad esempio il numero dei passeggeri, i punti di partenza e arrivo, la durata e la lunghezza della corsa. Di seguito vengono forniti alcuni record di esempio:

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. I file con estensione csv "trip\_fare" contengono i dettagli della tariffa pagata per ciascuna corsa, ad esempio tipo di pagamento, importo, soprattassa e tasse, mance e pedaggi e l'importo totale pagato. Di seguito vengono forniti alcuni record di esempio:

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5


La chiave univoca che consente di unire trip\_data e trip\_fare è composta dai campi: medallion, hack\_licence e pickup\_datetime.


È stato rilevato un campione dello 0,1% di questi file che sono stati uniti in un singolo set di dati da usare come set di dati di input per questa procedura dettagliata. La chiave univoca che consente di unire trip\_data e trip\_fare è composta dai campi: medallion, hack\_licence e pickup\_datetime. Ogni record del set di dati contiene gli attributi seguenti che rappresentano una corsa dei taxi di NYC:


|Campo| Breve descrizione
|------|---------------------------------
| medallion |Numero di licenza anonimo (ID univoco del taxi).
| hack\_license |	Numero di licenza anonimo di vetture a noleggio
| vendor\_id |	ID del fornitore del taxi
| rate\_code | Tariffe dei taxi di NYC
| store\_and\_fwd\_flag | Contrassegno di registrazione e inoltro
| pickup\_datetime |	Data e ora di inizio corsa
| dropoff\_datetime | Data e ora di fine corsa
| pickup\_hour |	Ora di inizio corsa
| pickup\_week |	Settimana dell'anno di inizio corsa
| weekday |	Giorno della settimana (intervallo 1-7)
| passenger\_count |	Numero di passeggeri in una corsa del taxi
| trip\_time\_in\_secs | Tempo delle corse in secondi
| trip\_distance | Distanza delle corse percorsa in miglia
| pickup\_longitude | Longitudine di inizio corsa
| pickup\_latitude |	Latitudine di inizio corsa
| dropoff\_longitude | Longitudine di fine corsa
| dropoff\_latitude | Latitudine di fine corsa
| direct\_distance |	Distanza diretta tra località di inizio corsa e fine corsa
| payment\_type | Tipo di pagamento (contanti, carta di credito e così via).
| fare\_amount | Imposto della tariffa in
| surcharge | Supplemento di tariffa
| mta\_tax |	Tassa MTA
| tip\_amount | Importo delle mance
| tolls\_amount | Importo dei pedaggi
| total\_amount | Importo totale
| tipped | Versato versato (0/1 per no o sì)
| tip\_class | Categoria mance (0: $ 0, 1: $ 0-5, 2: $ 6-10, 3: $ 11-20, 4: > $ 20)


## Eseguire il codice da Jupyter Notebook nel cluster Spark 

È possibile avviare Jupyter Notebook dal portale di Azure: trovare il cluster Spark e selezionarlo per accedere alla pagina dettagliata di gestione di cluster. Da lì è possibile fare clic sui **Dashboard cluster**, in cui l'icona di Jupyter Notebook è associata al cluster Spark.

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-on-portal.png)

È anche possibile passare a ******https://CLUSTERNAME.azurehdinsight.net/jupyter*** per accedere ai notebook di Jupyter. Sarà necessaria la password dell'account amministratore accedere al notebook.

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-notebook.png)

Passare a Python per vedere i notebook esistenti che eseguono script Python. Si noterà una directory che contiene l'elenco di alcuni esempi di notebook preconfezionati. Il notebook che contiene gli esempi di codice in questo argomento è disponibile in [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Python).

È possibile caricare il notebook direttamente da Github nel server Jupyter Notebook del cluster Spark. Nella home page di Jupyter fare clic sul pulsante **Upload** a destra nella schermata. Si aprirà una finestra di esplorazione file. Qui è possibile incollare l'URL di Github (contenuto non elaborato) di Notebook e fare clic su **Open**. Il nome del file verrà visualizzato nell'elenco di file Jupyter premendo di nuovo il pulsante **Upload**. Fare clic sul pulsante **Upload**. A questo punto, il notebook è stato importato. Ripetere questi passaggi per caricare il notebook seguenti da questa procedura dettagliata. Nota: è possibile fare clic con il pulsante destro del mouse sui collegamenti seguenti sul browser e scegliere **Copia collegamento** per ottenere l'URL del contenuto non elaborato di github da incollare nella finestra di dialogo di esplorazione file per il caricamento in Jupyter.

1.	[machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/machine-learning-data-science-spark-data-exploration-modeling.ipynb)
2.	[machine-learning-data-science-spark-model-consumption.ipynb](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/machine-learning-data-science-spark-model-consumption.ipynb)
3.	[machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb)

A questo punto è possibile:

- Fare clic sul notebook per visualizzare il codice.
- Eseguire ogni cella premendo **MAIUSC+INVIO**.
- Eseguire l'intero notebook facendo clic su **Cell** -> **Run**.


## Passaggi successivi

Dopo avere configurato un cluster HDInsight Spark e avere caricato Jupyter Notebook, è possibile usare gli argomenti corrispondenti a questi tre notebook, che illustrano come esplorare i dati e creare e usare i modelli. Il notebook per la modellazione e l'esplorazione avanzata dei dati approfondisce la convalida incrociata, lo sweep degli iperparametri e la valutazione del modello.

**Esplorazione dei dati e creazione del modello con Spark:** esplorare il set di dati, creare i modelli di Machine Learning per l'assegnazione di punteggi e la valutazione illustrati nell'argomento [Creare modelli di regressione e classificazione binaria per i dati con il toolkit Spark MLlib](machine-learning-data-science-spark-data-exploration-modeling.md).

**Utilizzo dei modelli:** per informazioni su come valutare i modelli di regressione e di classificazione creati in questo argomento, vedere [Assegnare punteggi e valutare i modelli di Machine Learning compilati con Spark](machine-learning-data-science-spark-model-consumption.md).

**Convalida incrociata e sweep di iperparametri**: vedere [Modellazione ed esplorazione avanzata dei dati con Spark](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) per informazioni su come istruire i modelli sulla convalida incrociata e lo sweep di iperparametri.

<!---HONumber=AcomDC_0427_2016-->