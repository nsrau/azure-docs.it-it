---
title: Errore di memoria insufficiente - Impostazioni Hive | Documentazione Microsoft
description: Correggere un errore di memoria insufficiente da una query Hive in Hadoop in HDInsight. Lo scenario del cliente fa riferimento a una query in molte tabelle di grandi dimensioni.
keywords: errore di memoria insufficiente, OOM, impostazioni Hive
services: hdinsight
documentationcenter: 
author: rashimg
manager: jhubbard
editor: cgronlun
ms.assetid: 7bce3dff-9825-4fa0-a568-c52a9f7d1dad
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/22/2017
ms.author: rashimg;jgao
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: bf0ff13a2d5ffc5bf0b07b80f482fc4144b0cd0f
ms.lasthandoff: 11/17/2016


---
# <a name="fix-an-out-of-memory-oom-error-with-hive-memory-settings-in-hadoop-in-azure-hdinsight"></a>Correggere un errore di memoria insufficiente con le impostazioni di memoria di Hive in Hadoop in Azure HDInsight
Uno dei problemi comuni dei clienti è la ricezione di un errore di memoria insufficiente quando usano Hive. Questo articolo descrive uno scenario del cliente e le impostazioni di Hive consigliate per risolvere il problema.

## <a name="scenario-hive-query-across-large-tables"></a>Scenario: Query Hive in tabelle di grandi dimensioni
Un cliente ha eseguito la query seguente usando Hive.

    SELECT
        COUNT (T1.COLUMN1) as DisplayColumn1,
        …
        …
        ….
    FROM
        TABLE1 T1,
        TABLE2 T2,
        TABLE3 T3,
        TABLE5 T4,
        TABLE6 T5,
        TABLE7 T6
    where (T1.KEY1 = T2.KEY1….
        …
        …

Dettagli della query:

* T1 è l'alias di una tabella di grandi dimensioni, TABLE1, che ha molti tipi di colonna STRING.
* Le altre tabelle non hanno tali dimensioni, ma dispongono di un numero elevato di colonne.
* Tutte le tabelle sono unite tra loro, in alcuni casi con più colonne in TABLE1 e altre.

Quando il cliente ha eseguito la query usando Hive in MapReduce in un cluster A3 a 24 nodi, la query è stata eseguita in circa 26 minuti. Il cliente ha notato i messaggi di avviso seguenti quando è stata eseguita la query usando Hive in MapReduce:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Poiché la query è stata eseguita in circa 26 minuti, il cliente ha ignorato tali avvisi e ha invece iniziato a concentrarsi su come migliorare ulteriormente le prestazioni della query.

Il cliente ha consultato [Ottimizzare le query Hive per Hadoop in HDInsight](hdinsight-hadoop-optimize-hive-query.md)e ha deciso di usare il motore di esecuzione Tez. Quando la stessa query è stata eseguita con l'impostazione Tez abilitata, il tempo di esecuzione è stato di 15 minuti e quindi ha generato l'errore seguente:

    Status: Failed
    Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
    org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
    org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
    Caused by: java.lang.OutOfMemoryError: Java heap space

Il cliente ha quindi deciso di usare una macchina virtuale di dimensioni maggiori (ad esempio D12), ritenendo che una macchina virtuale più grande disponesse di più spazio nell'heap. Nonostante ciò, il cliente ha continuato a visualizzare l'errore. Il cliente ha contattato il team di HDInsight per assistenza nella risoluzione del problema.

## <a name="debug-the-out-of-memory-oom-error"></a>Debug dell'errore di memoria insufficiente
Il supporto Microsoft insieme al team di progettazione ha rilevato che uno dei problemi che provocava l'errore di memoria insufficiente era un [problema noto descritto in Apache JIRA](https://issues.apache.org/jira/browse/HIVE-8306). Dalla descrizione in JIRA:

    When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesnt take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

Controllando il file hive-site.xml, è stato verificato che **hive.auto.convert.join.noconditionaltask** era effettivamente impostato su **true**:

    <property>
        <name>hive.auto.convert.join.noconditionaltask</name>
        <value>true</value>
        <description>
              Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
              If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
              specified size, the join is directly converted to a mapjoin (there is no conditional task).
        </description>
      </property>

In base all'avviso e a JIRA, l'ipotesi è che Map Join fosse la causa dell'errore di memoria insufficiente nello spazio dell'heap di Java. Il problema è stato quindi esaminato più a fondo.

Come illustrato nel post di blog sulle [impostazioni della memoria Yarn di Hadoop in HDInsight](http://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), quando si usa il motore di esecuzione Tez lo spazio dell'heap effettivamente usato appartiene al contenitore Tez. Vedere l'immagine seguente che descrive la memoria del contenitore Tez.

![Diagramma della memoria del contenitore Tez: errore di memoria insufficiente di Hive](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

Come suggerisce il post di blog, le due impostazioni di memoria seguenti definiscono la memoria del contenitore per l'heap: **hive.tez.container.size** e **hive.tez.java.opts**. In base all'esperienza attuale, l'eccezione di memoria insufficiente non è correlata alle dimensioni ridotte del contenitore. Ossia, ad essere ridotte solo le dimensioni dell'heap di Java (hive.tez.java.opts). Pertanto ogni volta che viene visualizzato un errore di memoria insufficiente, è possibile provare ad aumentare **hive.tez.java.opts**. Se necessario, può essere necessario aumentare **hive.tez.container.size**. L'impostazione **java.opts** deve essere circa l'80% di **container.size**.

> [!NOTE]
> L'impostazione **hive.tez.java.opts** deve sempre essere inferiore a **hive.tez.container.size**.
> 
> 

Poiché una macchina D12 ha una memoria di 28 GB, si è deciso di usare una dimensione del contenitore di 10 GB (10.240 MB) e assegnare l'80% a java.opts. Questa operazione è stata eseguita nella console di Hive mediante l'impostazione seguente:

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

In base a queste impostazioni, la query è stata eseguita in meno di dieci minuti.

## <a name="conclusion-oom-errors-and-container-size"></a>Conclusione: errori di memoria insufficiente e dimensioni dei contenitori
Un errore di memoria insufficiente non indica necessariamente che le dimensioni del contenitore sono troppo piccole. Al contrario, è necessario configurare le impostazioni della memoria in modo che le dimensioni dell'heap aumentino e raggiungano almeno l'80% delle dimensioni della memoria del contenitore.


