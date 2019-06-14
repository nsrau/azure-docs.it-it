---
title: Apache Hive con strumenti Data Lake (Apache Hadoop) per Visual Studio - Azure HDInsight
description: Informazioni su come usare gli strumenti Data Lake per Visual Studio per eseguire query Apache Hive con Apache Hadoop in Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: hrasheed
ms.openlocfilehash: 7480dafe435e555bfba81ebd9242bb5724c0bf3f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65861589"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Eseguire query Apache Hive usando gli strumenti Data Lake per Visual Studio

Informazioni su come usare gli strumenti Data Lake per Visual Studio per eseguire query su Apache Hive. Gli strumenti Data Lake consentono di creare, inviare e monitorare facilmente query Hive in Apache Hadoop in Azure HDInsight.

## <a id="prereq"></a>Prerequisiti

* Un cluster Apache Hadoop in HDInsight. Vedere [Guida introduttiva: Introduzione ad Apache Hadoop e Apache Hive in Azure HDInsight usando il modello di Resource Manager](./apache-hadoop-linux-tutorial-get-started.md).

* Visual Studio (una delle versioni seguenti):

    * Visual Studio 2015, 2017 (qualsiasi edizione)
    * Visual Studio 2013 Community/Professional/Premium/Ultimate con Update 4

* Strumenti HDInsight per Visual Studio o Azure Data Lake Tools per Visual Studio. Vedere [Introduzione all'uso di HDInsight Hadoop Tools per Visual Studio](apache-hadoop-visual-studio-tools-get-started.md) per informazioni sull'installazione e la configurazione degli strumenti.

## <a id="run"></a> Eseguire query Apache Hive usando Visual Studio

Per la creazione e l'esecuzione di query Hive sono disponibili due opzioni:

* Creare query ad hoc
* Creare un'applicazione Hive

### <a name="ad-hoc"></a>Ad hoc

È possibile eseguire query ad hoc in uno **Batch** oppure **Interactive** modalità.

1. Aprire **Visual Studio**.

2. Dal **Esplora Server**, passare alla **Azure** > **HDInsight**.

3. Espandere **HDInsight**e fare doppio clic su cluster in cui si desidera eseguire la query e quindi selezionare **Scrivi una Query Hive**.

4. Immettere la query hive seguente:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Scegliere **Execute**(Esegui). Si noti che la modalità di esecuzione viene impostato sul valore predefinita **Interactive**.

    ![Screenshot dell'esecuzione di query Interactive Hive](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Per eseguire la stessa query in **Batch** modalità, Attiva/Disattiva elenco a discesa elenco dal **Interactive** al **Batch**. Si noti che il pulsante di esecuzione viene modificato da **Execute** al **Submit**.

    ![Screenshot del processo di invio di una query Hive](./media/apache-hadoop-use-hive-visual-studio/vs-batch-query.png)

    L'editor Hive supporta IntelliSense. Strumenti Data Lake per Visual Studio supporta il caricamento di metadati remoti quando si modifica lo script Hive. Ad esempio, se si digita `SELECT * FROM`, IntelliSense Elenca tutti i nomi di tabella suggeriti. Quando si specifica un nome di tabella, IntelliSense elenca i nomi delle colonne. Gli strumenti supportano la maggior parte delle funzioni definite dall'utente predefinite, delle sottoquery e delle istruzioni DML Hive. IntelliSense suggerisce solo i metadati del cluster selezionato nella barra degli strumenti HDInsight.

    ![Screenshot 1 di un esempio di IntelliSense in Strumenti di Visual Studio in HDInsight](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-table-name.png "IntelliSense in U-SQL")
   
    ![Screenshot 2 di un esempio di IntelliSense in Strumenti di Visual Studio in HDInsight](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-column-name.png "IntelliSense in U-SQL")

7. Selezionare **Submit** (Invio) o **Submit (Advanced)** (Invio - Avanzato).

   Se si seleziona l'opzione di invio avanzato, configurare le impostazioni per **Nome processo**, **Argomenti**, **Configurazioni aggiuntive** e **Directory di stato** per lo script:

    ![Screenshot di una query Hive Hadoop HDInsight](./media/apache-hadoop-use-hive-visual-studio/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Inviare query")

### <a name="hive-application"></a>Applicazione di hive

1. Aprire **Visual Studio**.

2. Dalla barra dei menu, passare a **File** > **New** > **progetto**.

3. Dal **nuovo progetto** finestra, passare alla **modelli** > **Azure Data Lake** > **HIVE (HDInsight)**  >  **Hive Application**. 

4. Specificare un nome per questo progetto e quindi selezionare **OK**.

5. Aprire il file **Script.hql** creato con il progetto e incollarvi le seguenti istruzioni HiveQL:

    ```hiveql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Le istruzioni eseguono queste azioni:

   * `DROP TABLE`: Se la tabella esiste, questa istruzione la elimina.

   * `CREATE EXTERNAL TABLE`: Crea una nuova tabella "esterna" in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati vengono lasciati nella posizione originale.

     > [!NOTE]  
     > Usa le tabelle esterne se si prevede che i dati sottostanti verranno aggiornati da un'origine esterna. Ad esempio, un processo MapReduce o un servizio di Azure.
     >
     > L'eliminazione di una tabella esterna **non** comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.

   * `ROW FORMAT`: Indica a Hive il modo in cui sono formattati i dati. In questo caso, i campi in ogni log sono separati da uno spazio.

   * `STORED AS TEXTFILE LOCATION`: Indica a Hive che i dati sono archiviati nella directory example/data e che sono archiviati come testo.

   * `SELECT`: Seleziona un numero di tutte le righe in cui la colonna `t4` include il valore `[ERROR]`. L'istruzione dovrebbe restituire un valore pari a `3`, poiché sono presenti tre righe contenenti questo valore.

   * `INPUT__FILE__NAME LIKE '%.log'`: indica a Hive che si dovrebbero restituire solo i dati da file che terminano con .log. Questa clausola limita la ricerca al file sample.log che contiene i dati.

6. Dalla barra degli strumenti, selezionare il **Cluster HDInsight** che si desidera usare per la query. Selezionare **Invia** per eseguire le istruzioni come processo Hive.

   ![Barra di invio](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

7. Verrà visualizzata una finestra di **riepilogo del processo Hive** con informazioni relative al processo in esecuzione. Usare il collegamento **Aggiorna** per aggiornare le informazioni del processo finché il campo **Stato processo** non viene impostato su **Completato**.

   ![riepilogo del processo che mostra un processo completato](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

8. Usare il collegamento **Output processo** per visualizzare l'output del processo. Mostra `[ERROR] 3`, ovvero il valore restituito dalla query.

### <a name="additional-example"></a>Altro esempio

In questo esempio si basa sul `log4jLogs` tabella creata nel passaggio precedente.

1. Dal **Esplora Server**, fare clic sul cluster e selezionare **Scrivi una Query Hive**.

2. Immettere la query hive seguente:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Le istruzioni eseguono queste azioni:

    * `CREATE TABLE IF NOT EXISTS`: Crea una tabella, se non esiste già. Poiché la parola chiave `EXTERNAL` non viene usata, questa istruzione crea una tabella interna. Le tabelle interne vengono archiviate nel data warehouse di Hive e sono gestite da Hive.
    
    > [!NOTE]  
    > A differenza delle tabelle `EXTERNAL`, se si elimina una tabella interna, vengono eliminati anche i dati sottostanti.

    * `STORED AS ORC`: Archivia i dati nel formato ORC, Optimized Row Columnar. ORC è un formato altamente ottimizzato ed efficiente per l'archiviazione di dati Hive.
    
    * `INSERT OVERWRITE ... SELECT`: Seleziona le righe della tabella `log4jLogs` contenenti `[ERROR]`, quindi inserisce i dati nella tabella `errorLogs`.

3. Esecuzione della query nella **Batch** modalità.

4. Per verificare che il processo abbia creato la tabella, usare **Esplora server** ed espandere **Azure** > **HDInsight** > il proprio cluster HDInsight > **Database Hive** > **Predefinito**. Vengono elencate la tabella **errorLogs** e la tabella **log4jLogs**.

## <a id="nextsteps"></a>Passaggi successivi

Come si può notare, gli strumenti HDInsight per Visual Studio forniscono un modo semplice per lavorare con le query Hive in HDInsight.

Per informazioni generali su Hive in HDInsight:

* [Usare Apache Hive con Apache Hadoop su HDInsight](hdinsight-use-hive.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Apache Pig con Apache Hadoop su HDInsight](hdinsight-use-pig.md)

* [Usare MapReduce con Apache Hadoop su HDInsight](hdinsight-use-mapreduce.md)

Per altre informazioni su HDInsight Tools per Visual Studio:

* [Introduzione all'uso di HDInsight Tools per Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)