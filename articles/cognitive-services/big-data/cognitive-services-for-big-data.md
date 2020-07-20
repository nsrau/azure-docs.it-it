---
title: Servizi cognitivi per Big Data
description: Informazioni su come sfruttare Servizi cognitivi di Azure con grandi set di dati usando Python, Java e Scala. Con Servizi cognitivi per Big data è possibile incorporare modelli intelligenti continuamente migliorabili direttamente nei calcoli di Apache Spark e SQL.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.date: 07/09/2020
ms.author: marhamil
ms.openlocfilehash: 0001002636362a74915f9dae21a6beff52baea63
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86201778"
---
# <a name="azure-cognitive-services-for-big-data"></a>Servizi cognitivi di Azure per Big Data

![Servizi cognitivi di Azure per Big Data](media/cognitive-services-big-data-overview.svg)

Con Servizi cognitivi di Azure per Big Data è possibile incanalare diversi terabyte di dati attraverso Servizi cognitivi usando [Apache Spark&trade;](https://docs.microsoft.com/dotnet/spark/what-is-spark). Con Servizi cognitivi per Big data, è facile creare applicazioni intelligenti su larga scala con qualsiasi archivio dati.

Con Servizi cognitivi per Big data è possibile incorporare modelli intelligenti continuamente migliorabili direttamente nei calcoli di Apache Spark e SQL. Questi strumenti liberano gli sviluppatori dalle operazioni di rete di basso livello, in modo che possano dedicarsi alla creazione di applicazioni intelligenti e distribuite.

## <a name="features-and-benefits"></a>Funzionalità e vantaggi

Con Servizi cognitivi per Big Data è possibile usare servizi di qualsiasi area del mondo, oltre a [Servizi cognitivi in contenitori](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support). I contenitori supportano distribuzioni con connettività bassa o inesistente e con risposte a latenza estremamente bassa. È possibile eseguire Servizi cognitivi in contenitori localmente, direttamente nei nodi di lavoro del cluster Spark, oppure in un agente di orchestrazione esterno, come Kubernetes.

## <a name="supported-services"></a>Servizi supportati

Con [Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/), a cui è possibile accedere tramite API e SDK, gli sviluppatori possono creare applicazioni intelligenti anche senza avere competenze in intelligenza artificiale o data science. Con Servizi cognitivi è possibile creare applicazioni in grado di vedere, ascoltare, parlare, capire e ragionare. Per usare Servizi cognitivi, l'applicazione deve inviare dati al servizio tramite la rete. Una volta ricevuti, il servizio restituisce una risposta intelligente. Per i carichi di lavoro di Big Data sono disponibili i servizi seguenti:

### <a name="vision"></a>Visione

|Nome servizio|Descrizione del servizio|
|:-----------|:------------------|
|[Visione artificiale](https://docs.microsoft.com/azure/cognitive-services/computer-vision/ "Visione artificiale")| Il servizio Visione artificiale consente di accedere ad algoritmi avanzati per l'elaborazione delle immagini e la restituzione di informazioni. |
|[Viso](https://docs.microsoft.com/azure/cognitive-services/face/ "Viso")| Il servizio Viso fornisce l'accesso ad algoritmi viso avanzati consentendo il rilevamento e il riconoscimento degli attributi del viso. |

### <a name="speech"></a>Sintesi vocale

|Nome servizio|Descrizione del servizio|
|:-----------|:------------------|
|[Servizio di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/ "Servizio Voce")|Il servizio Voce fornisce l'accesso a funzionalità come riconoscimento vocale, sintesi vocale, traduzione vocale, oltre a verifica e identificazione del parlante.|

### <a name="decision"></a>Decisione

|Nome servizio|Descrizione del servizio|
|:-----------|:------------------|
|[Rilevamento anomalie](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/ "Rilevamento anomalie") | Il servizio Rilevamento anomalie (anteprima) consente di monitorare e rilevare le anomalie nei dati di serie temporali.|

### <a name="language"></a>Linguaggio

|Nome servizio|Descrizione del servizio|
|:-----------|:------------------|
|[Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/ "Text Analytics")| Il servizio Analisi del testo fornisce l'elaborazione del linguaggio naturale su testo non elaborato per l'analisi del sentiment, l'estrazione delle frasi chiave e il rilevamento della lingua.|

### <a name="search"></a>Ricerca

|Nome servizio|Descrizione del servizio|
|:-----------|:------------------|
|[Ricerca immagini Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search "Ricerca immagini Bing")|Il servizio Ricerca immagini Bing restituisce immagini ritenute pertinenti alla query dell'utente.|

## <a name="supported-programming-languages-for-cognitive-services-for-big-data"></a>Linguaggi di programmazione supportati per Servizi cognitivi per Big Data

Le distribuzioni di Servizi cognitivi per Big Data sono basate su Apache Spark. Apache Spark è una libreria di calcolo distribuito che supporta Java, Scala, Python, R e molti altri linguaggi. Questi linguaggi sono attualmente supportati.

### <a name="python"></a>Python

È disponibile un'API PySpark nello spazio dei nomi `mmlspark.cognitive` di [Microsoft ML per Apache Spark](https://aka.ms/spark). Per altre informazioni, vedere l'[API per sviluppatori Python](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/pyspark/mmlspark.cognitive.html). Per esempi di utilizzo, vedere gli [esempi di Python](samples-python.md).

### <a name="scala-and-java"></a>Scala e Java

È disponibile un'API Spark basata su Scala e Java nello spazio dei nomi `com.microsoft.ml.spark.cognitive` di [Microsoft ML per Apache Spark](https://aka.ms/spark). Per altre informazioni, vedere l'[API per sviluppatori Scala](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/scala/index.html#package). Pe esempi di utilizzo, vedere gli [esempi di Scala](samples-scala.md).

## <a name="supported-platforms-and-connectors"></a>Piattaforme e connettori supportati

Per Servizi cognitivi per Big Data è necessario Apache Spark. Sono disponibili diverse piattaforme Apache Spark che supportano Servizi cognitivi per Big Data.

### <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) è una piattaforma di analisi basata su Apache Spark ottimizzata per la piattaforma dei servizi cloud di Microsoft Azure. Offre procedure di configurazione con un clic, flussi di lavoro semplificati e un'area di lavoro interattiva che consente la collaborazione tra scienziati dei dati, ingegneri dei dati e analisti aziendali.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

[Azure Synapse Analytics](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/synapse-analytics) (in precedenza SQL Data Warehouse) è un data warehouse aziendale che usa l'elaborazione parallela massiccia. Con Synapse Analytics, è possibile eseguire rapidamente query complesse su diversi petabyte di dati. Azure Synapse Analytics fornisce pool Spark gestiti per l'esecuzione di processi Spark con un'interfaccia Jupyter Notebook intuitiva.

### <a name="azure-kubernetes-service"></a>Servizio Azure Kubernetes

Il [servizio Azure Kubernetes](https://docs.microsoft.com/azure/aks/) orchestra i contenitori Docker e le applicazioni distribuite su larghissima scala. Il servizio Azure Kubernetes è un'offerta Kubernetes gestita che semplifica l'uso di Kubernetes in Azure. Kubernetes consente un controllo con granularità fine di scalabilità, latenza e rete di Servizi cognitivi. Tuttavia, se non si ha familiarità con Apache Spark, è consigliabile usare Azure Databricks o Synapse Analytics.

### <a name="data-connectors"></a>Connettori di dati

Una volta configurato un cluster Spark, il passaggio successivo consiste nel connettere i dati. Apache Spark include un'ampia raccolta di connettori di database. Questi connettori consentono alle applicazioni di usare set di dati di grandi dimensioni, indipendentemente dalla posizione in cui sono archiviati. Per altre informazioni sui connettori e i database supportati, vedere l'[elenco di origini dati supportate per Azure Databricks](https://docs.microsoft.com/azure/databricks/data/data-sources/).

## <a name="concepts"></a>Concetti

### <a name="spark"></a>Spark

[Apache Spark&trade;](http://spark.apache.org/) è un motore di analisi unificato per l'elaborazione di dati su larga scala. Il framework di elaborazione parallela migliora le prestazioni delle applicazioni di Big Data e analisi. Spark può funzionare come sistema di elaborazione di batch e flussi di dati, senza cambiare il codice dell'applicazione principale.

La base di Spark è il dataframe, ossia una raccolta tabulare di dati distribuiti tra i nodi di lavoro di Apache Spark. Un dataframe Spark è paragonabile a una tabella di un database relazionale o a un dataframe in R/Python, ma su scala illimitata. I dataframe possono essere creati da varie origini, come file di dati strutturati, tabelle in hive o database esterni. Una volta inseriti i dati in un dataframe Spark, è possibile:

   - Eseguire calcoli di tipo SQL, ad esempio le tabelle di join e filtro.
   - Applicare funzioni a set di dati di grandi dimensioni usando il parallelismo di tipo MapReduce.
   - Applicare Machine Learning distribuito usando Microsoft Machine Learning per Apache Spark.
   - Usare Servizi cognitivi per Big Data per arricchire i dati con servizi intelligenti pronti per l'uso.

### <a name="microsoft-machine-learning-for-apache-spark-mmlspark"></a>Microsoft Machine Learning per Apache Spark (MMLSpark)

[Microsoft Machine Learning for Apache Spark](https://mmlspark.blob.core.windows.net/website/index.html#install) (MMLSpark) è una libreria di Machine Learning (ML) distribuita e open source basata su Apache Spark. Questo pacchetto include Servizi cognitivi per Big Data. Inoltre, MMLSpark contiene diversi altri strumenti di ML per Apache Spark, ad esempio LightGBM, Vowpal Wabbit, OpenCV, LIME e altro ancora. Con MMLSpark è possibile creare modelli predittivi e analitici avanzati da qualsiasi origine dati Spark.

### <a name="http-on-spark"></a>HTTP su Spark

L'esempio di Servizi cognitivi per Big Data dimostra come sia possibile integrare servizi Web intelligenti con Big Data. I servizi Web potenziano molte applicazioni in tutto il mondo e la maggior parte dei servizi comunica tramite HTTP (Hypertext Transfer Protocol). Per usare *servizi Web* arbitrari su larga scala, viene fornito il protocollo HTTP su Spark. Con HTTP su Spark è possibile passare diversi terabyte di dati tramite qualsiasi servizio Web. Dietro le quinte, questa tecnologia viene usata per Servizi cognitivi per Big Data.

## <a name="developer-samples"></a>Esempi per gli sviluppatori

- [Recipe: Manutenzione predittiva](recipes/anomaly-detection.md)
- [Recipe: Esplorazione intelligente di opere d'arte](recipes/art-explorer.md)

## <a name="blog-posts"></a>Post di BLOG

- [Altre informazioni sul funzionamento di Servizi cognitivi in Apache Spark&trade;](https://azure.microsoft.com/blog/dear-spark-developers-welcome-to-azure-cognitive-services/)
- [Salvataggio dei leopardi delle nevi con Deep Learning e Visione artificiale in Spark](http://www.datawizard.io/2017/06/27/saving-snow-leopards-with-deep-learning-and-computer-vision-on-spark/)
- [Podcast di Microsoft Research: MMLSpark, supporto di AI for Good con Mark Hamilton](https://blubrry.com/microsoftresearch/49485070/092-mmlspark-empowering-ai-for-good-with-mark-hamilton/)

## <a name="webinars-and-videos"></a>Webinar e video

- [Servizi cognitivi di Azure in Spark: cluster con servizi intelligenti incorporati](https://databricks.com/session/the-azure-cognitive-services-on-spark-clusters-with-embedded-intelligent-services)
- [Intervento al summit su Spark: AI for Good scalabile](https://databricks.com/session_eu19/scalable-ai-for-good)
- [Servizi cognitivi per Big Data in Cosmos DB](https://medius.studios.ms/Embed/Video-nc/B19-BRK3004?latestplayer=true&l=2571.208093)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione a Servizi cognitivi per Big Data](getting-started.md)
- [Semplici esempi di Python](samples-python.md)
- [Semplici esempi di Scala](samples-scala.md)
