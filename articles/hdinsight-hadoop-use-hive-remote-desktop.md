<properties
   pageTitle="Usare Hive di Hadoop in HDInsight | Azure"
   description="Informazioni su come usare Hive con HDInsight tramite Desktop remoto."
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

# Uso di Hive con Hadoop in HDInsight con Desktop remoto

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

In questo articolo si apprenderà come connettersi a un cluster HDInsight tramite Desktop remoto e quindi eseguire query Hive usando l'interfaccia della riga di comando di Hive.

> [AZURE.NOTE] Questo documento non fornisce una descrizione dettagliata delle operazioni eseguite dalle istruzioni HiveQL usate negli esempi. Per informazioni sul codice HiveQL usato in questo esempio, vedere <a href="../hdinsight-use-hive/" target="_blank">Usare Hive con Hadoop in HDInsight</a>.

## <a id="prereq"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster HDInsight (Hadoop in HDInsight) basato su Windows

* Windows 7 o un sistema operativo client più recente

## <a id="connect"></a>Connettersi a Desktop remoto

Abilitare Desktop remoto per il cluster HDInsight e quindi effettuare la connessione seguendo le istruzioni fornite in <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Connettersi a cluster HDInsight tramite RDP</a>.

## <a id="hive"></a>Usare il comando Hive

Una volta connessi al desktop per il cluster HDInsight, seguire questa procedura per l'uso con Hive.

1. Dal desktop di HDInsight, avviare la **riga di comando di Hadoop**.

2. Immettere il seguente comando per avviare l'interfaccia della riga di comando di Hive.

        %hive_home%\bin\hive

    Dopo l'avvio dell'interfaccia della riga di comando, verrà visualizzato il prompt dell'interfaccia della riga di comando di Hive - `hive>`.

3. Usando l'interfaccia della riga di comando, immettere le seguenti istruzioni per creare una nuova tabella denominata **log4jLogs** con i dati di esempio.

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


4. Usare le seguenti istruzioni per creare una nuova tabella  'interna' denominata **errorLogs**.

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

    Queste istruzioni eseguono le azioni seguenti.

    * **CREATE TABLE IF NOT EXISTS**: crea una tabella, se non esiste già. Poiché non viene usata la parola chiave **EXTERNAL**, questa è una tabella interna che viene archiviata nel data warehouse di Hive e gestita completamente da Hive.
    
		> [AZURE.NOTE] A differenza delle tabelle **EXTERNAL**, se si elimina una tabella interna verranno eliminati anche i dati sottostanti.
		
    * **STORED AS ORC**: archivia i dati nel formato ORC (Optimized Row Columnar). Si tratta di un formato di file altamente ottimizzato per l'archiviazione di dati Hive.
    
    * **INSERT OVERWRITE ... SELECT**: seleziona dalla tabella **log4jLogs** le righe contenenti **[ERROR]**, quindi inserisce i dati nella tabella **errorLogs**.

    Per verificare che solo le righe contenenti **[ERROR]** nella colonna t4 siano state archiviate nella tabella **errorLogs**, usare l'istruzione seguente per restituire tutte le righe da **errorLogs**.

        SELECT * from errorLogs;

    Dovrebbero essere restituite tre righe di dati, tutte contenenti **[ERROR]** nella colonna t4.

## <a id="summary"></a>Riepilogo

Come è possibile osservare, il comando Hive fornisce un modo semplice per eseguire query Hive in un cluster HDInsight, monitorare lo stato del processo e recuperare l'output in modo interattivo.

## <a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su Hive in HDInsight.

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight.

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)

* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)


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
