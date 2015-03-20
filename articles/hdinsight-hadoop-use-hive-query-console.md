<properties
   pageTitle="Usare Hive di Hadoop in HDInsight | Azure"
   description="Informazioni su come usare Hive con la console di query (Query Console) di HDInsight basata sul Web."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Esecuzione di query Hive mediante Query Console

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

In questo articolo si apprenderà come usare Query Console per eseguire query Hive in un cluster HDInsight Hadoop dal browser.

> [AZURE.NOTE] Query Console è disponibile solo nei cluster HDInsight basati su Windows.

## <a id="prereq"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster HDInsight Hadoop basato su Windows

* Un Web browser moderno

## <a id="run"></a> Eseguire query Hive usando Query Console

1. Aprire il <a href="https://manage.windowsazure.com" target="_blank">portale di gestione di Azure</a> e selezionare il cluster HDInsight. Nella parte inferiore della pagina selezionare **Query Console**. Quando richiesto, immettere il nome utente e la password usati durante la creazione del cluster.

    > [AZURE.NOTE] È possibile accedere a Query Console anche immettendo **https://NOMECLUSTER.azurehdinsight.net** nel browser.

2. Nei collegamenti nella parte superiore della pagina fare clic su **Hive Editor**. Verrà visualizzato un modulo che può essere usato per immettere le istruzioni HiveQL che si desidera eseguire nel cluster HDInsight. 
	
	![the hive editor](./media/hdinsight-hadoop-use-hive-query-console/queryconsole.png)
	
	Sostituire il testo  `Select * from hivesampletable` con le seguenti istruzioni HiveQL.

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

    Le istruzioni eseguono queste azioni:

    * **DROP TABLE**: elimina la tabella e il file di dati, nel caso in cui la tabella esista già.
    * **CREATE EXTERNAL TABLE**: crea una nuova tabella 'esterna' in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati rimangono nella posizione originale.

    > [AZURE.NOTE] È consigliabile usare le tabelle esterne quando si prevede che i dati sottostanti vengano aggiornati da un'origine esterna, ad esempio un processo automatico di caricamento dei dati, oppure da un'altra operazione MapReduce, ma si vuole che le query Hive usino sempre i dati più recenti.
    >
    > L'eliminazione di una tabella esterna **non** comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.

    * **ROW FORMAT**: indica a Hive il modo in cui sono formattati i dati. In questo caso i campi di ogni log sono separati da uno spazio.
    * **STORED AS TEXTFILE LOCATION**: indica a Hive dove sono archiviati i dati (directory example/data) e specifica che i dati sono archiviati come testo.
    * **SELECT**: seleziona un numero di tutte le righe in cui la colonna **t4** include il valore **[ERROR]**. Dovrebbe restituire un valore pari a **3**, poiché sono presenti tre righe contenenti questo valore.

2. Fare clic su **Submit**. L'opzione **Job Session** nella parte inferiore della pagina dovrebbe visualizzare i dettagli del processo.

3. Quando il campo **Status** si imposta su **Completed**, fare clic su **View Details** per il processo. Nella pagina dei dettagli il campo **Job Output** conterrà `[ERROR]	3`. È possibile usare il pulsante **Download** al di sotto di questo campo per scaricare un file contenente l'output del processo.


## <a id="summary"></a>Riepilogo

Come è possibile notare, Query Console fornisce un modo semplice per eseguire query Hive in un cluster HDInsight, monitorare lo stato del processo e recuperare l'output. 

Per altre informazioni sull'uso di Hive con Query Console, fare clic su **Getting Started** nella parte superiore di Query Console, quindi usare gli esempi. Ogni esempio illustra il processo di analisi dei dati con Hive, fornendo anche spiegazioni delle istruzioni HiveQL usate nell'esempio.

## <a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su Hive in HDInsight.

* [Usare Hive con Hadoop in HDInsight](../hdinsight-use-hive/)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight.

* [Usare Pig con Hadoop in HDInsight](../hdinsight-use-pig/)

* [Usare MapReduce con Hadoop in HDInsight](../hdinsight-use-mapreduce/)

[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/



[hdinsight-storage]: ../hdinsight-use-blob-storage

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/

[Powershell-install-configure]: ../install-configure-powershell/
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!--HONumber=47-->
