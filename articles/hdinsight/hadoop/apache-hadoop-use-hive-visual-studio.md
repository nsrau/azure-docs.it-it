---
title: Hive con strumenti Data Lake (Hadoop) per Visual Studio - Azure HDInsight | Microsoft Docs
description: Informazioni su come usare gli strumenti Data Lake per Visual Studio per eseguire query Apache Hive con Apache Hadoop in Azure HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2b3e672a-1195-4fa5-afb7-b7b73937bfbe
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: larryfr
ms.openlocfilehash: b1d9fbfbf2fc1a17611842ff11409769058e9fba
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="run-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Eseguire query Hive usando gli strumenti Data Lake per Visual Studio

Informazioni su come usare gli strumenti Data Lake per Visual Studio per eseguire query su Apache Hive. Gli strumenti Data Lake consentono di creare, inviare e monitorare facilmente query Hive in Hadoop in HDInsight di Azure.

## <a id="prereq"></a>Prerequisiti

* Un cluster Azure HDInsight (Hadoop in HDInsight)

  > [!IMPORTANT]
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Visual Studio (una delle versioni seguenti):

    * Visual Studio 2013 Community/Professional/Premium/Ultimate con Update 4

    * Visual Studio 2015, qualsiasi edizione

    * Visual Studio 2017, qualsiasi edizione

* Strumenti HDInsight per Visual Studio o Azure Data Lake Tools per Visual Studio. Vedere [Introduzione all'uso di HDInsight Hadoop Tools per Visual Studio](apache-hadoop-visual-studio-tools-get-started.md) per informazioni sull'installazione e la configurazione degli strumenti.

## <a id="run"></a> Eseguire query Hive usando Visual Studio

1. Aprire **Visual Studio** e scegliere **Nuovo** > **Progetto** > **Azure Data Lake** > **HIVE** > **Applicazione Hive**. Specificare un nome per questo progetto.

2. Aprire il file **Script.hql** creato con il progetto e incollarvi le seguenti istruzioni HiveQL:

   ```hiveql
   set hive.execution.engine=tez;
   DROP TABLE log4jLogs;
   CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
   STORED AS TEXTFILE LOCATION '/example/data/';
   SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   ```

    Di seguito sono elencate le istruzioni che eseguono queste azioni:

   * `DROP TABLE`: se la tabella esiste, questa istruzione la elimina.

   * `CREATE EXTERNAL TABLE`: crea una nuova tabella "esterna" in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati vengono lasciati nella posizione originale.

     > [!NOTE]
     > Usa le tabelle esterne se si prevede che i dati sottostanti verranno aggiornati da un'origine esterna. Ad esempio, un processo MapReduce o un servizio di Azure.
     >
     > L'eliminazione di una tabella esterna **non** comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.

   * `ROW FORMAT`: indica a Hive il modo in cui sono formattati i dati. In questo caso, i campi in ogni log sono separati da uno spazio.

   * `STORED AS TEXTFILE LOCATION`: indica a Hive che i dati sono archiviati nella directory example/data e che sono archiviati come testo.

   * `SELECT`: seleziona un numero di tutte le righe in cui la colonna `t4` include il valore `[ERROR]`. L'istruzione dovrebbe restituire un valore pari a `3`, poiché sono presenti tre righe contenenti questo valore.

   * `INPUT__FILE__NAME LIKE '%.log'`: indica a Hive che si dovrebbero restituire solo i dati da file che terminano con .log. Questa clausola limita la ricerca al file sample.log che contiene i dati.

3. Dalla barra degli strumenti, selezionare il **Cluster HDInsight** che si desidera usare per la query. Selezionare **Invia** per eseguire le istruzioni come processo Hive.

   ![Barra di invio](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

4. Verrà visualizzata una finestra di **riepilogo del processo Hive** con informazioni relative al processo in esecuzione. Usare il collegamento **Aggiorna** per aggiornare le informazioni del processo finché il campo **Stato processo** non viene impostato su **Completato**.

   ![riepilogo del processo che mostra un processo completato](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

5. Usare il collegamento **Output processo** per visualizzare l'output del processo. Mostra `[ERROR] 3`, ovvero il valore restituito dalla query.

6. È anche possibile eseguire query Hive senza creare un progetto. Usando **Esplora server**, espandere **Azure** > **HDInsight**, fare clic con il pulsante destro del mouse sul server HDInsight, quindi scegliere **Scrivi una query Hive**.

7. Nel documento **temp.hql** che viene visualizzato aggiungere le seguenti istruzioni HiveQL:

   ```hiveql
   set hive.execution.engine=tez;
   CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
   INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   ```

    Di seguito sono elencate le istruzioni che eseguono queste azioni:

   * `CREATE TABLE IF NOT EXISTS`: crea una tabella, se non esiste già. Poiché la parola chiave `EXTERNAL` non viene usata, questa istruzione crea una tabella interna. Le tabelle interne vengono archiviate nel data warehouse di Hive e sono gestite da Hive.

     > [!NOTE]
     > A differenza delle tabelle `EXTERNAL`, se si elimina una tabella interna, vengono eliminati anche i dati sottostanti.

   * `STORED AS ORC`: archivia i dati nel formato ORC, Optimized Row Columnar. ORC è un formato altamente ottimizzato ed efficiente per l'archiviazione di dati Hive.

   * `INSERT OVERWRITE ... SELECT`: seleziona le righe della tabella `log4jLogs` contenenti `[ERROR]`, quindi inserisce i dati nella tabella `errorLogs`.

8. Nella barra degli strumenti selezionare **Invia** per eseguire il processo. Usare **Stato processo** per accertarsi che il processo sia stato completato correttamente.

9. Per verificare che il processo abbia creato la tabella, usare **Esplora server** ed espandere **Azure** > **HDInsight** > il proprio cluster HDInsight > **Database Hive** > **Predefinito**. Vengono elencate la tabella **errorLogs** e la tabella **log4jLogs**.

## <a id="nextsteps"></a>Passaggi successivi

Come si può notare, gli strumenti HDInsight per Visual Studio forniscono un modo semplice per lavorare con le query Hive in HDInsight.

Per informazioni generali su Hive in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)

* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Per altre informazioni su HDInsight Tools per Visual Studio:

* [Introduzione all'uso di HDInsight Tools per Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)

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

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
