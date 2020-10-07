---
title: Informazioni su Apache HBase in Azure HDInsight
description: Introduzione ad Apache HBase in HDInsight, un database NoSQL basato su Hadoop. Informazioni sui casi di utilizzo e confronto di HBase con altri cluster Hadoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: afbf9aff09999a34a84d55634a868250fbb6d1ff
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "82188961"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Informazioni su Apache HBase in Azure HDInsight

[Apache HBase](https://hbase.apache.org/) è un database NoSQL open source basato su Apache Hadoop e modellato su [Google BigTable](https://cloud.google.com/bigtable/). HBase offre accesso casuale e coerenza assoluta per quantità elevate di dati in un database senza schema. Il database è organizzato per famiglie di colonne.

Dal punto di vista dell'utente, HBase è simile a un database. I dati sono archiviati nelle righe e nelle colonne di una tabella e i dati di ogni riga sono raggruppati in base al tipo di colonna. HBase è un database senza schema. Non è necessario definire colonne e tipi di dati prima di usarli. Il codice open source offre scalabilità lineare, in modo da gestire petabyte di dati in migliaia di nodi. Può contare su ridondanza dei dati, elaborazione batch e altre funzionalità offerte dalle applicazioni distribuite nell'ambiente di Hadoop.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Modalità di implementazione di Apache HBase in Azure HDInsight

HBase di HDInsight è offerto come cluster gestito integrato nell'ambiente di Azure. I cluster sono configurati per archiviare i dati direttamente in [Archiviazione di Azure](./../hdinsight-hadoop-use-blob-storage.md), che offre bassa latenza e maggiore flessibilità nelle opzioni relative a prestazioni e costi. Questa proprietà consente ai clienti di creare siti Web interattivi compatibili con set di dati di grandi dimensioni, allo scopo di creare servizi che archiviano dati di sensori e telemetria da milioni di endpoint e di analizzare questi dati con processi Hadoop. HBase e Hadoop costituiscono validi punti di partenza per progetti Big Data in Azure. Grazie ai servizi è possibile consentire ad applicazioni in tempo reale di usare set di dati di grandi dimensioni.

L'implementazione di HDInsight usa l'architettura di scale-out di HBase per offrire il partizionamento orizzontale automatico delle tabelle, oltre a coerenza assoluta per operazioni di lettura e scrittura e failover automatico. Le prestazioni sono ottimizzate dalla cache in memoria per le operazioni di lettura e da flussi a velocità effettiva elevata per quelle di scrittura. È possibile creare un cluster HBase in una rete virtuale. Per informazioni dettagliate, vedere [Creare cluster HDInsight nella rete virtuale di Azure](./apache-hbase-provision-vnet.md).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Modalità di gestione dei dati in HBase di HDInsight

I dati possono essere gestiti in HBase tramite i comandi `create`, `get`, `put` e `scan` dalla shell di HBase. I dati vengono scritti nel database tramite `put` e letti tramite `get`. Il comando `scan` viene usato per ottenere i dati da più righe in una tabella. I dati possono essere gestiti anche tramite l'API C# di HBase, che offre una libreria client, oltre all'API REST di HBase. È anche possibile eseguire query in un database di HBase tramite [Apache Hive](https://hive.apache.org/). Per informazioni introduttive su questi modelli di programmazione, vedere [Introduzione a Apache HBase con Apache Hadoop in HDInsight](./apache-hbase-tutorial-get-started-linux.md). Sono anche disponibili coprocessori che consentono l'elaborazione dei dati nei nodi che ospitano il database.

> [!NOTE]  
> Thrift non è supportato da HBase in HDInsight.

## <a name="use-cases-for-apache-hbase"></a>casi di utilizzo per Apache HBase

Il caso di utilizzo tipico per cui è stato creato BigTable, e per estensione HBase, dalla ricerca Web. I motori di ricerca costruiscono indici per il mapping di termini alle pagine Web che li contengono. Tuttavia, HBase è adatto a molti altri casi di utilizzo, alcuni dei quali sono descritti in dettaglio in questa sezione.

|Scenario |Descrizione |
|---|---|
|Archivio chiave-valore|HBase può essere usato come archivio di tipo chiave-valore ed è adatto alla gestione di sistemi di messaggistica. Facebook usa HBase per il proprio sistema di messaggistica ed è ideale per l'archiviazione e la gestione delle comunicazioni Internet. WebTable usa HBase per eseguire ricerche e gestire tabelle estratte da pagine Web.|
|Dati di sensori|HBase è utile per l'acquisizione di dati raccolti in modo incrementale da varie origini, incluse analisi di social media e serie temporali, e permette di mantenere aggiornati i dashboard interattivi con tendenze e contatori e di gestire i sistemi di log di controllo. Alcuni esempi includono il terminale per trader di Bloomberg e Open Time Series Database (OpenTSDB). OpenTSDB archivia e offre l'accesso alle metriche raccolte sullo dei sistemi server.|
|Query in tempo reale|[Apache Phoenix](https://phoenix.apache.org/) è un motore di query SQL per Apache HBase. Vi si accede mediante un driver JDBC e permette di eseguire query e di gestire le tabelle HBase tramite SQL.|
|HBase come piattaforma|Le applicazioni possono essere eseguite su HBase, usato come un archivio dati. Alcuni esempi sono Phoenix, OpenTSDB, `Kiji` e Titan. Le applicazioni possono anche essere integrate con HBase. Gli esempi includono: [Apache Hive](https://hive.apache.org/), Apache Pig, [Solr](https://lucene.apache.org/solr/), Apache Storm, Apache Flume, [Apache Impala](https://impala.apache.org/), Apache Spark, `Ganglia` e Apache Drill.|

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione all'uso di Apache HBase con Apache Hadoop in HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Creare cluster HDInsight nella rete virtuale di Azure](./apache-hbase-provision-vnet.md)
* [Configurare la replica geografica di Apache HBase in HDInsight](apache-hbase-replication.md)
