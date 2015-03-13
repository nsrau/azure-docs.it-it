<properties
   pageTitle="Usare Hive di Hadoop in HDInsight | Azure"
   description="Informazioni sull'uso di Hive con HDInsight tramite SSH."
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

# Uso di Hive con Hadoop in HDInsight tramite SSH

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

In questo articolo si apprenderà come usare SSH per connettersi a un cluster Hadoop in HDInsight e quindi inviare in modo interattivo query Hive mediante l'interfaccia della riga di comando di Hive.

> [AZURE.NOTE] Se si ha già familiarità con l'uso di server Hadoop basati su Linux ma non si è esperti di HDInsight, vedere l'articolo relativo alle <a href="../hdinsight-hadoop-linux-information/" target="_blank">nozioni che è necessario conoscere sul funzionamento di Hadoop in HDInsight basato su Linux</a>.

## <a id="prereq"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster Hadoop  basato su Linux in HDInsight.

* Un client SSH. I sistemi operativi Linux, Unix e Mac dovrebbero essere forniti con un client SSH. Per gli utenti di Windows è necessario scaricare un client, ad esempio <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">PuTTY</a>.

## <a id="ssh"></a>Connettersi tramite SSH

Connettersi al nome di dominio completo (FQDN) del cluster HDInsight usando il comando SSH. L'FQDN è costituito dal nome assegnato al cluster, seguito da **.azurehdinsight.net**. Ad esempio, il seguente comando stabilirà la connessione a un cluster denominato **myhdinsight**.

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se è stata fornita una chiave del certificato per l'autenticazione SSH** durante la creazione del cluster HDInsight, potrebbe essere necessario specificare il percorso della chiave privata nel sistema client.

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Se è stata specificata una password per l'autenticazione SSH** durante la creazione del cluster HDInsight, sarà necessario fornire la password quando richiesto.

### PuTTY (client di Windows)

Windows non fornisce un client SSH incorporato. È consigliabile usare **PuTTY**, disponibile per il download da <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>.

Per altre informazioni sull'uso di PuTTY, vedere la sezione sull'**uso di PuTTY per la connessione a un computer Linux** nella pagina relativa a <a href="http://azure.microsoft.com/ documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">come usare SSH con Linux in Azure</a>.

> [AZURE.NOTE] Se è stato usato un certificato per l'autenticazione SSH per il cluster HDInsight, sarà necessario anche leggere la sezione sulla **creazione di una chiave privata PPK per PuTTY** nella pagina relativa a <a href="http://azure.microsoft.com/ documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">come usare SSH con Linux in Azure.</a>

## <a id="hive"></a>Usare il comando Hive

2. Una volta connessi, avviare l'interfaccia della riga di comando di Hive usando il seguente comando.

        hive

3. Usando l'interfaccia della riga di comando, immettere le seguenti istruzioni per creare una nuova tabella denominata **log4jLogs** con i dati di esempio.

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

    Le istruzioni eseguono queste azioni:

    * **DROP TABLE**: elimina la tabella e il file di dati, nel caso in cui la tabella esista già.
    * **CREATE EXTERNAL TABLE**: crea una nuova tabella 'external' in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati rimangono nella posizione originale.
    * **ROW FORMAT**: indica a Hive il modo in cui sono formattati i dati. In questo caso i campi di ogni log sono separati da uno spazio.
    * **STORED AS TEXTFILE LOCATION**: indica a Hive dove sono archiviati i dati (directory example/data) e specifica che i dati sono archiviati come testo.
    * **SELECT**: seleziona un numero di tutte le righe in cui la colonna **t4** contiene il valore **[ERROR]**. Dovrebbe restituire un valore pari a **3**, poiché sono presenti tre righe contenenti questo valore.

    > [AZURE.NOTE] È consigliabile usare le tabelle esterne quando si prevede che i dati sottostanti vengano aggiornati da un'origine esterna, ad esempio un processo automatico di caricamento dei dati, oppure da un'altra operazione MapReduce, ma si vuole che le query Hive usino sempre i dati più recenti.
    >
    > L'eliminazione di una tabella esterna **non** comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.

4. Usare le seguenti istruzioni per creare una nuova tabella  'internal' denominata **errorLogs**.

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

    Queste istruzioni eseguono le azioni seguenti.

    * **CREATE TABLE IF NOT EXISTS**: crea una tabella, se non esiste già. Poiché non viene usata la parola chiave **EXTERNAL**, questa è una tabella interna che viene archiviata nel data warehouse di Hive e gestita completamente da Hive.
    * **STORED AS ORC**: archivia i dati nel formato ORC (Optimized Row Columnar). Si tratta di un formato di file altamente ottimizzato per l'archiviazione di dati Hive.
    * **INSERT OVERWRITE ... SELECT**: seleziona dalla tabella **log4jLogs** le righe contenenti **[ERROR]**, quindi inserisce i dati nella tabella **errorLogs**.

    Per verificare che solo le righe contenenti **[ERROR]** nella colonna t4 vengano archiviate nella tabella **errorLogs**, usare la seguente istruzione per restituire tutte le righe da **errorLogs**.

        SELECT * from errorLogs;

    Dovrebbero essere restituite tre righe di dati, tutte contenenti **[ERROR]** nella colonna t4.

    > [AZURE.NOTE] A differenza delle tabelle **EXTERNAL**, se si elimina una tabella interna verranno eliminati anche i dati sottostanti.

## <a id="summary"></a>Riepilogo

Come è possibile osservare, il comando Hive fornisce un modo semplice per eseguire query Hive in un cluster HDInsight, monitorare lo stato del processo e recuperare l'output in modo interattivo.

## <a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su Hive in HDInsight.

* [Usare Hive con Hadoop in HDInsight](../hdinsight-use-hive/)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight.

* [Usare Pig con Hadoop in HDInsight](../hdinsight-use-pig/)

* [Usare MapReduce con Hadoop in HDInsight](../hdinsight-use-mapreduce/)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/it-it/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/ pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/ documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html

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

<!--HONumber=45--> 
