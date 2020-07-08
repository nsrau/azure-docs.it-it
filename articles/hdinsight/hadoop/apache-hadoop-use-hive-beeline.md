---
title: Usare Apache Beeline con Apache Hive - Azure HDInsight
description: Informazioni su come usare il client Beeline per eseguire query Hive con Hadoop in HDInsight. Beeline è un'utilità per l'utilizzo di HiveServer2 rispetto a JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: 46d0a21ac1461b2553b8262b913aada3fa2a1b6f
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86081303"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Usare il client Apache Beeline con Apache Hive

Informazioni su come usare [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) per eseguire query Apache Hive in HDInsight.

Beeline è un client Hive incluso nei nodi head del cluster HDInsight. Per eseguire la connessione al client con estensione per il cluster HDInsight o installare l'oggetto con l'uso di una connessione locale, vedere la pagina relativa alla [connessione o all'installazione di Apache caeline](connect-install-beeline.md). Beeline usa JDBC per connettersi a HiveServer2, un servizio ospitato nel cluster HDInsight. È anche possibile usare Beeline per accedere in remoto a Hive in HDInsight tramite Internet. Gli esempi seguenti forniscono le stringhe di connessione più comuni usate per la connessione a HDInsight da deeline.

## <a name="prerequisites-for-examples"></a>Prerequisiti per gli esempi

* Un cluster Hadoop in HDInsight. Vedere [Guida introduttiva: Introduzione ad Apache Hadoop e Apache Hive in Azure HDInsight usando il modello di Resource Manager](./apache-hadoop-linux-tutorial-get-started.md).

* Si noti lo schema URI per l'archiviazione primaria del cluster. Ad esempio, `wasb://` per archiviazione di Azure, `abfs://` per Azure Data Lake storage Gen2 o `adl://` per Azure Data Lake storage Gen1. Se il trasferimento sicuro è abilitato per archiviazione di Azure, l'URI è `wasbs://` . Per altre informazioni, vedere [trasferimento sicuro](../../storage/common/storage-require-secure-transfer.md).

* Opzione 1: un client SSH. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Per la maggior parte dei passaggi di questo documento si presuppone che si stia usando una sessione SSH nel cluster.

* Opzione 2: un client di Oneline locale.

## <a name="run-a-hive-query"></a>Eseguire una query Hive

Questo esempio si basa sull'uso del client con estensione da una connessione SSH.

1. Aprire una connessione SSH al cluster con il codice seguente. Sostituire `sshuser` con il nome utente SSH del cluster e sostituire `CLUSTERNAME` con il nome del cluster. Quando richiesto, immettere la password per l'account utente SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Connettersi a HiveServer2 con il client dell'utente corrente dalla sessione SSH aperta immettendo il comando seguente:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. I comandi di Beeline iniziano di solito con un carattere `!`, ad esempio `!help` visualizza la Guida. Tuttavia il carattere `!` può essere omesso per alcuni comandi. Ad esempio, anche `help` funziona.

    `!sql`Viene usato per eseguire le istruzioni HiveQL. HiveQL è comunque così diffuso da poter omettere il precedente `!sql`. Le due istruzioni seguenti sono equivalenti:

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

5. Immettere le istruzioni seguenti per creare una tabella denominata **log4jLogs** usando i dati di esempio forniti con il cluster HDInsight: (rivedere la richiesta in base allo schema URI.)

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

    Queste istruzioni eseguono le azioni seguenti:

    |. |Descrizione |
    |---|---|
    |DROP TABLE|Se la tabella esiste, viene eliminata.|
    |CREATE EXTERNAL TABLE|Crea una tabella **esterna** in hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati rimangono nel percorso originale.|
    |FORMATO DI RIGA|Modalità di formattazione dei dati. In questo caso, i campi in ogni log sono separati da uno spazio.|
    |ARCHIVIATO COME PERCORSO DI TEXTFILE|Dove vengono archiviati i dati e in quale formato di file.|
    |SELECT|Seleziona un conteggio di tutte le righe in cui la colonna **T4** contiene il valore **[Error]**. Questa query restituisce **3**, poiché sono presenti tre righe contenenti questo valore.|
    |INPUT__FILE__NAME LIKE '%. log '|Hive tenta di applicare lo schema a tutti i file nella directory. In questo caso, la directory contiene file che non corrispondono allo schema. Per evitare dati errati nei risultati, questa istruzione indica a Hive di restituire dati solo da file che terminano con .log.|

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

6. Esci da:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>Eseguire un file HiveQL

Questo esempio è una continuazione dell'esempio precedente. Usare la procedura seguente per creare un file, quindi eseguirlo tramite Beeline.

1. Usare il comando seguente per creare un file denominato **query.hql**:

    ```bash
    nano query.hql
    ```

1. Usare il testo seguente come contenuto del file. Questa query crea una nuova tabella "interna" denominata **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Queste istruzioni eseguono le azioni seguenti:

    |. |Descrizione |
    |---|---|
    |CREATE TABLE SE NON ESISTE|Se la tabella non esiste già, viene creata. Poiché non viene usata la parola chiave **External** , questa istruzione crea una tabella interna. Le tabelle interne vengono archiviate nel data warehouse di Hive e sono totalmente gestite da Hive.|
    |ARCHIVIATO COME ORC|archivia i dati nel formato ORC (Optimized Row Columnar). ORC è un formato altamente ottimizzato ed efficiente per l'archiviazione di dati Hive.|
    |INSERISCI SOVRASCRITTURA... Selezionare|seleziona dalla tabella **log4jLogs** le righe contenenti **[ERROR]**, poi inserisce i dati nella tabella **errorLogs**.|

    > [!NOTE]  
    > A differenza delle tabelle esterne, se si elimina una tabella interna, vengono eliminati anche i dati sottostanti.

1. Per salvare il file, usare **CTRL** + **X**, quindi immettere **Y**e infine **premere invio**.

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

    Devono essere restituite tre righe di dati, tutte contenenti **[Error]** nella colonna T4:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (0.813 seconds)

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni generali su hive in HDInsight, vedere [usare Apache hive con Apache Hadoop in HDInsight](hdinsight-use-hive.md)

* Per altre informazioni su altri modi per lavorare con Hadoop in HDInsight, vedere [usare MapReduce con Apache Hadoop su HDInsight](hdinsight-use-mapreduce.md)
