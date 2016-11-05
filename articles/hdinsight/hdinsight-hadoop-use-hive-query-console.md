---
title: Usare Hive di Hadoop in Query Console in HDInsight | Microsoft Docs
description: Informazioni su come usare Query Console basata sul Web per eseguire query Hive in un cluster Hadoop di HDInsight dal browser.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/20/2016
ms.author: larryfr

---
# Eseguire query Hive usando Query Console
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

In questo articolo si apprenderà come usare Query Console per eseguire query Hive in un cluster HDInsight Hadoop dal browser.

> [!IMPORTANT]
> Query Console di HDInsight è disponibile solo nei cluster HDInsight basati su Windows. Se si utilizza un cluster HDInsight basato su Linux, vedere [Eseguire query Hive usando Query Console](hdinsight-hadoop-use-hive-ambari-view.md).
> 
> 

## <a id="prereq"></a>Prerequisiti
Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster HDInsight Hadoop basato su Windows
* Un Web browser moderno

## <a id="run"></a> Eseguire query Hive usando Query Console
1. Aprire un Web browser e passare a **https://CLUSTERNAME.azurehdinsight.net**, dove *_CLUSTERNAME\*\_ è il nome del cluster HDInsight. Quando richiesto, immettere il nome utente e la password usati durante la creazione del cluster.
2. Nei collegamenti nella parte superiore della pagina fare clic su **Hive Editor**. Verrà visualizzato un modulo che può essere usato per immettere le istruzioni HiveQL che si desidera eseguire nel cluster HDInsight.
   
    ![L'editor Hive](./media/hdinsight-hadoop-use-hive-query-console/queryconsole.png)
   
    Sostituire il testo `Select * from hivesampletable` con le seguenti istruzioni HiveQL:
   
        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   
    Di seguito sono elencate le istruzioni che eseguono queste azioni:
   
   * **DROP TABLE**: elimina la tabella e il file di dati, se la tabella esiste già.
   * **CREATE EXTERNAL TABLE**: crea una nuova tabella "external" in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati vengono lasciati nella posizione originale.
     
     > [!NOTE]
     > È consigliabile usare le tabelle esterne quando si prevede che i dati sottostanti vengano aggiornati da un'origine esterna, ad esempio un processo automatico di caricamento dei dati, oppure da un'altra operazione MapReduce, ma si vuole che le query Hive usino sempre i dati più recenti.
     > 
     > L'eliminazione di una tabella esterna **non** comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.
     > 
     > 
   * **ROW FORMAT**: indica a Hive il modo in cui sono formattati i dati. In questo caso, i campi in ogni log sono separati da uno spazio.
   * **STORED AS TEXTFILE LOCATION**: indica a Hive dove sono archiviati i dati (la directory example/data) e che sono archiviati come testo.
   * **SELECT**: seleziona un numero di tutte le righe in cui la colonna **t4** include il valore **[ERROR]**. Dovrebbe restituire un valore pari a **3**, poiché sono presenti tre righe contenenti questo valore.
   * **INPUT\_\_FILE\_\_NAME come '%.log'** -indica ad Hive che si dovrebbero restituire solo i dati da file che terminano con. log. Questo limita la ricerca al file sample. log che contiene i dati, ed evita la restituzione di dati da altri file di dati di esempio che non corrispondono allo schema che è stato definito.
3. Fare clic su **Submit**. L'opzione **Job Session** nella parte inferiore della pagina dovrebbe visualizzare i dettagli del processo.
4. Quando il campo **Status** viene impostato su **Completed**, fare clic su **View Details** per il processo. Nella pagina dei dettagli il campo **Job Output** contiene `[ERROR]    3`. È possibile usare il pulsante **Download** al di sotto di questo campo per scaricare un file contenente l'output del processo.

## <a id="summary"></a>Riepilogo
Come è possibile notare, Query Console fornisce un modo semplice per eseguire query Hive in un cluster HDInsight, monitorare lo stato del processo e recuperare l'output.

Per altre informazioni sull'uso Query Console di Hive per eseguire processi Hive, fare clic su **Getting Started** nella parte superiore di Query Console, quindi usare gli esempi. Ogni esempio illustra il processo di utilizzo di Hive per l'analisi dei dati, fornendo anche spiegazioni sulle istruzioni HiveQL usate nell'esempio.

## <a id="nextsteps"></a>Passaggi successivi
Per informazioni generali su Hive in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Se si usa Tez con Hive, vedere i documenti seguenti per le informazioni di debug:

* [Usare l'interfaccia utente di Tez in HDInsight basato su Windows](hdinsight-debug-tez-ui.md)
* [Usare la vista Ambari Tez in HDInsight basato su Linux](hdinsight-debug-ambari-tez-view.md)

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[Powershell-install-configure]: powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png

<!---HONumber=AcomDC_0921_2016-->