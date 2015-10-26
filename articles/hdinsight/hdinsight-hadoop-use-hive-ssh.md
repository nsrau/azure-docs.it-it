<properties
   pageTitle="Usare Hive di Hadoop e SSH in HDInsight | Microsoft Azure"
   description="Informazioni su come usare SSH per connettersi a un cluster Hadoop in HDInsight e quindi inviare in modo interattivo query Hive usando l'interfaccia della riga di comando di Hive."
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

#Uso di Hive con Hadoop in HDInsight tramite SSH

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

In questo articolo si apprenderà come usare SSH (Secure Shell) per connettersi a un cluster Hadoop in Azure HDInsight e quindi inviare in modo interattivo query Hive mediante l'interfaccia della riga di comando di Hive.

> [AZURE.IMPORTANT]Anche se il comando Hive è disponibile nei cluster HDInsight basati su Linux, è consigliabile utilizzare Beeline. Beeline è un client più recente per l'utilizzo di Hive ed è incluso con il cluster HDInsight. Per ulteriori informazioni sull'utilizzo di questo, vedere [Utilizzo di Hive con Hadoop in HDInsight con Beeline](hdinsight-hadoop-use-hive-beeline.md).

##<a id="prereq"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster Hadoop basato su Linux in HDInsight.

* Un client SSH. Linux, Unix e Mac OS dovrebbero essere dotati di un client SSH. Per gli utenti di Windows è necessario scaricare un client, ad esempio [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Connettersi con SSH

Connettersi al nome di dominio completo (FQDN) del cluster HDInsight usando il comando SSH. L'FQDN è costituito dal nome assegnato al cluster, seguito da **.azurehdinsight.net**. Ad esempio, il seguente comando stabilirà la connessione a un cluster denominato **myhdinsight**:

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se è stata fornita una chiave del certificato per l'autenticazione SSH** durante la creazione del cluster HDInsight, potrebbe essere necessario specificare il percorso della chiave privata nel sistema client:

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Se è stata specificata una password per l'autenticazione SSH** durante la creazione del cluster HDInsight, sarà necessario fornire la password quando richiesto.

Per altre informazioni sull'uso di SSH con HDInsight, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###PuTTY (client basati su Windows)

Windows non fornisce un client SSH incorporato. È consigliabile usare **PuTTY**, disponibile per il download all'indirizzo [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Per altre informazioni sull'uso di PuTTY, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hive"></a>Usare il comando Hive

2. Dopo la connessione, avviare l'interfaccia della riga di comando di Hive mediante il comando seguente:

        hive

3. Usando l'interfaccia della riga di comando, immettere le seguenti istruzioni per creare una nuova tabella denominata **log4jLogs** con i dati di esempio:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Le istruzioni eseguono queste azioni:

    * **DROP TABLE**: elimina la tabella e il file di dati, qualora la tabella esista già.
    * **CREATE EXTERNAL TABLE**: crea una nuova tabella "external" in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati rimangono nel percorso originale.
    * **ROW FORMAT**: indica a Hive il modo in cui sono formattati i dati. In questo caso, i campi in ogni log sono separati da uno spazio.
    * **STORED AS TEXTFILE LOCATION**: indica a Hive dove sono archiviati i dati (la directory example/data) e che sono archiviati come testo.
    * **SELECT**: seleziona un numero di tutte le righe in cui la colonna **t4** include il valore **[ERROR]**. Dovrebbe restituire un valore pari a **3**, poiché sono presenti tre righe contenenti questo valore.
    * **INPUT\_\_FILE\_\_NAME come '%.log'** -indica ad Hive che si dovrebbero restituire solo i dati da file che terminano con. log. Questo limita la ricerca al file sample. log che contiene i dati, ed evita la restituzione di dati da altri file di dati di esempio che non corrispondono allo schema che è stato definito.

    > [AZURE.NOTE]È consigliabile usare le tabelle esterne quando si prevede che i dati sottostanti vengano aggiornati da un'origine esterna, ad esempio un processo automatico di caricamento dei dati, oppure da un'altra operazione MapReduce, ma si vuole che le query Hive usino sempre i dati più recenti.
    >
    > L'eliminazione di una tabella esterna **non** comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.

4. Usare le seguenti istruzioni per creare una nuova tabella "internal" denominata **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Le istruzioni eseguono queste azioni:

    * **CREATE TABLE IF NOT EXISTS**: crea una tabella, se non esiste già. Poiché non viene usata la parola chiave **EXTERNAL**, questa è una tabella interna che viene archiviata nel data warehouse di Hive e gestita completamente da Hive.
    * **STORED AS ORC**: archivia i dati nel formato ORC (Optimized Row Columnar). Questo è un formato altamente ottimizzato ed efficiente per l'archiviazione di dati Hive.
    * **INSERT OVERWRITE ... SELECT**: - seleziona dalla tabella **log4jLogs** le righe contenenti **[ERROR]**, poi inserisce i dati nella tabella **errorLogs**.

    Per verificare che solo le righe contenenti **[ERROR]** nella colonna t4 siano state archiviate nella tabella **errorLogs**, usare l'istruzione seguente per restituire tutte le righe da **errorLogs**:

        SELECT * from errorLogs;

    Dovrebbero essere restituite tre righe di dati, tutte contenenti **[ERROR]** nella colonna t4.

    > [AZURE.NOTE]A differenza di quanto accade con le tabelle esterne, se si elimina una tabella interna verranno eliminati anche i dati sottostanti.

##<a id="summary"></a>Riepilogo

Come è possibile osservare, il comando Hive fornisce un modo semplice per eseguire query Hive in un cluster HDInsight, monitorare lo stato del processo e recuperare l'output in modo interattivo.

##<a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su Hive in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)

* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

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

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html

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