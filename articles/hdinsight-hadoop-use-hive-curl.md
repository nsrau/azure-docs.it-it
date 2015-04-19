<properties
   pageTitle="Usare Pig di Hadoop in HDInsight | Azure"
   description="Informazioni su come inviare in modalità remota processi Pig a HDInsight mediante Curl."
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

# Esecuzione di query Hive con Hadoop in HDInsight mediante Curl

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

In questo documento si apprenderà come usare Curl per l'esecuzione di query Hive in un cluster Hadoop in HDInsight. 

CURL viene usato per illustrare come è possibile interagire con HDInsight tramite richieste HTTP non elaborate per eseguire, monitorare e recuperare i risultati di query Hive. Ciò avviene mediante l'API REST WebHCat, nota in precedenza come Templeton, fornita dal cluster HDInsight.

> [AZURE.NOTE] Se si ha già familiarità con l'uso di server Hadoop basati su Linux, ma non si ha esperienza con HDInsight, vedere <a href="../hdinsight-hadoop-linux-information/" target="_blank">Informazioni utili su Hadoop in HDInsight basato su Linux</a>.

## <a id="prereq"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster Hadoop in HDInsight (basato su Linux o su Windows)

* <a href="http://curl.haxx.se/" target="_blank">Curl</a>

* <a href="http://stedolan.github.io/jq/" target="_blank">jq</a>

## <a id="curl"></a>Eseguire query Hive usando Curl

> [AZURE.NOTE] Quando si usa Curl o qualsiasi altra forma di comunicazione REST con WebHCat, è necessario autenticare le richieste fornendo il nome utente e la password da amministratore del cluster HDInsight. È inoltre necessario specificare il nome del cluster come parte dell'URI usato per inviare le richieste al server.
> 
> Per i comandi riportati in questa sezione, sostituire **USERNAME** con l'utente da autenticare nel cluster e **PASSWORD** con la password dell'account utente. Sostituire **CLUSTERNAME** con il nome del cluster.
> 
> L'API REST viene protetta tramite <a href="http://en.wikipedia.org/wiki/Basic_access_authentication" target="_blank">autenticazione di base</a>. È necessario effettuare sempre le richieste usando il protocollo HTTPS per essere certi che le credenziali vengano inviate in modo sicuro al server.

1. Da una riga di comando usare il seguente comando per verificare che sia possibile connettersi al cluster HDInsight.

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Si dovrebbe ricevere una risposta simile alla seguente.

        {"status":"ok","version":"v1"}

    I parametri usati in questo comando sono i seguenti:

    * **-u**: il nome utente e la password usati per autenticare la richiesta.
    * **-G**: indica che si tratta di una richiesta GET.

    L'inizio dell'URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, sarà lo stesso per tutte le richieste. Il percorso, **/status**, indica che la richiesta deve restituire lo stato di WebHCat (noto anche come Templeton) per il server. È inoltre possibile richiedere la versione di Hive usando il seguente comando.

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive

    Dovrebbe restituire una risposta simile alla seguente.

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. Usare quanto segue per creare una nuova tabella denominata **log4jLogs**.

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'wasb:///example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+GROUP+BY+t4;" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    I parametri usati in questo comando sono i seguenti:

    * **-d**: poiché `-G` non viene usato, la richiesta userà il metodo POST per impostazione predefinita. `-d` specifica i valori di dati che vengono inviati con la richiesta

        * **user.name**: l'utente che esegue il comando
        
        * **execute**: le istruzioni HiveQL da eseguire
        
        * **statusdir**: la directory in cui verrà scritto lo stato del processo

    Queste istruzioni eseguono le azioni seguenti.

    * **DROP TABLE**: elimina la tabella e il file di dati, nel caso in cui la tabella esista già.
    
    * **CREATE EXTERNAL TABLE**: crea una nuova tabella 'esterna' in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati rimangono nella posizione originale.

		> [AZURE.NOTE] È consigliabile usare le tabelle esterne quando si prevede che i dati sottostanti vengano aggiornati da un'origine esterna, ad esempio un processo automatico di caricamento dei dati, oppure da un'altra operazione MapReduce, ma si vuole che le query Hive usino sempre i dati più recenti.
		>
		> L'eliminazione di una tabella esterna **non** comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.

    * **ROW FORMAT**: indica a Hive il modo in cui sono formattati i dati. In questo caso i campi di ogni log sono separati da uno spazio.
    
    * **STORED AS TEXTFILE LOCATION**: indica a Hive dove sono archiviati i dati (directory example/data) e specifica che i dati sono archiviati come testo.
    
    * **SELECT**: seleziona un numero di tutte le righe in cui la colonna **t4** include il valore **[ERROR]**. Dovrebbe restituire un valore pari a **3**, poiché sono presenti tre righe contenenti questo valore.

    > [AZURE.NOTE] Si noti che gli spazi tra le istruzioni HiveQL vengono sostituiti dal carattere `+` se è in uso Curl. I valori tra virgolette che contengono uno spazio, ad esempio il delimitatore, non devono essere sostituiti da `+`.

    Questo comando dovrebbe restituire un ID processo utilizzabile per verificare lo stato del processo.

        {"id":"job_1415651640909_0026"}

3. Per verificare lo stato del processo, usare il seguente comando. Sostituire **JOBID** con il valore restituito nel passaggio precedente. Ad esempio, se il valore restituito è `{"id":"job_1415651640909_0026"}` JOBID sarà `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	Se il processo è stato completato, lo stato sarà "SUCCEEDED".

    > [AZURE.NOTE] Questa richiesta curl restituisce un documento JSON con informazioni sul processo; jq viene usato per recuperare il valore di stato. 

4. Dopo che lo stato del processo risulta essere **SUCCEEDED**, è possibile recuperare i risultati del processo dall'archivio BLOB di Azure. Il parametro `statusdir` passato con la query contiene il percorso del file di output, in questo caso **wasb:///example/curl**. Questo indirizzo consente di archiviare l'output del processo nella directory **example/curl** del contenitore di archiviazione predefinito usato dal cluster HDInsight.

    È possibile elencare e scaricare questi file usando l'<a href="../xplat-cli/" target="_blank">interfaccia della riga di comando multipiattaforma di Azure (xplat-cli)</a>. Ad esempio, per elencare i file contenuti in **example/curl**, usare il seguente comando.

		azure storage blob list <container-name> example/curl

	Per scaricare un file, usare il seguente comando.

		azure storage blob download <container-name> <blob-name> <destination-file>

	> [AZURE.NOTE] È necessario specificare il nome di account di archiviazione che contiene il BLOB tramite i parametri `-a` e `-k`  oppure impostando le variabili di ambiente **AZURE\_STORAGE\_ACCOUNT** e **AZURE\_STORAGE\_ACCESS\_KEY**. Vedere <a href="../hdinsight-upload-data/" target="_blank" for more information.

6. Per creare una nuova tabella "interna" denominata **errorLogs**, usare le seguenti istruzioni.

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]';SELECT+*+from+errorLogs;" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Queste istruzioni eseguono le azioni seguenti.

    * **CREATE TABLE IF NOT EXISTS** : crea una tabella, se non esiste già. Poiché non viene usata la parola chiave **EXTERNAL**, si tratta di una tabella 'interna', che viene archiviata nel data warehouse di Hive e viene gestita completamente da Hive.

		> [AZURE.NOTE] A differenza di quanto accade con le tabelle **EXTERNAL** , se si elimina una tabella interna verranno eliminati anche i dati sottostanti.

    * **STORED AS ORC** : archivia i dati nel formato ORC (Optimized Row Columnar). Si tratta di un formato di file altamente ottimizzato per l'archiviazione di dati Hive.
    * **INSERT OVERWRITE ... SELECT** : seleziona dalla tabella **log4jLogs**  le righe che includono **[ERROR]**, quindi inserisce i dati nella tabella **errorLogs** .
    * <strong>SELECT \*</strong>: seleziona tutte le righe della nuova tabella **errorLogs** .

7. Usare l'ID processo restituito per verificare lo stato del processo. Se il processo è stato completato correttamente, usare xplat-cli come spiegato in precedenza per scaricare e visualizzare i risultati. L'output dovrebbe contenere tre righe, tutte contenenti **[ERROR]**.


## <a id="summary"></a>Riepilogo

Come illustrato in questo documento, è possibile usare una richiesta HTTP non elaborata per eseguire, monitorare e visualizzare i risultati dei processi Hive nel cluster HDInsight.

Per altre informazioni sull'interfaccia REST usata in questo articolo, vedere le <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference" target="_blank">informazioni di riferimento su WebHCat</a>.

## <a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su Hive con HDInsight.

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight.

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
