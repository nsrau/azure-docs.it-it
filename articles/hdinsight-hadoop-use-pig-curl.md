<properties
   pageTitle="Usare Pig con Hadoop in HDInsight | Azure"
   description="Informazioni su come usare Pig con Hadoop in HDInsight mediante SSH."
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
 
# Esecuzione di processi Pig con Hadoop in HDInsight mediante Curl

[AZURE.INCLUDE [pig-selector](../includes/hdinsight-selector-use-pig.md)]

In questo documento si apprenderà come usare Curl per l'esecuzione di processi Pig Latin in un cluster HDInsight. Il linguaggio di programmazione Pig Latin consente di descrivere le trasformazioni applicate ai dati di input per produrre l'output desiderato.

Curl viene usato per illustrare come è possibile interagire con HDInsight tramite richieste HTTP non elaborate per eseguire, monitorare e recuperare i risultati di processi Pig. Ciò avviene mediante l'API REST WebHCat, nota in precedenza come Templeton, fornita dal cluster HDInsight.

> [AZURE.NOTE] Se si ha già familiarità con l'uso di server Hadoop basati su Linux ma non si è esperti di HDInsight, vedere l'articolo relativo alle <a href="../hdinsight-hadoop-linux-information/" target="_blank">informazioni utili su Hadoop in HDInsight basato su Linux</a>.

## <a id="prereq"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster Azure HDInsight (Hadoop in HDInsight) (basato su Linux o su Windows)

* <a href="http://curl.haxx.se/" target="_blank">Curl</a>

* <a href="http://stedolan.github.io/jq/" target="_blank">jq</a>

## <a id="curl"></a>Eseguire processi Pig mediante Curl

> [AZURE.NOTE] Quando si usa Curl o qualsiasi altra forma di comunicazione REST con WebHCat, è necessario autenticare le richieste fornendo il nome utente e la password da amministratore del cluster HDInsight. È inoltre necessario specificare il nome del cluster come parte dell'URI usato per inviare le richieste al server.
> 
> Per i comandi riportati in questa sezione, sostituire **USERNAME** con l'utente da autenticare nel cluster e **PASSWORD** con la password dell'account utente. Sostituire **CLUSTERNAME** con il nome del cluster.
> 
> L'API REST è protetta mediante <a href="http://en.wikipedia.org/wiki/Basic_access_authentication" target="_blank">autenticazione di base</a>. È necessario effettuare sempre le richieste usando il protocollo HTTPS per essere certi che le credenziali vengano inviate in modo sicuro al server.

1. Da una riga di comando usare il seguente comando per verificare che sia possibile connettersi al cluster HDInsight. 

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Si dovrebbe ricevere una risposta simile alla seguente.

        {"status":"ok","version":"v1"}

    I parametri usati in questo comando sono i seguenti:

    * **-u**: il nome utente e la password usati per autenticare la richiesta.
    * **-G**: indica che si tratta di una richiesta GET.

    L'inizio dell'URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, sarà uguale per tutte le richieste. Il percorso, **/status**, indica che la richiesta deve restituire lo stato di WebHCat (noto anche come Templeton) per il server.

2. Usare quanto segue per inviare un processo Pig Latin al cluster.

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'wasb:///example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="wasb:///example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig

    I parametri usati in questo comando sono i seguenti:

    * **-d**: poiché non viene specificato il parametro `-G`, per la richiesta viene usato il metodo POST per impostazione predefinita. `-d`: specifica i valori di dati che vengono inviati con la richiesta.

        * **user.name**: l'utente che esegue il comando.
        * **execute**: le istruzioni Pig Latin da eseguire.
        * **statusdir**: la directory in cui verrà scritto lo stato del processo.

    > [AZURE.NOTE] Si noti che gli spazi tra le istruzioni Pig Latin vengono sostituiti dal carattere `+` se è in uso Curl.

    Questo comando dovrebbe restituire un ID processo utilizzabile per verificare lo stato del processo.

        {"id":"job_1415651640909_0026"}

3. Per verificare lo stato del processo, usare il seguente comando. Sostituire **JOBID** con il valore restituito nel passaggio precedente. Ad esempio, se il valore restituito è  `{"id":"job_1415651640909_0026"}`, JOBID sarà `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	Se il processo è stato completato, lo stato sarà "SUCCEEDED".

    > [AZURE.NOTE] Questa richiesta curl restituisce un documento JSON con informazioni sul processo; jq viene usato per recuperare il valore di stato. 

## <a id="results"></a>Visualizzare risultati

Dopo che lo stato del processo risulta essere **SUCCEEDED**, è possibile recuperare i risultati del processo dall'archivio BLOB di Azure. Il parametro `statusdir` passato con la query contiene il percorso del file di output; in questo caso **wasb:///example/pigcurl**. Questo indirizzo consente di archiviare l'output del processo nella directory **example/pigcur** del contenitore di archiviazione predefinito usato dal cluster HDInsight.

È possibile elencare e scaricare questi file usando <a href="../xplat-cli/" target="_blank">l'interfaccia della riga di comando multipiattaforma di Azure</a>. Ad esempio, per elencare i file contenuti in **example/pigcurl**, usare il seguente comando.

	azure storage blob list <container-name> example/pigcurl

Per scaricare un file, usare il seguente comando.

	azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] È necessario specificare il nome dell'account di archiviazione contenente il BLOB usando i parametri `-a` e `-k` oppure impostare le variabili di ambiente **AZURE\_STORAGE\_ACCOUNT** e **AZURE\_STORAGE\_ACCESS\_KEY**. Vedere <a href="../hdinsight-upload-data/" target="_blank" for more information.


## <a id="summary"></a>Riepilogo

Come illustrato in questo documento, è possibile usare una richiesta HTTP non elaborata per eseguire, monitorare e visualizzare i risultati dei processi Pig nel cluster HDInsight.

Per altre informazioni sull'interfaccia REST usata in questo articolo, vedere le [informazioni di riferimento su WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su Pig in HDInsight.

* [Usare Pig con Hadoop in HDInsight](../hdinsight-use-pig/)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight.

* [Usare Hive con Hadoop in HDInsight](../hdinsight-use-hive/)

* [Usare MapReduce con Hadoop in HDInsight](../hdinsight-use-mapreduce/)
<!--HONumber=45--> 
