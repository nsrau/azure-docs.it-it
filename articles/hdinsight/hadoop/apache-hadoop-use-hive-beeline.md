---
title: Usare Apache Beeline con Apache Hive - Azure HDInsight
description: Informazioni su come usare il client Beeline per eseguire query Hive con Hadoop in HDInsight. Beeline è un'utilità per l'utilizzo di HiveServer2 rispetto a JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 26a166e61086af8cf10f761b608fcf66eb8734fd
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406256"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Usare il client Apache Beeline con Apache Hive

Informazioni su come usare [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) per eseguire query Apache Hive in HDInsight.

Beeline è un client Hive incluso nei nodi head del cluster HDInsight. Beeline usa JDBC per connettersi a HiveServer2, un servizio ospitato nel cluster HDInsight. È anche possibile usare Beeline per accedere in remoto a Hive in HDInsight tramite Internet. Di seguito sono riportate le più comuni stringhe di connessione utilizzate per connettersi a HDInsight da Beeline:

## <a name="types-of-connections"></a>Types of connections

### <a name="from-an-ssh-session"></a>From an SSH session

When connecting from an SSH session to a cluster headnode, you can then connect to the `headnodehost` address on port `10001`:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Over an Azure Virtual Network

When connecting from a client to HDInsight over an Azure Virtual Network, you must provide the fully qualified domain name (FQDN) of a cluster head node. Poiché questa connessione viene eseguita direttamente ai nodi del cluster, la connessione usa la porta `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Replace `<headnode-FQDN>` with the fully qualified domain name of a cluster headnode. Per trovare il nome di dominio completo di un nodo head, usare le informazioni contenute nel documento [Gestire i cluster HDInsight mediante l'API REST Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>To HDInsight Enterprise Security Package (ESP) cluster using Kerberos

When connecting from a client to an Enterprise Security Package (ESP) cluster joined to Azure Active Directory (AAD)-DS on a machine in same realm of the cluster, you must also specify the domain name `<AAD-Domain>` and the name of a domain user account with permissions to access the cluster `<username>`:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Sostituire `<username>` con il nome di un account nel dominio che disponga delle autorizzazioni per accedere al cluster. Replace `<AAD-DOMAIN>` with the name of the Azure Active Directory (AAD) that the cluster is joined to. Use an uppercase string for the `<AAD-DOMAIN>` value, otherwise the credential won't be found. Check `/etc/krb5.conf` for the realm names if needed.

---

### <a name="over-public-or-private-endpoints"></a>Over public or private endpoints

When connecting to a cluster using the public or private endpoints, you must provide the cluster login account name (default `admin`) and password. Ad esempio, l'uso di Beeline da un sistema client per connettersi all'indirizzo `<clustername>.azurehdinsight.net`. Questa connessione viene eseguita tramite la porta `443` e viene crittografata mediante SSL:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n <username> -p password
```

or for private endpoint:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n <username> -p password
```

Sostituire `clustername` con il nome del cluster HDInsight. Sostituire `<username>` con l'account di accesso del cluster. For ESP clusters, use the full UPN (e.g. user@domain.com). Sostituire `password` con la password dell'account di accesso del cluster.

Private endpoints point to a basic load balancer, which can only be accessed from the VNETs peered in the same region. See [constraints on global VNet peering and load balancers](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) for more info. You can use the `curl` command with `-v` option to troubleshoot any connectivity problems with public or private endpoints before using beeline.

---

### <a id="sparksql"></a>Usare Beeline con Apache Spark

Apache Spark fornisce la propria implementazione di HiveServer2, spesso definita come server Spark Thrift. Questo servizio usa Spark SQL invece di Hive per risolvere le query e può offrire prestazioni migliori a seconda della query.

#### <a name="through-public-or-private-endpoints"></a>Through public or private endpoints

The connection string used  is slightly different. Instead of containing `httpPath=/hive2` it's `httpPath/sparkhive2`:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n <username> -p password
```

or for private endpoint:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n <username> -p password
```

Sostituire `clustername` con il nome del cluster HDInsight. Sostituire `<username>` con l'account di accesso del cluster. For ESP clusters, use the full UPN (e.g. user@domain.com). Sostituire `password` con la password dell'account di accesso del cluster.

Private endpoints point to a basic load balancer, which can only be accessed from the VNETs peered in the same region. See [constraints on global VNet peering and load balancers](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) for more info. You can use the `curl` command with `-v` option to troubleshoot any connectivity problems with public or private endpoints before using beeline.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>From cluster head or inside Azure Virtual Network with Apache Spark

Quando ci si connette direttamente dal nodo head del cluster o da una risorsa all'interno della stessa istanza di Rete virtuale di Azure del cluster HDInsight, è necessario usare la porta `10002` per il server Spark Thrift invece di `10001`. The following example shows how to connect directly to the head node:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>Prerequisiti

* A Hadoop cluster on HDInsight. Vedere [Guida introduttiva: Introduzione ad Apache Hadoop e Apache Hive in Azure HDInsight usando il modello di Resource Manager](./apache-hadoop-linux-tutorial-get-started.md).

* Notice the [URI scheme](../hdinsight-hadoop-linux-information.md#URI-and-scheme) for your cluster's primary storage. For example,  `wasb://` for Azure Storage, `abfs://` for Azure Data Lake Storage Gen2, or `adl://` for Azure Data Lake Storage Gen1. If secure transfer is enabled for Azure Storage, the URI is `wasbs://`. For more information, see [secure transfer](../../storage/common/storage-require-secure-transfer.md).

* Option 1: An SSH client. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Most of the steps in this document assume that you're using Beeline from an SSH session to the cluster.

* Option 2:  A local Beeline client.

## <a id="beeline"></a>Eseguire una query Hive

This example is based on using the Beeline client from an SSH connection.

1. Open an SSH connection to the cluster with the code below. Sostituire `sshuser` con il nome utente SSH del cluster e sostituire `CLUSTERNAME` con il nome del cluster. When prompted, enter the password for the SSH user account.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Connect to HiveServer2 with your Beeline client from your open SSH session by entering the following command:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. I comandi di Beeline iniziano di solito con un carattere `!`, ad esempio `!help` visualizza la Guida. Tuttavia il carattere `!` può essere omesso per alcuni comandi. Ad esempio, anche `help` funziona.

    There's `!sql`, which is used to execute HiveQL statements. HiveQL è comunque così diffuso da poter omettere il precedente `!sql`. Le due istruzioni seguenti sono equivalenti:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    In un nuovo cluster viene elencata solo una tabella: **hivesampletable**.

4. Usare il comando seguente per visualizzare lo schema di hivesampletable:

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

5. Enter the following statements to create a table named **log4jLogs** by using sample data provided with the HDInsight cluster: (Revise as needed based on your [URI scheme](../hdinsight-hadoop-linux-information.md#URI-and-scheme).)

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
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' 
        GROUP BY t4;
    ```

    These statements do the following actions:

    * `DROP TABLE` - If the table exists, it's deleted.

    * `CREATE EXTERNAL TABLE`: crea una tabella **esterna** in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati rimangono nel percorso originale.

    * `ROW FORMAT`: indica il modo in cui sono formattati i dati. In questo caso, i campi in ogni log sono separati da uno spazio.

    * `STORED AS TEXTFILE LOCATION`: indica dove sono archiviati i dati e in quale formato di file.

    * `SELECT`: seleziona un conteggio di tutte le righe in cui la colonna **t4** include il valore **[ERROR]** . Questa query restituisce **3**, poiché sono presenti tre righe contenenti questo valore.

    * `INPUT__FILE__NAME LIKE '%.log'`: Hive tenta di applicare lo schema a tutti i file della directory. In this case, the directory contains files that don't match the schema. Per evitare dati errati nei risultati, questa istruzione indica a Hive di restituire dati solo da file che terminano con .log.

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

6. Per uscire da Beeline, usare `!exit`.

## <a id="file"></a>Eseguire un file HiveQL

This is a continuation from the prior example. Usare la procedura seguente per creare un file, quindi eseguirlo tramite Beeline.

1. Usare il comando seguente per creare un file denominato **query.hql**:

    ```bash
    nano query.hql
    ```

2. Usare il testo seguente come contenuto del file. Questa query crea una nuova tabella "interna" denominata **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    These statements do the following actions:

   * **CREATE TABLE IF NOT EXISTS** - If the table doesn't already exist, it's created. Since the **EXTERNAL** keyword isn't used, this statement creates an internal table. Le tabelle interne vengono archiviate nel data warehouse di Hive e sono totalmente gestite da Hive.
   * **STORED AS ORC** : archivia i dati nel formato ORC (Optimized Row Columnar). ORC è un formato altamente ottimizzato ed efficiente per l'archiviazione di dati Hive.
   * **INSERT OVERWRITE ... SELECT** - Selects rows from the **log4jLogs** table that contain **[ERROR]** , then inserts the data into the **errorLogs** table.

    > [!NOTE]  
    > A differenza delle tabelle esterne, se si elimina una tabella interna, vengono eliminati anche i dati sottostanti.

3. To save the file, use **Ctrl**+**X**, then enter **Y**, and finally **Enter**.

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
        3 rows selected (0.813 seconds)

## <a id="summary"></a><a id="nextsteps"></a>Passaggi successivi

* For more general information on Hive in HDInsight, see [Use Apache Hive with Apache Hadoop on HDInsight](hdinsight-use-hive.md)

* For more information on other ways you can work with Hadoop on HDInsight, see [Use MapReduce with Apache Hadoop on HDInsight](hdinsight-use-mapreduce.md)
