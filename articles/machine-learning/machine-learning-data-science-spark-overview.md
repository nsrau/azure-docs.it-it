---
title: Panoramica dell'analisi scientifica dei dati con Spark in Azure HDInsight | Microsoft Docs
description: Il toolkit Spark MLlib introduce importanti funzionalità di modellazione di Machine Learning nell'ambiente distribuito HDInsight.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2016
ms.author: deguhath;bradsev;gokuma

---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Panoramica dell'analisi scientifica dei dati con Spark in Azure HDInsight
[!INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Questa raccolta di argomenti illustra come usare HDInsight Spark per completare attività comuni di analisi scientifica dei dati, come l'inserimento di dati, la progettazione di funzionalità, la modellazione e la valutazione di modelli. Come dati di esempio sono stati presi i dati relativi alle corse e alle tariffe dei taxi di New York nel 2013. I modelli creati includono la regressione logistica e quella lineare, foreste casuali e alberi con boosting a gradienti. Gli argomenti illustrano anche come archiviare questi modelli nell'archiviazione BLOB di Azure (WASB) e come classificare e valutare le relative prestazioni predittive. Argomenti più avanzati illustrano le modalità di training dei modelli con la convalida incrociata e lo sweep di iperparametri. Questo argomento di panoramica descrive anche come impostare il cluster Spark necessario per completare i passaggi delle tre procedure dettagliate illustrate. 

[Spark](http://spark.apache.org/) è un framework open source di elaborazione parallela che supporta l'elaborazione in memoria per migliorare le prestazioni delle applicazioni analitiche di Big Data. Il motore di elaborazione Spark è costruito per la velocità, la semplicità d'uso e le analisi sofisticate. Le funzionalità di elaborazione distribuita in memoria rendono Spark uno strumento valido per l'esecuzione di algoritmi iterativi in calcoli grafici e di Machine Learning. [MLlib](http://spark.apache.org/mllib/) è la libreria scalabile per il Machine Learning di Spark che introduce funzionalità di modellazione nell'ambiente distribuito. 

[HDInsight Spark](../hdinsight/hdinsight-apache-spark-overview.md) è la soluzione ospitata in Azure di Spark open source. Include anche il supporto per **notebook di Jupyter PySpark** nel cluster Spark in grado di eseguire query Spark SQL interattive per trasformare, filtrare e visualizzare i dati archiviati in BLOB di Azure (WASB). PySpark è l'API di Python per Spark. I frammenti di codice che consentono di creare le soluzioni e mostrano i tracciati rilevanti per la visualizzazione dei dati sono eseguiti qui in notebook di Jupyter installati in cluster Spark. La procedura di modellazione riportata in questi argomenti include anche codice che illustra come eseguire il training, valutare, salvare e usare ogni tipo di modello. 

La procedura di installazione e il codice fornito in questa procedura dettagliata è per HDInsight 3.4 Spark 1.6. Tuttavia, il codice in questo esempio e nei notebook è generico e funzionerà in qualsiasi cluster Spark. Se non si usa HDInsight Spark, i passaggi di configurazione e gestione del cluster possono essere leggermente diversi rispetto a quanto illustrato qui.

## <a name="prerequisites"></a>Prerequisiti
1. È necessario avere una sottoscrizione di Azure. Se non è già disponibile, vedere l'articolo che illustra [come ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
2. Per completare questa procedura dettagliata è necessario un cluster HDInsight 3.4 Spark 1.6. Per crearne uno, vedere le istruzioni fornite in [Introduzione: creare cluster Apache Spark in Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). Il tipo e la versione del cluster vengono specificati tramite il menu **Selezionare il tipo di cluster** . 

![](./media/machine-learning-data-science-spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Per un argomento che illustra come usare Scala anziché Python per completare le attività per un processo di analisi scientifica dei dati end-to-end, vedere l'articolo sull' [analisi scientifica dei dati tramite Scala con Spark in Azure](machine-learning-data-science-process-scala-walkthrough.md).
> 
> 

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

## <a name="the-nyc-2013-taxi-data"></a>Dati dei taxi di NYC 2013
I dati relativi alle corse dei taxi della città di New York sono costituiti da file con valori delimitati da virgole (CSV) compressi di dimensioni pari a circa 20 GB (circa 48 GB non compressi), che includono informazioni su oltre 173 milioni di corse singole e sulle tariffe pagate per ognuna. Il record di ogni corsa include la località e l'orario di partenza e di arrivo, il numero di patente anonimo (del tassista) e il numero di licenza (ID univoco del taxi). I dati sono relativi a tutte le corse per l'anno 2013 e vengono forniti nei due set di dati seguenti per ciascun mese:

1. I file CSV "trip_data" contengono i dettagli delle corse, ad esempio il numero dei passeggeri, i punti di partenza e di arrivo, la durata e la lunghezza della corsa. Di seguito vengono forniti alcuni record di esempio:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. I file con estensione csv "trip_fare" contengono i dettagli della tariffa pagata per ciascuna corsa, ad esempio tipo di pagamento, importo, soprattassa e tasse, mance e pedaggi e l'importo totale pagato. Di seguito vengono forniti alcuni record di esempio:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

È stato prelevato un campione pari allo 0,1% di questi file e i file CSV trip\_data e trip\_fare sono stati uniti in un singolo set di dati da usare come set di dati di input per questa procedura dettagliata. La chiave univoca che consente di unire trip\_data e trip\_fare è composta dai campi: medallion, hack\_licence e pickup\_datetime. Ogni record del set di dati contiene gli attributi seguenti che rappresentano una corsa dei taxi di NYC:

| Campo | Breve descrizione |
| --- | --- |
| medallion |Numero di licenza anonimo (ID univoco del taxi). |
| hack_license |Numero di licenza anonimo di vetture a noleggio |
| vendor_id |ID del fornitore del taxi |
| rate_code |Tariffe dei taxi di NYC |
| store_and_fwd_flag |Contrassegno di registrazione e inoltro |
| pickup_datetime |Data e ora di partenza |
| dropoff_datetime |Data e ora di arrivo |
| pickup_hour |Ora di partenza |
| pickup_week |Settimana dell'anno di partenza |
| weekday |Giorno della settimana (intervallo 1-7) |
| passenger_count |Numero di passeggeri in una corsa del taxi |
| trip_time_in_secs |Tempo delle corse in secondi |
| trip_distance |Distanza delle corse percorsa in miglia |
| pickup_longitude |Longitudine di partenza |
| pickup_latitude |Latitudine di partenza |
| dropoff_longitude |Longitudine di arrivo |
| dropoff_latitude |Latitudine di arrivo |
| direct_distance |Distanza diretta tra le località di partenza e di arrivo |
| payment_type |Tipo di pagamento (contanti, carta di credito e così via). |
| fare_amount |Imposto della tariffa in |
| surcharge |Sovrapprezzo |
| mta_tax |Tassa MTA |
| tip_amount |Importo delle mance |
| tolls_amount |Importo dei pedaggi |
| total_amount |Importo totale |
| tipped |Mancia lasciata (0/1 per no o sì) |
| tip_class |Categoria mance (0: $ 0, 1: $ 0-5, 2: $ 6-10, 3: $ 11-20, 4: > $ 20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Eseguire il codice da Jupyter Notebook nel cluster Spark
È possibile avviare il notebook di Jupyter dal portale di Azure. Trovare il cluster Spark nel dashboard e fare clic su di esso per aprire la relativa pagina di gestione. Per aprire il notebook associato al cluster Spark, fare clic su **Dashboard cluster** -> **Notebook di Jupyter**.

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-on-portal.png)

Per accedere ai notebook di Jupyter, è anche possibile passare a ***https://NOMECLUSTER.azurehdinsight.net/jupyter***. Sostituire NOMECLUSTER nell'URL con il nome del proprio cluster. Per accedere ai notebook, sarà necessaria la password dell'account amministratore.

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-notebook.png)

Selezionare PySpark per visualizzare una directory con alcuni esempi di notebook predefiniti che usano l'API PySpark. I notebook contenenti gli esempi di codice per questa serie di argomenti su Spark sono disponibili in [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark).

È possibile caricare il notebook direttamente da Github nel server Jupyter Notebook del cluster Spark. Nella home page di Jupyter fare clic sul pulsante **Upload** (Carica) a destra nella schermata. Verrà visualizzata una finestra di esplorazione file, in cui è possibile incollare l'URL di GitHub (contenuto non elaborato) del notebook e fare clic su **Open** (Apri). I notebook di PySpark sono disponibili agli URL seguenti:

1. [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb)
2. [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-model-consumption.ipynb)
3. [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb)

Il nome del file verrà visualizzato nell'elenco di file Jupyter e sarà di nuovo disponibile il pulsante **Upload** (Carica). Fare clic sul pulsante **Upload** (Carica). A questo punto, il notebook è stato importato. Ripetere i passaggi per caricare gli altri notebook di questa procedura dettagliata.

> [!TIP]
> È possibile fare clic con il pulsante destro del mouse sui collegamenti nel browser e scegliere **Copia collegamento** per ottenere l'URL del contenuto non elaborato di GitHub. È quindi possibile incollare questo URL nella finestra di dialogo per il caricamento di file in Jupyter.
> 
> 

A questo punto è possibile:

* Fare clic sul notebook per visualizzare il codice.
* Eseguire ogni cella premendo **MAIUSC+INVIO**.
* Eseguire l'intero notebook facendo clic su **Cell** -> **Run** (Cella -> Esegui).
* Usare la visualizzazione automatica delle query.

> [!TIP]
> Il kernel Pyspark visualizza automaticamente l'output delle query SQL (HiveQL). È possibile scegliere tra diversi tipi di visualizzazioni (tabella, a torta, a linee, ad area o a barre) usando i pulsanti del menu **Type** (Tipo) nel notebook:
> 
> 

![Curva ROC di regressione logistica per approccio generico](./media/machine-learning-data-science-spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="what's-next?"></a>Passaggi successivi
Dopo aver configurato un cluster HDInsight Spark e avere caricato i notebook di Jupyter, è possibile seguire gli argomenti corrispondenti a questi tre notebook di PySpark. Tali argomenti illustrano come esplorare i dati e quindi come creare e utilizzare i modelli. Il notebook relativo a modellazione ed esplorazione avanzate dei dati mostra come includere la convalida incrociata, lo sweep di iperparametri e la valutazione del modello. 

**Esplorazione e modellazione avanzate dei dati con Spark:** esplorare il set di dati ed eseguire operazioni di creazione, assegnazione dei punteggi e valutazione di modelli di Machine Learning seguendo quanto illustrato nell'argomento [Create binary classification and regression models for data with the Spark MLlib toolkit](machine-learning-data-science-spark-data-exploration-modeling.md) (Creare modelli di classificazione binaria e regressione per i dati con il toolkit Spark MLlib).

**Uso dei modelli:** per informazioni su come valutare i modelli di regressione e di classificazione creati in questo argomento, vedere [Assegnare punteggi a modelli di apprendimento automatico compilati con Spark](machine-learning-data-science-spark-model-consumption.md).

**Convalida incrociata e sweep di iperparametri**: vedere [Esplorazione e modellazione avanzate dei dati con Spark](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) per informazioni su come istruire i modelli sulla convalida incrociata e lo sweep di iperparametri

<!--HONumber=Oct16_HO2-->


