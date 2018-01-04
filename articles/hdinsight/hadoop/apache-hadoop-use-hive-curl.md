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
ms.date: 12/04/2017
ms.author: larryfr
ms.openlocfilehash: b05dbdcec3cfb5c78115061567bb6229623cd0ff
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="run-hive-queries-with-hadoop-in-hdinsight-using-rest"></a>Esecuzione di query Hive con Hadoop in HDInsight tramite REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Informazioni su come usare l'API REST WebHCat per eseguire query Hive con Hadoop nel cluster HDInsight di Azure.

[Curl](http://curl.haxx.se/) viene usato per illustrare come sia possibile interagire con HDInsight usando richieste HTTP non elaborate. L'utilità [jq](http://stedolan.github.io/jq/) viene usata per elaborare i dati JSON restituiti dalle richieste REST.

> [!NOTE]
> Se si ha già familiarità con l'uso di server Hadoop basati su Linux, ma non si ha esperienza con HDInsight, vedere il documento [Informazioni utili su Hadoop in HDInsight basato su Linux](../hdinsight-hadoop-linux-information.md).

## <a id="curl"></a>Eseguire query Hive

> [!NOTE]
> Quando si usa Curl o qualsiasi altra forma di comunicazione REST con WebHCat, è necessario autenticare le richieste fornendo il nome utente e la password dell'amministratore cluster HDInsight.
>
> Per i comandi in questa sezione, sostituire **admin** con l'utente per l'autenticazione nel cluster. Sostituire **CLUSTERNAME** con il nome del cluster. Quando richiesto, immettere la password per l'account dell'utente.
>
> L'API REST viene protetta tramite l' [autenticazione di base](http://en.wikipedia.org/wiki/Basic_access_authentication). Per essere certi che le credenziali vengano inviate in modo sicuro al server, eseguire sempre le richieste usando il protocollo Secure HTTP (HTTPS).

1. Da una riga di comando usare il comando seguente per verificare che sia possibile connettersi al cluster HDInsight:

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Si riceverà un messaggio simile al testo seguente:

        {"status":"ok","version":"v1"}

    I parametri usati in questo comando sono i seguenti:

    * **-u** : il nome utente e la password usati per autenticare la richiesta.
    * **-G** - indica che è un'operazione GET.

   L'inizio dell'URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, sarà uguale per tutte le richieste. Il percorso, **/status**, indica che la richiesta deve restituire uno stato di WebHCat (noto anche come Templeton) per il server. È inoltre possibile richiedere la versione di Hive usando il seguente comando:

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    Questa richiesta restituisce una risposta simile al testo seguente:

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. Usare quanto segue per creare una tabella denominata **log4jLogs**:

    ```bash
    curl -u admin -d user.name=admin -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive
    ```

    Con questa richiesta sono stati usati i seguenti parametri:

   * **-d**: poiché `-G` non viene usato, la richiesta userà il metodo POST per impostazione predefinita. `-d` specifica i valori di dati che vengono inviati con la richiesta.

     * **user.name** : l'utente che esegue il comando.
     * **execute** : le istruzioni HiveQL da eseguire.
     * **statusdir** - La directory in cui è scritto lo stato per questo processo.

   Le istruzioni eseguono queste azioni:
   
   * **DROP TABLE** - Se la tabella esiste già, viene eliminata.
   * **CREATE EXTERNAL TABLE** : crea una nuova tabella "external" in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati rimangono nel percorso originale.

     > [!NOTE]
     > Usa le tabelle esterne se si prevede che i dati sottostanti verranno aggiornati da un'origine esterna. Ad esempio, un processo di caricamento dati automatizzato o un'altra operazione MapReduce.
     >
     > L'eliminazione di una tabella esterna **non** comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.

   * **ROW FORMAT** - Indica il modo in cui sono formattati i dati. I campi in ogni log sono separati da uno spazio.
   * **STORED AS TEXTFILE LOCATION** - Indica dove sono archiviati i dati (la directory example/data) e che sono archiviati come testo.
   * **SELECT**: seleziona un numero di tutte le righe in cui la colonna **t4** include il valore **[ERROR]**. L'istruzione dovrebbe restituire un valore pari a **3**, poiché sono presenti tre righe contenenti questo valore.

     > [!NOTE]
     > Si noti che gli spazi tra le istruzioni HiveQL vengono sostituiti dal carattere `+` se è in uso Curl. I valori tra virgolette che contengono uno spazio, ad esempio il delimitatore, non devono essere sostituiti da `+`.

   * **INPUT__FILE__NAME LIKE '%25.log'** - Questa istruzione limita la ricerca solo ai file che terminano con .log.

     > [!NOTE]
     > `%25` è l'URL con codifica %, pertanto la condizione effettiva è `like '%.log'`. % deve essere l'URL codificato, poiché viene considerato come un carattere speciale negli URL.

   Questo comando resituisce un'ID del comando che può essere usata per controllare lo stato del processo.

       {"id":"job_1415651640909_0026"}

3. Per verificare lo stato del processo, usare il seguente comando:

    ```bash
    curl -G -u admin -d user.name=admin https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Sostituire **JOBID** con il valore restituito nel passaggio precedente. Se ad esempio il valore restituito è `{"id":"job_1415651640909_0026"}`, **JOBID** sarà `job_1415651640909_0026`.

    Se il processo è stato completato, lo stato è **SUCCEEDED**.

   > [!NOTE]
   > Questa richiesta curl restituisce un documento JSON (JavaScript Object Notation) con informazioni sul processo. Jq viene usato per recuperare solo il valore di stato.

4. Dopo che lo stato del processo risulta essere **SUCCEEDED**, è possibile recuperare i risultati del processo dall'archivio BLOB di Azure. Il parametro `statusdir` passato con la query contiene il percorso del file di output; in questo caso **/example/curl**. Questo indirizzo archivia l'output nella directory **esempio/curl** nell'archiviazione predefinita dei cluster.

    È possibile elencare e scaricare questi file usando l' [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Per altre informazioni sull'uso dell'Interfaccia della riga di comando di Azure con Archiviazione di Azure, vedere il documento [Usa Interfaccia della riga di comando di Azure 2.0 con Archiviazione di Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs).

5. Usare le seguenti istruzioni per creare una nuova tabella "internal" denominata **errorLogs**:

    ```bash
    curl -u admin -d user.name=admin -d execute="set+hive.execution.engine=tez;CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log';SELECT+*+from+errorLogs;" -d statusdir="/example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive
    ```

    Le istruzioni eseguono queste azioni:

   * **CREATE TABLE IF NOT EXISTS** : crea una tabella, se non esiste già. Questa istruzione crea una tabella interna, che viene archiviata nel data warehouse di Hive. Questa tabella è gestita da Hive.

     > [!NOTE]
     > A differenza delle tabelle esterne, se si elimina una tabella interna, vengono eliminati anche i dati sottostanti.

   * **STORED AS ORC** : archivia i dati nel formato ORC (Optimized Row Columnar). ORC è un formato altamente ottimizzato ed efficiente per l'archiviazione di dati Hive.
   * **INSERT OVERWRITE ... SELECT**: seleziona dalla tabella **log4jLogs** le righe contenenti **[ERROR]**, poi inserisce i dati nella tabella **errorLogs**.
   * **SELECT**: seleziona tutte le righe della nuova tabella **errorLogs**.

6. Usare l'ID processo restituito per verificare lo stato del processo. Se il processo è stato completato correttamente, usare l'interfaccia della riga di comando di Azure come spiegato in precedenza per scaricare e visualizzare i risultati. L'output dovrebbe contenere tre righe, tutte contenenti **ERROR**.

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


