---
title: Note sulla versione di Azure HDInsight
description: Note sulla versione più recente di Azure HDInsight. Ottenere suggerimenti e dettagli sullo sviluppo per Hadoop, Spark, R Server, Hive e altro.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.openlocfilehash: ba3c7304b0ce52cb720a9e2ac7ae6cf1b5ffeeea
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751465"
---
# <a name="azure-hdinsight-release-notes"></a>Note sulla versione di Azure HDInsight

Questo articolo include informazioni sugli aggiornamenti di versione di Azure HDInsight **più recenti**. Per informazioni sulle versioni precedenti, vedere [Archivio delle note di versione di HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Summary

Azure HDInsight è uno dei servizi più diffusi fra i clienti enterprise per analisi open source in Azure.

## <a name="release-date-08092020"></a>Data di rilascio: 08/09/2020

Questa versione è valida solo per HDInsight 4,0. La versione di HDInsight è resa disponibile per tutte le aree in diversi giorni. La data di release riportata indica la data di rilascio di release della prima area. Se non vengono visualizzate le modifiche riportate di seguito, attendere che la versione risieda nella propria area in diversi giorni.

## <a name="new-features"></a>Nuove funzionalità
### <a name="support-for-sparkcruise"></a>Supporto per SparkCruise
SparkCruise è un sistema di riutilizzo automatico dei calcoli per Spark. Consente di selezionare sottoespressioni comuni per la materializzazione in base al carico di lavoro della query precedente. SparkCruise materializza queste sottoespressioni come parte dell'elaborazione di query e il riutilizzo dei calcoli viene applicato automaticamente in background. È possibile trarre vantaggio da SparkCruise senza alcuna modifica al codice Spark.
 
### <a name="support-hive-view-for-hdinsight-40"></a>Supporto della visualizzazione hive per HDInsight 4,0
Apache Ambari hive View è progettato per semplificare la creazione, l'ottimizzazione e l'esecuzione di query hive dal Web browser. La visualizzazione hive è supportata in modo nativo per i cluster HDInsight 4,0 a partire da questa versione. Non si applica ai cluster esistenti. È necessario eliminare e ricreare il cluster per ottenere la visualizzazione hive incorporata.
 
### <a name="support-tez-view-for-hdinsight-40"></a>Supporta la visualizzazione Tez per HDInsight 4,0
Apache Tez View viene usato per tenere traccia ed eseguire il debug dell'esecuzione del processo Tez hive. La vista TeZ è supportata in modo nativo per HDInsight 4,0 a partire da questa versione. Non si applica ai cluster esistenti. È necessario eliminare e ricreare il cluster per ottenere la visualizzazione Tez incorporata.

## <a name="deprecation"></a>Deprecazione
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Deprecazione di Spark 2.1 e 2.2 in un cluster Spark HDInsight 3.6
A partire dal 1 2020 luglio, i clienti non possono creare nuovi cluster Spark con Spark 2,1 e 2,2 in HDInsight 3,6. I cluster esistenti verranno eseguiti così come sono, senza il supporto di Microsoft. Valutare il passaggio a Spark 2.3 in HDInsight 3.6 entro il 30 giugno 2020 per evitare potenziali interruzioni del sistema o del supporto.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Deprecazione di Spark 2.3 nel cluster Spark HDInsight 4.0
A partire dal 1 2020 luglio, i clienti non possono creare nuovi cluster Spark con Spark 2,3 in HDInsight 4,0. I cluster esistenti verranno eseguiti così come sono, senza il supporto di Microsoft. Valutare il passaggio a Spark 2.4 in HDInsight 4.0 entro il 30 giugno 2020 per evitare potenziali interruzioni del sistema o del supporto.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Deprecazione di Kafka 1.1 nel cluster Kafka di HDInsight 4.0
A partire dal 1° luglio 2020, i clienti non potranno creare nuovi cluster Kafka con Kafka 1.1 in HDInsight 4.0. I cluster esistenti verranno eseguiti così come sono, senza il supporto di Microsoft. Valutare il passaggio a Kafka 2.1 in HDInsight 4.0 entro il 30 giugno 2020 per evitare potenziali interruzioni del sistema o del supporto.

## <a name="behavior-changes"></a>Modifiche del comportamento
### <a name="ambari-stack-version-change"></a>Modifica della versione dello stack di Ambari
Da questa versione, la versione di Ambari è cambiata da 2. x. x. x a 4,1. È possibile ottenere la versione di Ambari dall'interfaccia utente di Ambari > informazioni su.

## <a name="upcoming-changes"></a>Modifiche imminenti
Nessun imminente cambiamento di rilievo a cui è necessario prestare attenzione.

## <a name="bug-fixes"></a>Correzioni di bug
HDInsight continua a migliorare l'affidabilità e le prestazioni del cluster. 

Di seguito sono riportati i portali di JIRAs per hive:
* [HIVE-23619](https://issues.apache.org/jira/browse/HIVE-23619)
* [HIVE-21223](https://issues.apache.org/jira/browse/HIVE-21223)
* [HIVE-22599](https://issues.apache.org/jira/browse/HIVE-22599)
* [HIVE-22121](https://issues.apache.org/jira/browse/HIVE-22121)
* [HIVE-22136](https://issues.apache.org/jira/browse/HIVE-22136)
* [HIVE-18786](https://issues.apache.org/jira/browse/HIVE-18786)

Al di sotto di JIRAs è stato eseguito il porting per HBase:
* [HBASE-21458](https://issues.apache.org/jira/browse/HBASE-21458)
* [HBASE-24208](https://issues.apache.org/jira/browse/HBASE-24208)
* [HBASE-24205](https://issues.apache.org/jira/browse/HBASE-24205)

## <a name="component-version-change"></a>Modifica della versione dei componenti
Questa release non prevede alcuna modifica della versione dei componenti. È possibile trovare le versioni dei componenti correnti per HDInsight 4,0 e HDInsight 3,6 in [questo documento](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).

## <a name="known-issues"></a>Problemi noti

Quando si prova a creare un cluster Azure HDInsight dalla portale di Azure e si usa un tipo di autenticazione SSH della chiave pubblica, si verifica un errore quando si fa clic su **Verifica + crea**. L'errore nel portale è "non deve contenere tre caratteri consecutivi dal nome utente SSH". Questo problema è stato risolto. Tuttavia, se si verifica questo errore, la soluzione alternativa consiste nel creare un cluster con un modello ARM. 
