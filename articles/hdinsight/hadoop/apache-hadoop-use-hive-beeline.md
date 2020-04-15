---
title: Usare Apache Beeline con Apache Hive - Azure HDInsight
description: Informazioni su come usare il client Beeline per eseguire query Hive con Hadoop in HDInsight. Beeline è un'utilità per l'utilizzo di HiveServer2 rispetto a JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 426294f20dd51538920182a0e7a2915f6a47ba54
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383564"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Usare il client Apache Beeline con Apache Hive

Informazioni su come usare [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) per eseguire query Apache Hive in HDInsight.

Beeline è un client Hive incluso nei nodi head del cluster HDInsight. Per installare Beeline localmente, vedere Installare il [client beeline](#install-beeline-client), di seguito. Beeline usa JDBC per connettersi a HiveServer2, un servizio ospitato nel cluster HDInsight. È anche possibile usare Beeline per accedere in remoto a Hive in HDInsight tramite Internet. The following examples provide the most common connection strings used to connect to HDInsight from Beeline.

## <a name="types-of-connections"></a>Tipi di connessioni

### <a name="from-an-ssh-session"></a>Da una sessione SSH

Quando ci si connette da una sessione SSH a `headnodehost` un nodo `10001`head del cluster, è quindi possibile connettersi all'indirizzo sulla porta :

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Su una rete virtuale di AzureOver an Azure Virtual Network

Quando ci si connette da un client a HDInsight tramite una rete virtuale di Azure, è necessario fornire il nome di dominio completo (FQDN) di un nodo head del cluster. Poiché questa connessione viene eseguita direttamente ai nodi del cluster, la connessione usa la porta `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

<a name="replace-headnode-fqdn-with-the-fully-qualified-domain-name-of-a-cluster-headnode-to-find-the-fully-qualified-domain-name-of-a-headnode-use-the-information-in-the-manage-hdinsight-using-the-apache-ambari-rest-api-document"></a>Sostituire `<headnode-FQDN>` con il nome di dominio completo di un nodo head del cluster. Per trovare il nome di dominio completo di un nodo head, usare le informazioni contenute nel documento [Gestire i cluster HDInsight mediante l'API REST Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes).
---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Per il cluster HDInsight Enterprise Security Package (ESP) tramite Kerberos

Quando ci si connette da un client a un cluster Enterprise Security Package (ESP) aggiunto ad Azure Active Directory (AAD)-DS in un computer nella stessa area di autenticazione del cluster, è inoltre necessario specificare il nome `<AAD-Domain>` di dominio e il nome di un account utente di dominio con le autorizzazioni per accedere al cluster: `<username>`

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Sostituire `<username>` con il nome di un account nel dominio che disponga delle autorizzazioni per accedere al cluster. Sostituire `<AAD-DOMAIN>` con il nome di Azure Active Directory (AAD) a cui è aggiunto il cluster. Utilizzare una stringa maiuscola per il `<AAD-DOMAIN>` valore, altrimenti la credenziale non verrà trovata. Se `/etc/krb5.conf` necessario, verificare i nomi delle aree di autenticazione.

Per trovare l'URL JDBC da Ambari:

1. Da un Web browser `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`passare `CLUSTERNAME` a , dove è il nome del cluster. Verificare che HiveServer2 sia in esecuzione.

1. Utilizzare gli Appunti per copiare l'URL JDBC HiveServer2.

---

### <a name="over-public-or-private-endpoints"></a>Su endpoint pubblici o privati

Quando ci si connette a un cluster utilizzando gli endpoint pubblici o `admin`privati, è necessario specificare il nome dell'account di accesso del cluster (impostazione predefinita) e la password. Ad esempio, l'uso di Beeline da un sistema client per connettersi all'indirizzo `clustername.azurehdinsight.net`. Questa connessione viene `443`effettuata tramite la porta e viene crittografata utilizzando TLS/SSL.

Sostituire `clustername` con il nome del cluster HDInsight. Sostituire `admin` con l'account di accesso del cluster. Per i cluster ESP, utilizzare l'UPN completo, user@domain.comad esempio . Sostituire `password` con la password dell'account di accesso del cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

o per endpoint privati:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Gli endpoint privati puntano a un servizio di bilanciamento del carico di base, a cui è possibile accedere solo dai VNET sottoposti a peered nella stessa area. Per altre info, vedi [i vincoli sul peering globale della rete virtuale e sui servizi di bilanciamento del carico.](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) È possibile `curl` utilizzare `-v` il comando con l'opzione per risolvere eventuali problemi di connettività con endpoint pubblici o privati prima di usare beeline.

---

### <a name="use-beeline-with-apache-spark"></a>Usare Beeline con Apache Spark

Apache Spark fornisce la propria implementazione di HiveServer2, spesso definita come server Spark Thrift. Questo servizio usa Spark SQL invece di Hive per risolvere le query e può offrire prestazioni migliori a seconda della query.

#### <a name="through-public-or-private-endpoints"></a>Tramite endpoint pubblici o privati

La stringa di connessione utilizzata è leggermente diversa. Invece `httpPath=/hive2` di contenerlo è `httpPath/sparkhive2`. Sostituire `clustername` con il nome del cluster HDInsight. Sostituire `admin` con l'account di accesso del cluster. Per i cluster ESP, utilizzare l'UPN completo, user@domain.comad esempio . Sostituire `password` con la password dell'account di accesso del cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

o per endpoint privati:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Gli endpoint privati puntano a un servizio di bilanciamento del carico di base, a cui è possibile accedere solo dai VNET sottoposti a peered nella stessa area. Per altre info, vedi [i vincoli sul peering globale della rete virtuale e sui servizi di bilanciamento del carico.](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) È possibile `curl` utilizzare `-v` il comando con l'opzione per risolvere eventuali problemi di connettività con endpoint pubblici o privati prima di usare beeline.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Dalla testa del cluster o dalla rete virtuale di Azure con Apache Spark

Quando ci si connette direttamente dal nodo head del cluster o da una risorsa all'interno della stessa istanza di Rete virtuale di Azure del cluster HDInsight, è necessario usare la porta `10002` per il server Spark Thrift invece di `10001`. L'esempio seguente mostra come connettersi direttamente al nodo head:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a name="prerequisites-for-examples"></a>Prerequisiti per gli esempi

* Un cluster Hadoop in HDInsight.A Hadoop cluster on HDInsight. Vedere [Introduzione a HDInsight su Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Si noti lo [schema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) per l'archiviazione primaria del cluster. Ad esempio, `wasb://` per `abfs://` Archiviazione di Azure, per `adl://` Azure Data Lake Storage Gen2 o per Azure Data Lake Storage Gen1.For example, for Azure Storage, for Azure Data Lake Storage Gen2, or for Azure Data Lake Storage Gen1. Se il trasferimento sicuro è abilitato `wasbs://`per Archiviazione di Azure, l'URI è . Per ulteriori informazioni, vedere [trasferimento sicuro](../../storage/common/storage-require-secure-transfer.md).

* Opzione 1: un client SSH. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). La maggior parte dei passaggi descritti in questo documento presuppone che si stia utilizzando Beeline da una sessione SSH al cluster.

* Opzione 2: un client Beeline locale.

## <a name="run-a-hive-query"></a>Eseguire una query Hive

Questo esempio si basa sull'utilizzo del client Beeline da una connessione SSH.

1. Aprire una connessione SSH al cluster con il codice riportato di seguito. Sostituire `sshuser` con il nome utente SSH del cluster e sostituire `CLUSTERNAME` con il nome del cluster. Quando richiesto, immettere la password per l'account utente SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Connettersi a HiveServer2 con il client Beeline dalla sessione SSH aperta immettendo il comando seguente:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. I comandi di Beeline iniziano di solito con un carattere `!`, ad esempio `!help` visualizza la Guida. Tuttavia il carattere `!` può essere omesso per alcuni comandi. Ad esempio, anche `help` funziona.

    C'è `!sql`, che viene utilizzato per eseguire le istruzioni HiveQL. HiveQL è comunque così diffuso da poter omettere il precedente `!sql`. Le due istruzioni seguenti sono equivalenti:

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

5. Immettere le istruzioni seguenti per creare una tabella denominata **log4jLogs** usando i dati di esempio forniti con il cluster HDInsight: (rivedere in base alle esigenze in base [allo schema URI).](../hdinsight-hadoop-linux-information.md#URI-and-scheme)

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

    Queste istruzioni eseguire le azioni seguenti:These statements do the following actions:

    |. |Descrizione |
    |---|---|
    |DROP TABLE|Se la tabella esiste, viene eliminata.|
    |CREA TABELLA ESTERNA|Crea una tabella **esterna** in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati rimangono nel percorso originale.|
    |FORMATO RIGA|Modalità di formattazione dei dati. In questo caso, i campi in ogni log sono separati da uno spazio.|
    |MEMORIZZATO COME PERCORSO FILE DI TESTO|Dove sono memorizzati i dati e in quale formato di file.|
    |SELECT|Seleziona un conteggio di tutte le righe in cui la colonna **t4** contiene il valore **[ERRORE]**. Questa query restituisce **3**, poiché sono presenti tre righe contenenti questo valore.|
    |INPUT__FILE__NAME LIKE '%.log'|Hive tenta di applicare lo schema a tutti i file nella directory. In questo caso, la directory contiene file che non corrispondono allo schema. Per evitare dati errati nei risultati, questa istruzione indica a Hive di restituire dati solo da file che terminano con .log.|

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

6. Uscire da Beeline:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>Eseguire un file HiveQL

Si tratta di una continuazione dell'esempio precedente. Usare la procedura seguente per creare un file, quindi eseguirlo tramite Beeline.

1. Usare il comando seguente per creare un file denominato **query.hql**:

    ```bash
    nano query.hql
    ```

1. Usare il testo seguente come contenuto del file. Questa query crea una nuova tabella "interna" denominata **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Queste istruzioni eseguire le azioni seguenti:These statements do the following actions:

    |. |Descrizione |
    |---|---|
    |CREA TABELLA SE NON ESISTE|Se la tabella non esiste già, viene creata. Poiché la parola chiave **EXTERNAL** non viene utilizzata, questa istruzione crea una tabella interna. Le tabelle interne vengono archiviate nel data warehouse di Hive e sono totalmente gestite da Hive.|
    |STORED AS ORC|archivia i dati nel formato ORC (Optimized Row Columnar). ORC è un formato altamente ottimizzato ed efficiente per l'archiviazione di dati Hive.|
    |INSERISCI SOVRASCRITTURA... Selezionare|seleziona dalla tabella **log4jLogs** le righe contenenti **[ERROR]**, poi inserisce i dati nella tabella **errorLogs**.|

    > [!NOTE]  
    > A differenza delle tabelle esterne, se si elimina una tabella interna, vengono eliminati anche i dati sottostanti.

1. Per salvare il file, utilizzare **Ctrl**+**X**, quindi immettere **Y**e infine **invio**.

1. Usare il codice seguente per eseguire il file tramite Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > Il parametro `-i` avvia Beeline ed esegue le istruzioni nel file `query.hql`. Dopo il completamento della query, viene visualizzato il prompt `jdbc:hive2://headnodehost:10001/>`. È anche possibile eseguire un file usando il parametro `-f`, che chiude Beeline dopo il completamento della query.

1. Per verificare che la tabella **errorLogs** sia stata creata, usare l'istruzione seguente per restituire tutte le righe da **errorLogs**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Devono essere restituite tre righe di dati, tutte contenenti **[ERROR]** nella colonna t4:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (0.813 seconds)

## <a name="install-beeline-client"></a>Installare il client beeline

Anche se Beeline è incluso nei nodi head del cluster HDInsight, è consigliabile installarlo in un computer locale.  I passaggi seguenti per installare Beeline in un computer locale sono basati su un [sottosistema Windows per Linux.](https://docs.microsoft.com/windows/wsl/install-win10)

1. Aggiornare gli elenchi dei pacchetti. Immettere il seguente comando nella shell bash:

    ```bash
    sudo apt-get update
    ```

1. Installare Java se non è installato. È possibile verificare con il `which java` comando.

    1. Se non è installato alcun pacchetto java, immettere il seguente comando:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Aprire il file bashrc (in genere si `nano ~/.bashrc`trova in .bashrc): .

    1. Modificare il file bashrc. Aggiungere la riga seguente alla fine del file:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Premere quindi **Ctrl , X**, quindi **Y**, quindi immettere .

1. Scaricare gli archivi Hadoop e Beeline, immettere i seguenti comandi:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Decomprimere gli archivi, immettere i seguenti comandi:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Modificare ulteriormente il file bashrc. È necessario identificare il percorso in cui sono stati decompressi gli archivi. Se si utilizza il [sottosistema Windows per Linux](https://docs.microsoft.com/windows/wsl/install-win10)e si `/mnt/c/Users/user/`seguono esattamente i passaggi, il percorso sarà , dov'è `user` il nome utente.

    1. Aprire il file:`nano ~/.bashrc`

    1. Modificare i comandi seguenti con il percorso appropriato, quindi immetterli alla fine del file bashrc:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Premere quindi **Ctrl , X**, quindi **Y**, quindi immettere .

1. Chiudere e riaprire la sessione bash.

1. Verificare la connessione. Utilizzare il formato di connessione [da Over endpoint pubblici o privati](#over-public-or-private-endpoints), sopra.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni generali su Hive in HDInsight, vedere [Usare Apache Hive con Apache Hadoop in HDInsightFor](hdinsight-use-hive.md) more general information on Hive in HDInsight, see Use Apache Hive with Apache Hadoop on HDInsight

* Per altre informazioni su altri modi in cui è possibile usare Hadoop su HDInsight, vedere [Usare MapReduce con Apache Hadoop in HDInsightFor](hdinsight-use-mapreduce.md) more information on other ways you can work with Hadoop on HDInsight, see Use MapReduce with Apache Hadoop on HDInsight
