---
title: Usare Apache Beeline con Apache Hive - Azure HDInsight
description: Informazioni su come usare il client Beeline per eseguire query Hive con Hadoop in HDInsight. Beeline è un'utilità per l'utilizzo di HiveServer2 rispetto a JDBC.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
keywords: beeline hive, hive beeline
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: hrasheed
ms.openlocfilehash: 23fa146b7bdaef0451984d0fbc638c57691cf259
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201721"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Usare il client Apache Beeline con Apache Hive

Informazioni su come usare [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) per eseguire query Apache Hive in HDInsight.

Beeline è un client Hive incluso nei nodi head del cluster HDInsight. Beeline usa JDBC per connettersi a HiveServer2, un servizio ospitato nel cluster HDInsight. È anche possibile usare Beeline per accedere in remoto a Hive in HDInsight tramite Internet. Di seguito sono riportate le più comuni stringhe di connessione utilizzate per connettersi a HDInsight da Beeline:

* __Utilizzo di Beeline da una connessione SSH a un nodo head o a un nodo perimetrale__: `-u 'jdbc:hive2://headnodehost:10001/;transportMode=http'`
* __Utilizzo di Beeline in un client, connessione ad HDInsight tramite una rete virtuale di Azure__: `-u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'`
* __Utilizzo di Beeline in un client, connessione a un cluster ESP (Enterprise Security Package) HDInsight tramite una rete virtuale di Azure__: `-u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>`
* __Utilizzo di Beeline in un client, connessione ad HDInsight tramite la rete Internet pubblica__: `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password`

> [!NOTE]  
> Sostituire `admin` con l'account di accesso del cluster.
>
> Sostituire `password` con la password dell'account di accesso del cluster.
>
> Sostituire `clustername` con il nome del cluster HDInsight.
>
> Quando ci si connette al cluster tramite una rete virtuale, sostituire `<headnode-FQDN>` con il nome di dominio completo di un nodo head del cluster.
>
> Quando ci si connette a un cluster ESP, sostituire `<AAD-Domain>` con il nome dell'istanza di Azure Active Directory (AAD) a cui è stato aggiunto il cluster. Sostituire `<username>` con il nome di un account nel dominio che disponga delle autorizzazioni per accedere al cluster.

## <a id="prereq"></a>Prerequisiti

* Un cluster Hadoop basato su Linux in HDInsight versione 3.4 o successiva.

  > [!IMPORTANT]  
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Un client SSH o un client Beeline locale. La maggior parte dei passaggi di questo documento presuppongono che si usi Beeline da una sessione SSH al cluster. Per informazioni sull'esecuzione di Beeline dall'esterno del cluster, vedere la sezione sull'[uso di Beeline in remoto](#remote).

    Per altre informazioni sull'uso di SSH, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="beeline"></a>Eseguire una query Hive

1. Quando si avvia Beeline, è necessario fornire una stringa di connessione per HiveServer2 nel cluster HDInsight:

    * Durante la connessione mediante la rete Internet pubblica, è necessario fornire il nome di account di accesso del cluster (impostazione predefinita `admin`) e la password. Ad esempio, l'uso di Beeline da un sistema client per connettersi all'indirizzo `<clustername>.azurehdinsight.net`. Questa connessione viene eseguita tramite la porta `443` e viene crittografata mediante SSL:

        ```bash
        beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
        ```

    * Quando la connessione viene eseguita da una sessione SSH a un nodo head del cluster, è possibile connettersi all'indirizzo `headnodehost` sulla porta `10001`:

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    * Quando la connessione viene eseguita tramite una rete virtuale di Azure, è necessario fornire il nome di dominio completo di un nodo head del cluster. Poiché questa connessione viene eseguita direttamente ai nodi del cluster, la connessione usa la porta `10001`:

        ```bash
        beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
        ```
    * Quando ci si connette a un cluster ESP aggiunto ad Azure Active Directory, è anche necessario specificare il nome di dominio `<AAD-Domain>` e il nome di un account utente del dominio che disponga delle autorizzazioni per accedere al cluster `<username>`:
        
        ```bash
        kinit <username>
        beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
        ```

2. I comandi di Beeline iniziano di solito con un carattere `!`, ad esempio `!help` visualizza la Guida. Tuttavia il carattere `!` può essere omesso per alcuni comandi. Ad esempio, anche `help` funziona.

    Il comando `!sql` consente di eseguire istruzioni HiveQL. HiveQL è comunque così diffuso da poter omettere il precedente `!sql`. Le due istruzioni seguenti sono equivalenti:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    In un nuovo cluster viene elencata solo una tabella: **hivesampletable**.

3. Usare il comando seguente per visualizzare lo schema di hivesampletable:

    ```hiveql
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

    Tali informazioni descrivono le colonne nella tabella.

4. Immettere le istruzioni seguenti per creare una tabella denominata **log4jLogs** usando i dati di esempio specificati con il cluster HDInsight:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' 
        GROUP BY t4;
    ```

    Le istruzioni eseguono queste azioni:

    * `DROP TABLE`: se la tabella esiste, viene eliminata.

    * `CREATE EXTERNAL TABLE`: crea una tabella **esterna** in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati rimangono nel percorso originale.

    * `ROW FORMAT`: indica il modo in cui sono formattati i dati. In questo caso, i campi in ogni log sono separati da uno spazio.

    * `STORED AS TEXTFILE LOCATION`: indica dove sono archiviati i dati e in quale formato di file.

    * `SELECT`: seleziona un conteggio di tutte le righe in cui la colonna **t4** include il valore **[ERROR]**. Questa query restituisce **3**, poiché sono presenti tre righe contenenti questo valore.

    * `INPUT__FILE__NAME LIKE '%.log'`: Hive tenta di applicare lo schema a tutti i file della directory. In questo caso la directory contiene file che non corrispondono allo schema. Per evitare dati errati nei risultati, questa istruzione indica a Hive di restituire dati solo da file che terminano con .log.

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

### <a id="file"></a>Usare Beeline per eseguire un file HiveQL

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

4. Usare il codice seguente per eseguire il file tramite Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > Il parametro `-i` avvia Beeline ed esegue le istruzioni nel file `query.hql`. Dopo il completamento della query, viene visualizzato il prompt `jdbc:hive2://headnodehost:10001/>`. È anche possibile eseguire un file usando il parametro `-f`, che chiude Beeline dopo il completamento della query.

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

Se Beeline è installato in locale e si esegue la connessione tramite la rete Internet pubblica, usare i parametri seguenti:

* __Stringa di connessione__: `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'`

* __Nome account di accesso del cluster__:`-n admin`

* __Password di accesso al cluster__ `-p 'password'`

Sostituire `clustername` nella stringa di connessione con il nome del cluster HDInsight.

Sostituire `admin` con il nome dell'account di accesso del cluster e `password` con la password per l'account di accesso del cluster.

Se Beeline è installato in locale e si esegue la connessione tramite una rete virtuale di Azure, usare i parametri seguenti:

* __Stringa di connessione__: `-u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'`

Per trovare il nome di dominio completo di un nodo head, usare le informazioni contenute nel documento [Gestire i cluster HDInsight mediante l'API REST Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

## <a id="sparksql"></a>Usare Beeline con Apache Spark

Apache Spark fornisce la propria implementazione di HiveServer2, spesso definita come server Spark Thrift. Questo servizio usa Spark SQL invece di Hive per risolvere le query e può offrire prestazioni migliori a seconda della query.

La __stringa di connessione__ usata per la connessione tramite Internet è leggermente diversa. Invece di contenere `httpPath=/hive2` è `httpPath/sparkhive2`. Di seguito è riportato un esempio per la connessione tramite Internet:

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

Quando ci si connette direttamente dal nodo head del cluster o da una risorsa all'interno della stessa istanza di Rete virtuale di Azure del cluster HDInsight, è necessario usare la porta `10002` per il server Spark Thrift invece di `10001`. Di seguito è riportato un esempio per la connessione direttamente al nodo head:

```bash
beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

## <a id="summary"></a><a id="nextsteps"></a>Passaggi successivi

Per informazioni più generali sull'uso di Hive con HDInsight, vedere il documento seguente:

* [Usare Apache Hive con Apache Hadoop su HDInsight](hdinsight-use-hive.md)

Per altre informazioni su come usare Hadoop con HDInsight, vedere i documenti seguenti:

* [Usare Apache Pig con Apache Hadoop su HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con Apache Hadoop su HDInsight](hdinsight-use-mapreduce.md)

Se si usa Tez con Hive, vedere il documento seguente: [Usare la vista Apache Ambari Tez in HDInsight basato su Linux](../hdinsight-debug-ambari-tez-view.md).

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx
