---
title: Query Hive con Hadoop Tools per Visual Studio | Documentazione Microsoft
description: Informazioni su come usare Hive con Hadoop in HDInsight tramite gli strumenti di Visual Studio Hadoop.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2b3e672a-1195-4fa5-afb7-b7b73937bfbe
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 8c07f0da21eab0c90ad9608dfaeb29dd4a01a6b7
ms.openlocfilehash: 067725ee5f303fc21baa3204509e8facd6f216fc


---
# <a name="run-hive-queries-using-the-hdinsight-tools-for-visual-studio"></a>Eseguire query Hive usando gli strumenti di HDInsight per Visual Studio

[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

In questo articolo si apprenderà come inviare query Hive a un cluster HDInsight usando HDInsight Tools per Visual Studio.

> [!NOTE]
> Questo documento non fornisce una descrizione dettagliata delle operazioni eseguite dalle istruzioni HiveQL usate negli esempi. Per informazioni sul codice HiveQL usato in questo esempio, vedere [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md).

## <a name="a-idprereqaprerequisites"></a><a id="prereq"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster Azure HDInsight (Hadoop in HDInsight)

  > [!IMPORTANT]
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* Visual Studio (una delle versioni seguenti):
  
    Visual Studio 2013 Community/Professional/Premium/Ultimate con [Update 4](https://www.microsoft.com/download/details.aspx?id=44921)
  
    Visual Studio 2015 (Community/Enterprise)

* Strumenti HDInsight per Visual Studio o Azure Data Lake Tools per Visual Studio. Vedere [Introduzione all'uso di HDInsight Hadoop Tools per Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) per informazioni sull'installazione e la configurazione degli strumenti.

## <a name="a-idruna-run-hive-queries-using-the-visual-studio"></a><a id="run"></a> Eseguire query Hive usando Visual Studio

1. Aprire **Visual Studio** e scegliere **Nuovo** > **Progetto** > **Azure Data Lake** > **HIVE** > **Applicazione Hive**. Specificare un nome per questo progetto.

2. Aprire il file **Script.hql** creato con il progetto e incollarvi le seguenti istruzioni HiveQL:
   
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

   * **ROW FORMAT**: indica a Hive il modo in cui sono formattati i dati. In questo caso, i campi in ogni log sono separati da uno spazio.

   * **STORED AS TEXTFILE LOCATION**: indica a Hive dove sono archiviati i dati (la directory example/data) e che sono archiviati come testo.

   * **SELECT**: seleziona il numero di tutte le righe in cui la colonna **t4** include il valore **[ERROR]**. Dovrebbe restituire un valore pari a **3** , poiché sono presenti tre righe contenenti questo valore.

   * **INPUT__FILE__NAME come '%.log'** - indica a Hive che si dovrebbero restituire solo i dati da file che terminano con .log. Questo limita la ricerca al file sample. log che contiene i dati, ed evita la restituzione di dati da altri file di dati di esempio che non corrispondono allo schema che è stato definito.

3. Nella barra degli strumenti selezionare il **cluster HDInsight** da usare per la query, quindi fare clic su **Submit to WebHCat** (Invia a WebHCat) per eseguire le istruzioni come processo Hive con WebHCat. È anche possibile inviare il processo con il pulsante **Execute via HiveServer2** (Esegui tramite HiveServer2) se HiveServer2 è disponibile nella versione del cluster. Verrà visualizzata una finestra di **riepilogo del processo Hive** con informazioni relative al processo in esecuzione. Usare il collegamento **Aggiorna** per aggiornare le informazioni del processo finché il campo **Stato processo** non viene impostato su **Completato**.

4. Usare il collegamento **Output processo** per visualizzare l'output del processo. Dovrebbe includere `[ERROR] 3`, ovvero il valore restituito dall'istruzione SELECT.

5. È anche possibile eseguire query Hive senza creare un progetto. Usando **Esplora server**, espandere **Azure** > **HDInsight**, fare clic con il pulsante destro del mouse sul server HDInsight, quindi scegliere **Scrivi una query Hive**.

6. Nel documento **temp.hql** che viene visualizzato aggiungere le seguenti istruzioni HiveQL:
   
        set hive.execution.engine=tez;
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   
    Di seguito sono elencate le istruzioni che eseguono queste azioni:
   
   * **CREATE TABLE IF NOT EXISTS**: crea una tabella, se non esiste già. Poiché non viene usata la parola chiave **EXTERNAL** , questa è una tabella interna che viene archiviata nel data warehouse di Hive e gestita completamente da Hive.
     
     > [!NOTE]
     > A differenza delle tabelle **EXTERNAL** , se si elimina una tabella interna, vengono eliminati anche i dati sottostanti.

   * **STORED AS ORC**: archivia i dati nel formato ORC (Optimized Row Columnar). Questo è un formato altamente ottimizzato ed efficiente per l'archiviazione di dati Hive.

   * **INSERT OVERWRITE ... SELECT**: seleziona dalla tabella **log4jLogs** le righe contenenti **[ERROR]**, quindi inserisce i dati nella tabella **errorLogs**.

7. Nella barra degli strumenti selezionare **Invia** per eseguire il processo. Usare **Stato processo** per accertarsi che il processo sia stato completato correttamente.

8. Per verificare che il processo sia stato completato e che sia stata creata una nuova tabella, usare **Esplora server** ed espandere **Azure** > **HDInsight** > cluster HDInsight > **Hive Databases** (Database Hive) > e **Default** (Predefinito). Dovrebbero essere visibili la tabella **errorLogs** e la tabella **log4jLogs**.

## <a name="a-idsummaryasummary"></a><a id="summary"></a>Riepilogo

Come è possibile notare, HDInsight Tools per Visual Studio fornisce un modo semplice per eseguire query Hive in un cluster HDInsight, monitorare lo stato del processo e recuperare l'output.

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su Hive in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)

* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Per altre informazioni su HDInsight Tools per Visual Studio:

* [Introduzione all'uso di HDInsight Tools per Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

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

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png



<!--HONumber=Jan17_HO3-->


