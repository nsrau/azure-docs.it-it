---
title: Panoramica di HDInsight 4.0 - Azure
description: Confrontare le funzionalità di HDInsight 3.6 con quelle di HDInsight 4.0, con informazioni sulle limitazioni e consigli per l'aggiornamento.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: b2e77049d121a11a45a096017f18f1345f6c6884
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85374849"
---
# <a name="azure-hdinsight-40-overview"></a>Panoramica di HDInsight 4.0

Azure HDInsight è uno dei servizi più diffusi fra i clienti enterprise per Apache Hadoop e Apache Spark. HDInsight 4.0 è una distribuzione cloud dei componenti di Apache Hadoop. Questo articolo include informazioni sulla versione più recente di Azure HDInsight e su come eseguire l'aggiornamento.

## <a name="whats-new-in-hdinsight-40"></a>Novità di HDInsight 4.0

### <a name="apache-hive-30-and-low-latency-analytical-processing"></a>Apache Hive 3.0 ed elaborazione analitica a bassa latenza

LLAP (Low-Latency Analytical Processing) di Apache Hive usa server di query persistenti e memorizzazione nella cache in memoria. Questo processo consente di distribuire rapidamente risultati di query SQL sui dati in risorse di archiviazione cloud remote. LLAP di Hive usa un set di daemon persistenti che eseguono frammenti di query Hive. L'esecuzione di query in LLAP è simile ad Hive senza LLAP, con attività di lavoro in esecuzione all'interno di daemon LLAP anziché di contenitori.

Tra i vantaggi di LLAP di Hive sono inclusi i seguenti:

* Possibilità di eseguire analisi SQL approfondite senza sacrificare le prestazioni e l'adattabilità, quali join complessi, sottoquery, funzioni finestra, ordinamento, funzioni definite dall'utente e aggregazioni complesse.

* Query interattive sui dati nella stessa risorsa di archiviazione in cui vengono preparati i dati, eliminando la necessità di spostare dati dall'archiviazione a un altro motore per l'elaborazione analitica.

* La memorizzazione nella cache dei risultati delle query consente di riutilizzare i risultati delle query calcolati in precedenza. Questa cache consente di risparmiare tempo e risorse per l'esecuzione delle attività del cluster necessarie per la query.

### <a name="hive-dynamic-materialized-views"></a>Viste materializzate dinamiche di Hive

Hive supporta ora le viste materializzate dinamiche, o il precalcolo di riepiloghi pertinenti. Le viste accelerano l'elaborazione delle query nei data warehouse. Le viste materializzate possono essere archiviate in modo nativo in Hive e possono usare l'accelerazione LLAP in tutta semplicità.

### <a name="hive-transactional-tables"></a>Tabelle transazionali di Hive

HDI 4.0 include Apache Hive 3. Hive 3 richiede la conformità ad atomicità, consistenza, isolamento e durabilità per le tabelle transazionali che si trovano nel data warehouse di Hive. Hive accede alle tabelle e ai dati di tabella conformi ad ACID e li gestisce. I dati nelle tabelle di creazione, recupero, aggiornamento ed eliminazione (CRUD) devono usare il formato di file ORC (Optimized Row Column). Le tabelle di solo inserimento supportano tutti i formati.

* ACID v2 include miglioramenti per le prestazioni riguardo sia al formato di archiviazione sia al motore di esecuzione.

* ACID è abilitato per impostazione predefinita per offrire il supporto completo per gli aggiornamenti dei dati.

* Le funzionalità migliorate di ACID permettono operazioni di aggiornamento ed eliminazione a livello di riga.

* Nessun overhead delle prestazioni.

* Nessun inserimento in bucket necessario.

* Spark può leggere e scrivere in tabelle ACID tramite il connettore Warehouse di Hive.

Per altre informazioni, vedere [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html).

### <a name="apache-spark"></a>Apache Spark

Apache Spark ottiene tabelle aggiornabili e transazioni ACID con il connettore Warehouse di Hive. Questo connettore di Hive permette di registrare tabelle transazionali di Hive come tabelle esterne in Spark per accedere alla funzionalità transazionale completa. Le versioni precedenti supportano solo la modifica delle partizioni di tabella. Hive Warehouse Connector supporta anche frame di dati di streaming.  Questo processo esegue lo streaming di letture e scritture in tabelle di Hive transazionali e di streaming da Spark.

Gli executor Spark possono connettersi direttamente al daemon LLAP di Hive per recuperare e aggiornare dati in modo transazionale, permettendo ad Hive di mantenere il controllo dei dati.

Apache Spark in HDInsight 4.0 supporta gli scenari seguenti:

* Esecuzione di training di modelli di apprendimento automatico sulla stessa tabella transazionale usata per la creazione di report.
* Uso di transazioni ACID per aggiungere colonne in modo sicuro da Spark ML a una tabella di Hive.
* Esecuzione di un processo di streaming di Spark nel feed di modifiche da una tabella di streaming di Hive.
* Creazione di file ORC direttamente da un processo di streaming strutturato di Spark.

Non è più necessario preoccuparsi di accedere accidentalmente a tabelle transazionali di Hive direttamente da Spark, generando risultati incoerenti, dati duplicati o dati danneggiati. In HDInsight 4.0 le tabelle di Spark e di Hive vengono mantenute in metastore separati. Usare il connettore Data Warehouse di Hive per registrare in modo esplicito tabelle transazionali di Hive come tabelle esterne di Spark.

Per altre informazioni, vedere [Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie 4.3.1 è incluso in HDInsight 4.0 con le modifiche seguenti:

* Oozie non esegue più azioni Hive. L'interfaccia della riga di comando di Hive è stata rimossa e sostituita da BeeLine.

* È possibile escludere dipendenze indesiderate dalla libreria di condivisione aggiungendo un modello di esclusione nel file **job.properties**.

Per altre informazioni, vedere [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdinsight-40"></a>Come eseguire l'aggiornamento a HDInsight 4.0

Testare accuratamente i componenti prima di implementare la versione più recente in un ambiente di produzione. HDInsight 4.0 è disponibile per iniziare il processo di aggiornamento, ma HDInsight 3.6 è l'opzione predefinita per impedire problemi accidentali.

Non è disponibile alcun percorso di aggiornamento supportato dalle versioni precedenti di HDInsight a HDInsight 4.0. Poiché i formati di dati BLOB e metastore sono stati modificati, la versione 4.0 non è compatibile con le versioni precedenti. È importante tenere il nuovo ambiente HDInsight 4.0 separato dall'ambiente di produzione corrente. Se si distribuisce HDInsight 4.0 nell'ambiente corrente, il metastore verrà aggiornato in modo permanente.  

## <a name="limitations"></a>Limitazioni

* HDInsight 4.0 non supporta MapReduce per Apache Hive. Usare invece Apache Tez. Per altre informazioni, vedere [Apache Tez](https://tez.apache.org/).
* HDInsight 4.0 non supporta Apache Storm.
* HDInsight 4,0 non supporta il tipo di cluster ML Services.
* La vista Hive non è più disponibile in HDInsight 4.0.
* L'interprete shell di Apache Zeppelin non è supportato nei cluster Spark e Interactive Query.
* Non è possibile *disabilitare* LLAP in un cluster Spark-LLAP. È possibile solo disattivare LLAP.
* Azure Data Lake Storage Gen2 non può salvare i notebook Jupyter in un cluster Spark.
* Apache Pig viene eseguito in Tez per impostazione predefinita, ma è possibile modificarlo in MapReduce
* L'integrazione di Spark SQL Ranger per la sicurezza di righe e colonne è deprecata
* Spark 2.4 e Kafka 2.1 sono disponibili in HDInsight 4.0, quindi Spark 2.3 e Kafka 1.1 non sono più supportati. Si consiglia di usare Spark 2.4 e Kafka 2.1 e versioni successive in HDInsight 4.0.

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di Azure HDInsight](index.yml)
* [Note sulla versione](hdinsight-release-notes.md)
