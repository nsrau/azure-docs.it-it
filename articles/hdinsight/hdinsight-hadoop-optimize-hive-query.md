---
title: Ottimizzare le query Hive in Azure HDInsight | Microsoft Docs
description: Informazioni su come ottimizzare le query Hive per Hadoop in HDInsight
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d6174c08-06aa-42ac-8e9b-8b8718d9978e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/03/2017
ms.author: jgao
ms.openlocfilehash: 4bd7f5b584030f9c1554b56895493837d2eac357
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2017
---
# <a name="optimize-hive-queries-in-azure-hdinsight"></a>Ottimizzare le query Hive in Azure HDInsight

Per impostazione predefinita, i cluster Hadoop non sono ottimizzati per le prestazioni. Questo articolo descrive alcuni metodi di ottimizzazione delle prestazioni Hive più comuni che è possibile applicare alle query.

## <a name="scale-out-worker-nodes"></a>Scalabilità orizzontale dei nodi di lavoro

Aumentando il numero di nodi di lavoro in un cluster è possibile usare più mapper e reducer da eseguire in parallelo. Esistono due modi per aumentare la scalabilità orizzontale in HDInsight:

* In fase di provisioning è possibile specificare il numero di nodi di lavoro usando il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando multipiattaforma.  Per altre informazioni, vedere [Creare cluster Hadoop in HDInsight](hdinsight-hadoop-provision-linux-clusters.md). La schermata seguente mostra la configurazione dei nodi del ruolo di lavoro nel portale di Azure:
  
    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]
* In fase di esecuzione è anche possibile scalare orizzontalmente un cluster senza ricreare uno:

    ![scaleout_1][image-hdi-optimize-hive-scaleout_2]

Per altre informazioni sulle diverse macchine virtuali supportate da HDInsight, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="enable-tez"></a>Abilitare Tez

[Apache Tez](http://hortonworks.com/hadoop/tez/) è un motore di esecuzione alternativo al motore di MapReduce:

![tez_1][image-hdi-optimize-hive-tez_1]

Tez è più veloce perché:

* **Esegue un grafo aciclico diretto (DAG) come un singolo processo nel motore di MapReduce**. Il DAG richiede che ogni set di mapper sia seguito da un set di reducer. In questo modo, più processi MapReduce vengono eseguiti per ciascuna query Hive. Tez non presenta questo vincolo e può elaborare DAG complessi con un unico processo, riducendo così il sovraccarico di avvio del processo.
* **Evita scritture non necessarie**. Poiché più processi vengono eseguiti per la stessa query Hive nel motore di MapReduce, l'output di ogni processo viene scritto in HDFS per dati intermedi. Poiché Tez riduce il numero di processi per ciascuna query Hive è in grado di evitare scritture non necessarie.
* **Riduce al minimo i ritardi di avvio**. Tez è in grado di ridurre al minimo il ritardo di avvio limitando il numero di mapper da avviare e migliorando anche l'ottimizzazione complessiva.
* **Riusa i contenitori**. Quando possibile, Tez è in grado di riusare i contenitori per assicurare che la latenza dovuta all'avvio dei contenitori venga ridotta.
* **Usa tecniche di ottimizzazione continua**. In genere l'ottimizzazione viene eseguita durante la fase di compilazione. Tuttavia, sono disponibili ulteriori informazioni sugli input che consentono una migliore ottimizzazione durante il runtime. Tez usa tecniche di ottimizzazione continua che consentono di ottimizzare ulteriormente il piano nella fase di runtime.

Per altre informazioni su questi concetti, vedere [Apache TEZ](http://hortonworks.com/hadoop/tez/).

È possibile eseguire qualsiasi query Hive a cui Tez è abilitato anteponendovi l'impostazione seguente:

    set hive.execution.engine=tez;

I cluster HDInsight basati su Linux hanno Tez abilitato per impostazione predefinita.


## <a name="hive-partitioning"></a>Partizionamento Hive

L'operazione di I/O rappresenta il principale collo di bottiglia delle prestazioni per l'esecuzione di query Hive. Le prestazioni possono essere migliorate se è possibile ridurre la quantità di dati da leggere. Per impostazione predefinita, le query Hive analizzano intere tabelle Hive. Questa operazione è utile per query come le analisi di tabella, ma per le query che devono analizzare solo una piccola quantità di dati, ad esempio le query con filtro, viene creato un sovraccarico non necessario. Il partizionamento Hive consente alle query Hive di accedere solo alla quantità di dati presente nelle tabelle Hive necessaria.

Il partizionamento Hive viene implementato riorganizzando i dati non elaborati in nuove directory in cui ciascuna partizione dispone di una propria directory, dove la partizione è definita dall'utente. Il diagramma seguente illustra il partizionamento di una tabella Hive mediante la colonna *Anno*. Viene creata una nuova directory per ogni anno.

![partizionamento][image-hdi-optimize-hive-partitioning_1]

Alcune considerazioni sul partizionamento:

* **Non creare un numero eccessivamente ridotto di partizioni**: il partizionamento in colonne con pochi valori può causare un numero ridotto di partizioni. Il partizionamento in base al sesso, ad esempio, crea solo due partizioni (maschio e femmina) e riduce quindi la latenza al massimo solo della metà.
* **Non creare un numero eccessivo di partizioni**: al contrario, la creazione di una partizione in una colonna con un valore univoco (ad esempio ID utente) causa più partizioni generando un sovraccarico nel nodo dei nomi del cluster, che dovrà gestire una grande quantità di directory.
* **Evitare lo sfasamento di dati** : scegliere la chiave di partizionamento con attenzione, in modo che tutte le partizioni siano di dimensioni pari. Ad esempio, con il partizionamento in *Stato* , il numero di record in California potrebbe essere quasi 30 volte quello del Vermont, a causa della differenza di popolazione.

Per creare una tabella di partizione, usare la clausola *Partizionato da* :

    CREATE TABLE lineitem_part
        (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE            STRING, L_SHIPINSTRUCT STRING, L_SHIPMODE STRING,
         L_COMMENT STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE;

Una volta creata la tabella partizionata, è possibile creare il partizionamento statico o il partizionamento dinamico.

* **Partizionamento statico** indica che i dati sono già stati partizionati nelle directory appropriate ed è possibile richiedere le partizioni Hive manualmente, in base al percorso di directory. Il frammento di codice seguente rappresenta un esempio.
  
        INSERT OVERWRITE TABLE lineitem_part
        PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
        SELECT * FROM lineitem 
        WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’
  
        ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
        LOCATION ‘wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
* **Partizionamento dinamico** indica che si desidera che Hive crei le partizioni automaticamente per l'utente. Poiché la tabella di partizionamento è già stata creata dalla tabella di gestione temporanea, è sufficiente inserire i dati nella tabella partizionata.
  
        SET hive.exec.dynamic.partition = true;
        SET hive.exec.dynamic.partition.mode = nonstrict;
        INSERT INTO TABLE lineitem_part
        PARTITION (L_SHIPDATE)
        SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
             L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
              L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
             L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as           L_RETURNFLAG, L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as           L_SHIPDATE_PS, L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as      L_RECEIPTDATE, L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as      L_SHIPMODE, L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;

Per altre informazioni, vedere [Partitioned Tables](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables) (Tabelle partizionate).

## <a name="use-the-orcfile-format"></a>Utilizzare il formato ORCFile
Hive supporta diversi formati di file. Ad esempio:

* **Testo**: questo è il formato di file predefinito e funziona con la maggior parte degli scenari
* **Avro**: funziona bene per scenari di interoperabilità
* **ORC/Parquet**: particolarmente indicato per le prestazioni

Il formato ORC (Optimized Row Columnar) è un modo particolarmente efficace per archiviare i dati Hive. Rispetto ad altri formati, ORC offre i vantaggi seguenti:

* supporto per tipi complessi, inclusi i tipi complessi e semistrutturati e DateTime
* fino al 70% di compressione
* indici ogni 10.000 righe, che consentono di ignorare le righe
* significativa riduzione in fase di esecuzione

Per abilitare il formato ORC, creare innanzitutto una tabella con la clausola *Archiviato come ORC*:

    CREATE TABLE lineitem_orc_part
        (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
         L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    STORED AS ORC;

Inserire quindi i dati nella tabella ORC dalla tabella di gestione temporanea. Ad esempio:

    INSERT INTO TABLE lineitem_orc
    SELECT L_ORDERKEY as L_ORDERKEY, 
           L_PARTKEY as L_PARTKEY , 
           L_SUPPKEY as L_SUPPKEY,
           L_LINENUMBER as L_LINENUMBER,
            L_QUANTITY as L_QUANTITY, 
           L_EXTENDEDPRICE as L_EXTENDEDPRICE,
           L_DISCOUNT as L_DISCOUNT,
           L_TAX as L_TAX,
           L_RETURNFLAG as L_RETURNFLAG,
           L_LINESTATUS as L_LINESTATUS,
           L_SHIPDATE as L_SHIPDATE,
           L_COMMITDATE as L_COMMITDATE,
           L_RECEIPTDATE as L_RECEIPTDATE, 
           L_SHIPINSTRUCT as L_SHIPINSTRUCT,
           L_SHIPMODE as L_SHIPMODE,
           L_COMMENT as L_COMMENT
    FROM lineitem;

Ulteriori informazioni sul formato ORC sono disponibili [qui](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Vettorizzazione

La vettorizzazione consente ad Hive di elaborare un batch di 1024 righe insieme invece di elaborare una riga alla volta. Ciò significa che le operazioni semplici vengono eseguite più rapidamente poiché è necessario eseguire una quantità inferiore di codice interno.

Per abilitare la vettorizzazione, anteporre alla query Hive la seguente impostazione:

    set hive.vectorized.execution.enabled = true;

Per altre informazioni, vedere [Esecuzione di query vettorizzate](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Altri metodi di ottimizzazione
Esistono altri metodi di ottimizzazione che è possibile considerare, ad esempio:

* **Hive bucket:** una tecnica che consente di raggruppare o segmentare grandi set di dati per ottimizzare le prestazioni delle query.
* **Ottimizzazione join:** ottimizzazione dell'esecuzione di query Hive pianificata per migliorare l'efficienza di join e ridurre la necessità di suggerimenti dell'utente. Per altre informazioni, vedere [Ottimizzazione join](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Aumento dei reducer**.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono stati illustrati vari metodi di ottimizzazione delle query comuni di Hive. Per altre informazioni, vedere gli articoli seguenti:

* [Usare Apache Hive in HDInsight](hadoop/hdinsight-use-hive.md)
* [Analizzare i dati sui ritardi dei voli mediante Hive in HDInsight](hdinsight-analyze-flight-delay-data.md)
* [Analizzare i dati Twitter mediante Hive in HDInsight](hdinsight-analyze-twitter-data.md)
* [Analizzare i dati dei sensori mediante Hive Query Console su Hadoop in HDInsight](hadoop/apache-hive-analyze-sensor-data.md)
* [Usare Hive con HDInsight per analizzare i log dei siti Web](hadoop/apache-hive-analyze-website-log.md)

[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png
