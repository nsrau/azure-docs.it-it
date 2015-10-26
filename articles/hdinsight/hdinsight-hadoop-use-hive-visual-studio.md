<properties
   pageTitle="Query Hive con Hadoop Tools per Visual Studio | Microsoft Azure"
   description="Informazioni su come usare Hive con Hadoop in HDInsight tramite gli strumenti di Visual Studio Hadoop."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/09/2015"
   ms.author="larryfr"/>

#Eseguire query Hive usando gli strumenti di HDInsight per Visual Studio

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

In questo articolo si apprenderà come inviare query Hive a un cluster HDInsight usando HDInsight Tools per Visual Studio.

> [AZURE.NOTE]Questo documento non fornisce una descrizione dettagliata delle operazioni eseguite dalle istruzioni HiveQL usate negli esempi. Per informazioni sul codice HiveQL usato in questo esempio, vedere [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md).

##<a id="prereq"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster Azure HDInsight (Hadoop in HDInsight) (basato su Linux o su Windows)

* Visual Studio 2012 [Update 4](http://www.microsoft.com/download/details.aspx?id=39305), Visual Studio 2013 [Update 3](http://go.microsoft.com/fwlink/?LinkId=390465) o [Visual Studio Express 2013](http://www.microsoft.com/download/details.aspx?id=40769)

##<a id="run"></a> Eseguire query Hive usando HDInsight Tools per Visual Studio

1. Aprire **Visual Studio**, quindi scegliere **Nuovo** > **Progetto** > **HDInsight** > **Hive Application**. Specificare un nome per questo progetto.

2. Aprire il file **Script.hql** creato con il progetto e incollarvi le seguenti istruzioni HiveQL:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Le istruzioni eseguono queste azioni:

    * **DROP TABLE**: elimina la tabella e il file di dati, se la tabella esiste già.
    * **CREATE EXTERNAL TABLE**: crea una nuova tabella "external" in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati vengono lasciati nella posizione originale.

        > [AZURE.NOTE]È consigliabile usare le tabelle esterne quando si prevede che i dati sottostanti vengano aggiornati da un'origine esterna, ad esempio un processo automatico di caricamento dei dati, oppure da un'altra operazione MapReduce, ma si vuole che le query Hive usino sempre i dati più recenti.
        >
        > L'eliminazione di una tabella esterna **non** comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.

    * **ROW FORMAT**: indica a Hive il modo in cui sono formattati i dati. In questo caso, i campi in ogni log sono separati da uno spazio.
    * **STORED AS TEXTFILE LOCATION**: indica a Hive dove sono archiviati i dati (la directory example/data) e che sono archiviati come testo.
    * **SELECT**: seleziona un numero di tutte le righe in cui la colonna **t4** include il valore **[ERROR]**. Dovrebbe restituire un valore pari a **3**, poiché sono presenti tre righe contenenti questo valore.
    * **INPUT\_\_FILE\_\_NAME come '%.log'** -indica ad Hive che si dovrebbero restituire solo i dati da file che terminano con. log. Questo limita la ricerca al file sample. log che contiene i dati, ed evita la restituzione di dati da altri file di dati di esempio che non corrispondono allo schema che è stato definito.

3. Nella barra degli strumenti selezionare il **cluster HDInsight** che si desidera usare per la query, quindi fare clic su **Invia** per eseguire le istruzioni come processo Hive. Verrà visualizzata una finestra di **riepilogo del processo Hive** con informazioni relative al processo in esecuzione. Usare il collegamento **Aggiorna** per aggiornare le informazioni del processo finché il campo **Stato processo** non viene imposto su **Completato**.

4. Usare il collegamento **Output processo** per visualizzare l'output del processo. Dovrebbe includere `[ERROR] 3`, ovvero il valore restituito dall'istruzione SELECT.

5. È anche possibile eseguire query Hive senza creare un progetto. Usando **Esplora server**, espandere **Azure** > **HDInsight**, fare clic con il pulsante destro del mouse sul server HDInsight, quindi scegliere **Write a Hive Query**.

6. Nel documento **temp.hql** che viene visualizzato aggiungere le seguenti istruzioni HiveQL:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Le istruzioni eseguono queste azioni:

    * **CREATE TABLE IF NOT EXISTS**: crea una tabella, se non esiste già. Poiché non viene usata la parola chiave **EXTERNAL**, questa è una tabella interna che viene archiviata nel data warehouse di Hive e gestita completamente da Hive.

        > [AZURE.NOTE]A differenza delle tabelle **EXTERNAL**, se si elimina una tabella interna, vengono eliminati anche i dati sottostanti.

    * **STORED AS ORC**: archivia i dati nel formato ORC (Optimized Row Columnar). Questo è un formato altamente ottimizzato ed efficiente per l'archiviazione di dati Hive.
    * **INSERT OVERWRITE ... SELECT**: seleziona dalla tabella **log4jLogs** le righe contenenti **[ERROR]**, quindi inserisce i dati nella tabella **errorLogs**.

7. Nella barra degli strumenti selezionare **Invia** per eseguire il processo. Usare **Stato processo** per accertarsi che il processo sia stato completato correttamente.

8. Per verificare che il processo sia stato completato e che sia stata creata una nuova tabella, usare **Esplora server** ed espandere **Azure** > **HDInsight** > il cluster HDInsight > **Hive Databases** > e **predefinito**. Dovrebbero essere visibili la tabella **errorLogs** e la tabella **log4jLogs**.

##<a id="summary"></a>Riepilogo

Come è possibile notare, HDInsight Tools per Visual Studio fornisce un modo semplice per eseguire query Hive in un cluster HDInsight, monitorare lo stato del processo e recuperare l'output.

##<a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su Hive in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)

* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Per altre informazioni su HDInsight Tools per Visual Studio:

* [Introduzione all'uso di HDInsight Tools per Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)


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



[hdinsight-storage]: hdinsight-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-get-started.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!---HONumber=Oct15_HO3-->