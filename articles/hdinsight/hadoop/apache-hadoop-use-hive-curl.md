---
title: Usare Hive di Hadoop con Curl in HDInsight - Azure | Microsoft Docs
description: "Informazioni su come inviare in modalità remota processi Pig a HDInsight mediante Curl."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6ce18163-63b5-4df6-9bb6-8fcbd4db05d8
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/26/2018
ms.author: larryfr
ms.openlocfilehash: dfe9efdb57a0ce2506abd251267f39020568d081
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="run-hive-queries-with-hadoop-in-hdinsight-using-rest"></a>Esecuzione di query Hive con Hadoop in HDInsight tramite REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Informazioni su come usare l'API REST WebHCat per eseguire query Hive con Hadoop nel cluster HDInsight di Azure.

## <a name="prerequisites"></a>prerequisiti

* Un cluster Hadoop basato su Linux in HDInsight versione 3.4 o successiva.

  > [!IMPORTANT]
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Un client REST. Questo documento usa esempi di Windows PowerShell e [Curl](http://curl.haxx.se/).

    > [!NOTE]
    > Azure PowerShell fornisce cmdlet dedicati per l'uso di Hive su HDInsight. Per altre informazioni, vedere il documento [Usare Hive con Azure PowerShell](apache-hadoop-use-hive-powershell.md).

Questo documento usa anche Windows PowerShell e [Jq](http://stedolan.github.io/jq/) per elaborare i dati JSON restituiti dalle richieste REST.

## <a id="curl"></a>Eseguire una query Hive

> [!NOTE]
> Quando si usa Curl o qualsiasi altra forma di comunicazione REST con WebHCat, è necessario autenticare le richieste fornendo il nome utente e la password dell'amministratore cluster HDInsight.
>
> L'API REST viene protetta tramite l' [autenticazione di base](http://en.wikipedia.org/wiki/Basic_access_authentication). Per essere certi che le credenziali vengano inviate in modo sicuro al server, eseguire sempre le richieste usando il protocollo Secure HTTP (HTTPS).

1. Per configurare l'accesso al cluster usato dagli script in questo documento, usare uno dei comandi seguenti:

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. Per configurare il nome del cluster, usare uno dei comandi seguenti:

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. Per verificare che sia possibile connettersi al cluster HDInsight, usare uno dei comandi seguenti:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status)
    ```
    
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Si riceverà un messaggio simile al testo seguente:

    ```json
    {"status":"ok","version":"v1"}
    ```

    I parametri usati in questo comando sono i seguenti:

    * `-u`: il nome utente e la password usati per autenticare la richiesta.
    * `-G`: indica che è un'operazione GET.

   La parte iniziale dell'URL, `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`, è uguale per tutte le richieste. Il percorso, `/status`, indica che la richiesta deve restituire uno stato di WebHCat (noto anche come Templeton) per il server. È inoltre possibile richiedere la versione di Hive usando il seguente comando:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Questa richiesta restituisce una risposta simile al testo seguente:

    ```json
        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}
    ```

4. Usare quanto segue per creare una tabella denominata **log4jLogs**:

    ```bash
    JOBID=`curl -s -u $LOGIN -d user.name=$LOGIN -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/hive | jq .id`
    echo $JOBID
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="set hive.execution.engine=tez;DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Questa richiesta usa il metodo POST, che invia dati come parte della richiesta all'API REST. I valori di dati seguenti vengono inviati insieme alla richiesta:

     * `user.name`: l'utente che esegue il comando.
     * `execute`: le istruzioni HiveQL da eseguire.
     * `statusdir`: la directory in cui è scritto lo stato del processo.

   Le istruzioni eseguono queste azioni:
   
   * `DROP TABLE`: se la tabella esiste già, viene eliminata.
   * `CREATE EXTERNAL TABLE`: crea una nuova tabella "esterna" in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati rimangono nel percorso originale.

     > [!NOTE]
     > Usa le tabelle esterne se si prevede che i dati sottostanti verranno aggiornati da un'origine esterna. Ad esempio, un processo di caricamento dati automatizzato o un'altra operazione MapReduce.
     >
     > L'eliminazione di una tabella esterna **non** comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.

   * `ROW FORMAT`: indica il modo in cui sono formattati i dati. I campi in ogni log sono separati da uno spazio.
   * `STORED AS TEXTFILE LOCATION`: indica la posizione in cui sono archiviati i dati (la directory example/data) e che sono archiviati come testo.
   * `SELECT`: seleziona un conteggio di tutte le righe in cui la colonna **t4** include il valore **[ERROR]**. L'istruzione dovrebbe restituire un valore pari a **3**, poiché sono presenti tre righe contenenti questo valore.

     > [!NOTE]
     > Si noti che gli spazi tra le istruzioni HiveQL vengono sostituiti dal carattere `+` se è in uso Curl. I valori tra virgolette che contengono uno spazio, ad esempio il delimitatore, non devono essere sostituiti da `+`.

      Questo comando resituisce un'ID del comando che può essere usata per controllare lo stato del processo.

5. Per verificare lo stato del processo, usare il seguente comando:

    ```bash
    curl -G -u $LOGIN -d user.name=$LOGIN https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/$JOBID | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    Se il processo è stato completato, lo stato è **SUCCEEDED**.

6. Dopo che lo stato del processo risulta essere **SUCCEEDED**, è possibile recuperare i risultati del processo dall'archivio BLOB di Azure. Il parametro `statusdir` passato con la query contiene il percorso del file di output; in questo caso `/example/rest`. Questo indirizzo archivia l'output nella directory `example/curl` nell'archiviazione predefinita dei cluster.

    È possibile elencare e scaricare questi file usando l' [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Per altre informazioni sull'uso dell'Interfaccia della riga di comando di Azure con Archiviazione di Azure, vedere il documento [Usa Interfaccia della riga di comando di Azure 2.0 con Archiviazione di Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs).

## <a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su Hive con HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Se si usa Tez con Hive, vedere i documenti seguenti per le informazioni di debug:

* [Usare la vista Ambari Tez in HDInsight basato su Linux](../hdinsight-debug-ambari-tez-view.md)

Per altre informazioni sull'API REST usata in questo documento, vedere il documento [Informazioni di riferimento su WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

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




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


