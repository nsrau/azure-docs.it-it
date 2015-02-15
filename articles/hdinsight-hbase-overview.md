<properties 
	pageTitle="Panoramica di HBase in HDInsight | Azure" 
	description="Introduzione a HBase in HDInsight, con informazioni sui casi di utilizzo e un confronto con altre soluzioni di database." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/21/2014" 
	ms.author="bradsev"/>



# Panoramica su HBase di HDInsight #

## Che cos'è HBase? ##

HBase è un database NoSQL open source Apache basato su Hadoop che fornisce accesso rapido e coerenza assoluta per quantità elevate di dati non strutturati e semistrutturati. È basato su BigTable di Google ed è un database a colonne orientato ai tipi di dati. I dati sono archiviati nelle righe di una tabella e i dati di ogni riga sono raggruppati in base al tipo di colonna. HBase è un database privo di schema, poiché non è necessario definire le colonne o i tipi di dati archiviati nelle colonne prima dell'uso. Il codice open source è stato rilasciato per la prima volta da Mike Cafarella nel 2007.  Offre scalabilità lineare, in modo da gestire petabyte di dati in migliaia di nodi. Può contare su ridondanza dei dati, elaborazione batch e altre funzionalità offerte dalle applicazioni distribuite nell'ecosistema di Hadoop.

## Che cos'è HBase di HDInsight di Azure? ##
HBase di HDInsight è offerto come cluster gestito integrato nell'ambiente di Azure. I cluster sono configurati per l'archiviazione dei dati direttamente nell'archiviazione BLOB di Azure, che offre bassa latenza e maggiore flessibilità nelle opzioni relative a prestazioni e costi. Ciò permette ai clienti di creare siti Web interattivi da usare con grandi set di dati, per creare servizi che archiviano dati di sensori e telemetria da milioni di endpoint e per analizzare questi dati tramite processi Hadoop. HBase e Hadoop sono punti di partenza ottimali per progetti Big Data in Azure e, in particolare, possono permettere ad applicazioni in tempo reale di usare set di dati di grandi dimensioni.

L'implementazione di HDInsight usa l'architettura di scalabilità orizzontale di HBase per fornire il partizionamento orizzontale delle tabelle, la coerenza assoluta di letture e scritture e il failover automatico. Le prestazioni sono ottimizzate dalla cache in memoria in fase di lettura e da scritture in flusso a velocità effettiva elevata. Per HBase di HDInsight è disponibile anche il provisioning della rete virtuale. Per informazioni dettagliate, vedere [Effettuare il provisioning di cluster HDInsight in Rete virtuale di Azure][hbase-provision-vnet].

## In che modo sono gestiti i dati in HBase di HDInsight? ##
I dati possono essere gestiti in HBase tramite i comandi `create` `get`, `put` e `scan` della shell di HBase. I dati vengono scritti nel database tramite `put` e vengono letti tramite i comandi `get`. Il comando `scan` è usato per ottenere i dati da più righe in una tabella. I dati possono essere gestiti anche tramite l'API C# di HBase, che offre una libreria client, oltre all'API REST di HBase. È anche possibile eseguire query in un database di HBase tramite Hive. Per informazioni introduttive su questi modelli di programmazione, vedere [Introduzione a HBase con Hadoop in HDInsight][hbase-get-started]. Sono anche disponibili coprocessori che permettono l'elaborazione di dati nei nodi che ospitano il database.
 

## Scenari: Quali sono i casi di utilizzo per HBase? ##
Il caso di utilizzo tipico per cui è stato creato BigTable, e quindi, per estensione, HBase, è costituito dalla ricerca Web. I motori di ricerca costruiscono indice per il mapping di termini alle pagine Web che li contengono. HBase, tuttavia, è adatto a molti altri casi di utilizzo, alcuni dei quali sono indicati in questa sezione.

### Caso di utilizzo n. 1: archivio chiave-valore
HBase può essere usato come archivio chiave-valore ed è adatto alla gestione di sistemi di messaggistica. Facebook usa HBase per il proprio sistema di messaggistica ed è ideale per l'archiviazione e la gestione di comunicazioni Internet. WebTable usa HBase per eseguire ricerche e gestire tabelle estratte da pagine Web.

### Caso di utilizzo n. 2: dati di sensori
HBase è utile per l'acquisizione di dati raccolti in modo incrementale da varie origini, incluse analisi di social media e serie temporali, e permette di mantenere aggiornati i dashboard interattivi con tendenze e contatori e di gestire i sistemi di log di controllo. Alcuni esempi sono costituiti dal terminale dei trader di Bloomberg
e dal database OpenTSDB (Open Time Series Database), che archivia e fornisce accesso alle metriche raccolte sull'integrità dei sistemi di server.


### Caso di utilizzo n. 3: query in tempo reale
[Phoenix](http://phoenix.apache.org/) è un motore di query SQL per Apache HBase. Vi si accede come un'unità JDBC e permette di eseguire query e di gestire le tabelle HBase tramite SQL. 

### Caso di utilizzo n. 4: HBase come piattaforma
Le applicazioni possono essere eseguite su HBase, usato come archivio dati. Alcuni esempi sono Phoenix, OpenTSDB, Kiji e Titan. Le applicazioni possono anche essere integrate con HBase, ad esempio Hive, Pig, Solr, Storm, Flume, Impala, Spark, Ganglia e Drill.


##<a name="next-steps"></a>Passaggi successivi

[Introduzione all'uso di HBase con Hadoop in HDInsight][hbase-get-started]

[Effettuare il provisioning di cluster HDInsight in Rete virtuale di Azure][hbase-provision-vnet]

[Analisi dei sentimenti Twitter con HBase in HDInsight][hbase-twitter-sentiment]

[Usare Maven per compilare applicazioni Java che usano HBase con HDInsight (Hadoop)][hbase-build-java-maven]

[SDK di HBase C#][cs-hbase-sdk]

##<a name="see-also"></a>Vedere anche

[Apache HBase](https://hbase.apache.org/)

[Bigtable: un sistema di archiviazione distribuita per dati strutturati](http://research.google.com/archive/bigtable.html)




[hbase-provision-vnet]: ../hdinsight-hbase-provision-vnet/

[hbase-twitter-sentiment]: ../hdinsight-hbase-analyze-twitter-sentiment/

[hbase-build-java-maven]: ../hdinsight-hbase-build-java-maven/

[hdinsight-use-hive]: ../hdinsight-use-hive/

[hdinsight-storage]: ../hdinsight-use-blob-storage/

[hbase-get-started]: http://azure.microsoft.com/it-it/documentation/articles/hdinsight-hbase-get-started/

[cs-hbase-sdk]: https://github.com/hdinsight/hbase-sdk-for-net

[azure-purchase-options]: http://azure.microsoft.com/it-it/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/it-it/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/it-it/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/ 

[apache-hadoop]: http://hadoop.apache.org/











<!--HONumber=42-->
