
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
   ms.date="02/18/2016"
   ms.author="masimms"/>

# Elasticsearch in Informazioni aggiuntive di Azure

Elasticsearch è un motore di ricerca e un database open source altamente scalabile. È adatto a situazioni che richiedono velocità nell'analisi e nell'individuazione di informazioni in set di dati di grandi dimensioni. Questa guida esamina alcuni aspetti importanti da considerare durante la progettazione di un cluster Elasticsearch:

- **[Esecuzione di Elasticsearch in Azure][]** include una breve introduzione alla struttura generale di Elasticsearch e descrive come implementare un cluster Elasticsearch con Azure.
- L'articolo sull'**[ottimizzazione delle prestazioni dell'inserimento dati per Elasticsearch in Azure][]** descrive le opzioni di distribuzione e di configurazione da considerare per un cluster Elasticsearch che prevede una frequenza elevata di inserimento dati.
- L'articolo sull'**[ottimizzazione delle prestazioni dell'aggregazione dati e delle query per Elasticsearch in Azure][]** riepiloga le opzioni che è possibile prendere in considerazione al momento di determinare il modo migliore di ottimizzare le prestazioni del sistema per le query e la ricerca.
- L'articolo sulla **[configurazione della flessibilità e del ripristino per Elasticsearch in Azure][]** riepiloga le opzioni relative alla flessibilità e al ripristino disponibili per Elasticsearch in Azure.
- L'articolo sulla **[creazione di un ambiente di test delle prestazioni per Elasticsearch in Azure][]** illustra come configurare un ambiente per i test delle prestazioni di un cluster Elasticsearch.
- **[Implementazione di un piano di test di JMeter per Elasticsearch][]** descrive come creare e usare un campionatore JUnit in grado di generare e caricare i dati in un cluster Elasticsearch.
- L'articolo sulla **[distribuzione di un campionatore JUnit di JMeter per il test delle prestazioni di Elasticsearch][]** riepiloga le principali esperienze acquisite durante la creazione e l'esecuzione di piani di test di query e di inserimento dati. 
- L'articolo sull'**[esecuzione dei test automatizzati della flessibilità di Elasticsearch][]** riepiloga l'esecuzione dei test di flessibilità usati nell'articolo precedente.
- L'articolo sull'**[esecuzione dei test automatizzati delle prestazioni di Elasticsearch][]** riepiloga l'esecuzione dei test delle prestazioni usati nell'articolo precedente.

> [AZURE.NOTE] Questa guida presuppone una certa conoscenza di base di Elasticsearch. Per informazioni più dettagliate, visitare il [sito Web Elasticsearch](https://www.elastic.co/products/elasticsearch).

[Esecuzione di Elasticsearch in Azure]: guidance-elasticsearch-running-on-azure.md
[ottimizzazione delle prestazioni dell'inserimento dati per Elasticsearch in Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[creazione di un ambiente di test delle prestazioni per Elasticsearch in Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
[Implementazione di un piano di test di JMeter per Elasticsearch]: guidance-elasticsearch-implementing-jmeter-test-plan.md
[distribuzione di un campionatore JUnit di JMeter per il test delle prestazioni di Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[ottimizzazione delle prestazioni dell'aggregazione dati e delle query per Elasticsearch in Azure]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[configurazione della flessibilità e del ripristino per Elasticsearch in Azure]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[esecuzione dei test automatizzati della flessibilità di Elasticsearch]: guidance-elasticsearch-running-automated-resilience-tests.md
[esecuzione dei test automatizzati delle prestazioni di Elasticsearch]: guidance-elasticsearch-running-automated-performance-tests.md

<!---HONumber=AcomDC_0224_2016-->