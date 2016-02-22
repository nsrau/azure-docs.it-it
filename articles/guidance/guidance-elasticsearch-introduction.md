
<properties
   pageTitle="Elasticsearch in Informazioni aggiuntive di Azure | Microsoft Azure"
   description="Elasticsearch in Informazioni aggiuntive di Azure."
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

# Elasticsearch in Informazioni aggiuntive di Azure

Elasticsearch è un motore di ricerca e un database open source altamente scalabile. È adatto a situazioni che richiedono velocità nell'analisi e nell'individuazione di informazioni in set di dati di grandi dimensioni. Questa guida esamina alcuni aspetti importanti da considerare durante la progettazione di un cluster Elasticsearch:

- La pagina con le **[linee guida generali][]** include una breve introduzione alla struttura generale di Elasticsearch e descrive come implementare un cluster Elasticsearch con Azure.
- La pagina con le **[linee guida per l'inserimento di dati][]** descrive le opzioni di distribuzione e di configurazione da considerare per un cluster Elasticsearch che prevede una frequenza elevata di inserimento dati.
- La pagina con le **[linee guida per i test delle prestazioni][]** illustra come configurare un ambiente per i test delle prestazioni di un cluster Elasticsearch.
- La pagina con le **[linee guida per JMeter][]** descrive come creare e usare un campionatore JUnit in grado di generare e caricare i dati in un cluster Elasticsearch.
- La pagina con le **[considerazioni su JMeter][]** riepiloga le principali esperienze acquisite durante la creazione e l'esecuzione di piani di test di query e di inserimento dati. 

  > [AZURE.NOTE] Questa guida presuppone una certa conoscenza di base di Elasticsearch. Per informazioni più dettagliate, visitare il [sito Web di Elasticsearch](https://www.elastic.co/products/elasticsearch).

[linee guida generali]: guidance-elasticsearch.md
[linee guida per l'inserimento di dati]: guidance-elasticsearch-data-ingestion.md
[linee guida per i test delle prestazioni]: guidance-elasticsearch-performance-testing-environment.md
[linee guida per JMeter]: guidance-elasticsearch-implementing-jmeter.md
[considerazioni su JMeter]: guidance-elasticsearch-deploy-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0211_2016-->