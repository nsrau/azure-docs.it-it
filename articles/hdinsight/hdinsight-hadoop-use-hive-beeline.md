---
title: Usare Beeline con Apache Hive - Azure HDInsight | Microsoft Docs
description: "Informazioni su come usare il client Beeline per eseguire query Hive con Hadoop in HDInsight. Beeline è un&quot;utilità per l&quot;utilizzo di HiveServer2 rispetto a JDBC."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
keywords: beeline hive, hive beeline
ms.assetid: 3adfb1ba-8924-4a13-98db-10a67ab24fca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/05/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: e75bc8b74f965a0d4509b6967f1cdc7fa32eec56
ms.contentlocale: it-it
ms.lasthandoff: 05/18/2017


---
# <a name="use-the-beeline-client-with-apache-hive"></a>Usare il client Beeline con Apache Hive

Informazioni su come usare [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) per eseguire query Hive in HDInsight.

Beeline è un client Hive incluso nei nodi head del cluster HDInsight. Beeline usa JDBC per connettersi a HiveServer2, un servizio ospitato nel cluster HDInsight. È anche possibile usare Beeline per accedere in remoto a Hive in HDInsight tramite Internet. Nella tabella seguente vengono elencate le stringhe di connessione da usare con Beeline:

| Esecuzione di Beeline da | Parametri |
| --- | --- | --- |
| Una connessione SSH a un nodo head o a un nodo perimetrale | `-u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -n admin` |
| All'esterno del cluster | `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password` |

> [!NOTE]
> Sostituire `admin` con l'account di accesso del cluster.
>
> Sostituire `password` con la password dell'account di accesso del cluster.
>
> Sostituire `clustername` con il nome del cluster HDInsight.

## <a id="prereq"></a>Prerequisiti

* Un cluster Hadoop basato su Linux in HDInsight.

  > [!IMPORTANT]
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-retirement-date).

* Un client SSH o un client Beeline locale. La maggior parte dei passaggi di questo documento presuppongono che si usi Beeline da una sessione SSH al cluster. Per informazioni sull'esecuzione di Beeline dall'esterno del cluster, vedere la sezione sull'[uso di Beeline in remoto](#remote).

    Per altre informazioni sull'uso di SSH, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="beeline"></a>Usare Beeline

1. Quando si avvia Beeline, è necessario fornire una stringa di connessione per HiveServer2 nel cluster HDInsight. È necessario inoltre fornire il nome dell'account di accesso al cluster (in genere `admin`). Se si esegue il comando dall'esterno del cluster, è necessario fornire anche la password dell'account di accesso del cluster. Usare la tabella seguente per trovare il formato della stringa di connessione e i parametri da usare:

    | Esecuzione di Beeline da | Parametri |
    | --- | --- | --- |
    | Una connessione SSH a un nodo head o a un nodo perimetrale | `-u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -n admin` |
    | All'esterno del cluster | `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password` |

    Il comando seguente, ad esempio, può essere usato per avviare Beeline da una sessione SSH al cluster:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -n admin
    ```

    Questo comando avvia il client Beeline ed esegue la connessione a HiveServer2 nel nodo head del cluster. Il parametro `-n` viene usato per specificare l'account di accesso del cluster. L'account di accesso predefinito è `admin`. Se durante la creazione del cluster è stato immesso un nome diverso, usarlo al posto di `admin`.

    Al termine dell'esecuzione del comando, si aprirà il prompt `jdbc:hive2://headnodehost:10001/>`.

2. I comandi di Beeline iniziano di solito con un carattere `!`, ad esempio `!help` visualizza la Guida. Tuttavia il carattere `!` può essere omesso per alcuni comandi. Ad esempio, anche `help` funziona.

    Il comando `!sql` consente di eseguire istruzioni HiveQL. HiveQL è comunque così diffuso da poter omettere il precedente `!sql`. Le due istruzioni seguenti sono equivalenti:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    In un nuovo cluster viene elencata solo una tabella: **hivesampletable**.

3. Usare il comando seguente per visualizzare lo schema di hivesampletable:

    ```bash
    describe hivesampletable;
    ```

    Questo comando restituisce le informazioni seguenti:

        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Tali informazioni descrivono le colonne nella tabella. Anche se è possibile eseguire alcune query su questi dati, verrà creata una nuova tabella per dimostrare come caricare i dati in Hive e applicare uno schema.

4. Immettere le istruzioni seguenti per creare una tabella denominata **log4jLogs** usando i dati di esempio specificati con il cluster HDInsight:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Di seguito sono elencate le istruzioni che eseguono queste azioni:

    * `DROP TABLE`: se la tabella esiste, viene eliminata.

    * `CREATE EXTERNAL TABLE`: crea una tabella **esterna** in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati rimangono nel percorso originale.

    * `ROW FORMAT`: indica il modo in cui sono formattati i dati. In questo caso, i campi in ogni log sono separati da uno spazio.

    * `STORED AS TEXTFILE LOCATION`: indica dove sono archiviati i dati e in quale formato di file.

    * `SELECT`: seleziona un conteggio di tutte le righe in cui la colonna **t4** include il valore **[ERROR]**. Questa query restituisce **3**, poiché sono presenti tre righe contenenti questo valore.

    * `INPUT__FILE__NAME LIKE '%.log'`: Hive tenta di applicare lo schema a tutti i file della directory. In questo caso la directory contiene file che non corrispondono allo schema. Per evitare dati errati nei risultati, questa istruzione indica a Hive che devono essere restituiti dati solo da file che terminano con .log.

  > [!NOTE]
  > Usa le tabelle esterne se si prevede che i dati sottostanti verranno aggiornati da un'origine esterna. Ad esempio, un processo di caricamento dati automatizzato o un'operazione MapReduce.
  >
  > L'eliminazione di una tabella esterna **non** comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.

    L'output di questo comando è simile al testo seguente:

        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :

        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

5. Per uscire da Beeline, usare `!exit`.

## <a id="file"></a>Usare Beeline per eseguire un file HiveQL

Usare la procedura seguente per creare un file, quindi eseguirlo tramite Beeline.

1. Usare il comando seguente per creare un file denominato **query.hql**:

    ```bash
    nano query.hql
    ```

2. Usare il testo seguente come contenuto del file. Questa query crea una nuova tabella "interna" denominata **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Le istruzioni eseguono queste azioni:

    * **CREATE TABLE IF NOT EXISTS**: crea una tabella, se non esiste già. Poiché la parola chiave **EXTERNAL** non viene usata, questa istruzione crea una tabella interna. Le tabelle interne vengono archiviate nel data warehouse di Hive e sono totalmente gestite da Hive.
    * **STORED AS ORC** : archivia i dati nel formato ORC (Optimized Row Columnar). ORC è un formato altamente ottimizzato ed efficiente per l'archiviazione di dati Hive.
    * **INSERT OVERWRITE ... SELECT**: seleziona dalla tabella **log4jLogs** le righe contenenti **[ERROR]**, poi inserisce i dati nella tabella **errorLogs**.

    > [!NOTE]
    > A differenza delle tabelle esterne, se si elimina una tabella interna, vengono eliminati anche i dati sottostanti.

3. Per salvare il file usare **Ctrl**+**_X**, quindi immettere **Y** e infine premere **INVIO**.

4. Usare il codice seguente per eseguire il file tramite Beeline: Sostituire **HOSTNAME** con il nome ottenuto in precedenza per il nodo head e **PASSWORD** con la password per l'account amministratore:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -n admin -i query.hql
    ```

    > [!NOTE]
    > Il parametro `-i` avvia Beeline ed esegue le istruzioni nel file query.hql. Dopo il completamento della query, viene visualizzato il prompt `jdbc:hive2://headnodehost:10001/>`. È anche possibile eseguire un file usando il parametro `-f`, che chiude Beeline dopo il completamento della query.

5. Per verificare che la tabella **errorLogs** sia stata creata, usare l'istruzione seguente per restituire tutte le righe da **errorLogs**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Dovrebbero essere restituite tre righe di dati, tutte contenenti **[ERROR]** nella colonna t4.

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a id="remote"></a>Usare Beeline da remoto

Se Beeline è installato localmente o se viene usato tramite un'immagine Docker, ad esempio [sutoiku/beeline](https://hub.docker.com/r/sutoiku/beeline/), è necessario usare i parametri seguenti:

* __Stringa di connessione__: `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'`

* __Nome account di accesso del cluster__:`-n admin`

* __Password di accesso al cluster__ `-p 'password'`

Sostituire `clustername` nella stringa di connessione con il nome del cluster HDInsight.

Sostituire `admin` con il nome dell'account di accesso del cluster e `password` con la password per l'account di accesso del cluster.

## <a id="summary"></a><a id="nextsteps"></a>Passaggi successivi

Per informazioni più generali sull'uso di Hive con HDInsight, vedere il documento seguente:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

Per altre informazioni su come usare Hadoop con HDInsight, vedere i documenti seguenti:

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Se si usa Tez con Hive, vedere i documenti seguenti:

* [Usare l'interfaccia utente di Tez in HDInsight basato su Windows](hdinsight-debug-tez-ui.md)
* [Usare la vista Ambari Tez in HDInsight basato su Linux](hdinsight-debug-ambari-tez-view.md)

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


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

