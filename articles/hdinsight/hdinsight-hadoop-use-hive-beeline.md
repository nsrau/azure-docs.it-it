---
title: Usare Hive con Hadoop in HDInsight con Beeline | Documentazione Microsoft
description: "Informazioni su come usare SSH per connettersi a un cluster Hadoop in HDInsight e quindi inviare query Hive in modo interattivo usando Beeline. Beeline è un&quot;utilità per l&quot;utilizzo di HiveServer2 rispetto a JDBC."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 3adfb1ba-8924-4a13-98db-10a67ab24fca
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 93990e342f6bd8fcfe9781bcb021aabfd33e8572
ms.openlocfilehash: c4fb98946a41ee37d8676eca52ff26f81d0ff56a


---
# <a name="use-hive-with-hadoop-in-hdinsight-with-beeline"></a>Usare Hive con Hadoop in HDInsight con Beeline
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

In questo articolo si apprenderà come usare SSH (Secure Shell) per connettersi a un cluster HDInsight basato su Linux e quindi inviare in modo interattivo query Hive tramite lo strumento da riga di comando [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) .

> [!NOTE]
> Beeline usa JDBC per connettersi a Hive. Per altre informazioni sull'uso di JDBC con Hive, vedere [Connettersi a Hive in Azure HDInsight con il driver Hive JDBC](hdinsight-connect-hive-jdbc-driver.md).

## <a name="a-idprereqaprerequisites"></a><a id="prereq"></a>Prerequisiti
Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster Hadoop basato su Linux in HDInsight.

  > [!IMPORTANT]
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* Un client SSH. Linux, Unix e Mac OS dovrebbero essere dotati di un client SSH. Per gli utenti di Windows è necessario scaricare un client, ad esempio [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## <a name="a-idsshaconnect-with-ssh"></a><a id="ssh"></a>Connettersi con SSH
Connettersi al nome di dominio completo (FQDN) del cluster HDInsight usando il comando SSH. L'FQDN è costituito dal nome assegnato al cluster, seguito da **.azurehdinsight.net**. Ad esempio, il seguente comando stabilirà la connessione a un cluster denominato **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se è stata fornita una chiave del certificato per l'autenticazione SSH** durante la creazione del cluster HDInsight, potrebbe essere necessario specificare il percorso della chiave privata nel sistema client:

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Se è stata specificata una password per l'autenticazione SSH** durante la creazione del cluster HDInsight, sarà necessario fornire la password quando richiesto.

Per altre informazioni sull'uso di SSH con HDInsight, vedere [Uso di SSH con Hadoop basato su Linux in HDInsight da Linux, Unix oppure OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="putty-windows-based-clients"></a>PuTTY (client basati su Windows)
Windows non fornisce un client SSH incorporato. È consigliabile usare **PuTTY**, disponibile per il download all'indirizzo [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Per altre informazioni sull'uso di PuTTY, vedere [Uso di SSH con Hadoop basato su Linux in HDInsight da Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

## <a name="a-idbeelineause-the-beeline-command"></a><a id="beeline"></a>Usare il comando Beeline
1. Dopo la connessione, usare il comando seguente per avviare Beeline:
   
        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
   
    Il client Beeline viene avviato e si effettua la connessione all'URL JDBC. Qui si utilizza `localhost` poiché HiveServer2 viene eseguito in entrambi i nodi head del cluster e Beeline è in esecuzione direttamente nel nodo head primario.
   
    Al termine dell'esecuzione del comando, si aprirà il prompt dei comandi `jdbc:hive2://localhost:10001/>`.
2. I comandi di Beeline iniziano di solito con un carattere `!`, ad esempio `!help` visualizza la Guida. È tuttavia possibile omettere `!` . Ad esempio, anche `help` funzionerà.
   
    Se si visualizza la Guida, si noterà `!sql`, che viene usato per eseguire le istruzioni HiveQL. HiveQL è comunque così diffuso da poter omettere il precedente `!sql`. Le due istruzioni seguenti hanno esattamente gli stessi risultati. Visualizzano le tabella disponibili attualmente tramite Hive:
   
        !sql show tables;
        show tables;
   
    In un nuovo cluster dovrebbe essere elencata solo una tabella: **hivesampletable**.
3. Usare il codice seguente per visualizzare lo schema di hivesampletable:
   
        describe hivesampletable;
   
    Verranno restituite informazioni simili alle seguenti:
   
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
   
    Visualizza le colonne nella tabella. Anche se è possibile eseguire alcune query su questi dati, verrà creata una nuova tabella per dimostrare come caricare i dati in Hive e applicare uno schema.
4. Immettere le istruzioni seguenti per creare una nuova tabella denominata **log4jLogs** usando i dati di esempio forniti con il cluster HDInsight:
   
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   
    Di seguito sono elencate le istruzioni che eseguono queste azioni:
   
   * **DROP TABLE** : elimina la tabella e il file di dati, qualora la tabella esista già.
   * **CREATE EXTERNAL TABLE** : crea una nuova tabella "external" in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati rimangono nel percorso originale.
   * **ROW FORMAT** : indica a Hive il modo in cui sono formattati i dati. In questo caso, i campi in ogni log sono separati da uno spazio.
   * **STORED AS TEXTFILE LOCATION** : indica a Hive dove sono archiviati i dati (la directory example/data) e che sono archiviati come testo.
   * **SELECT**: seleziona un numero di tutte le righe in cui la colonna **t4** include il valore **[ERROR]**. Dovrebbe restituire un valore pari a **3** , poiché sono presenti tre righe contenenti questo valore.
   * **INPUT__FILE__NAME come '%.log'** - indica a Hive che si dovrebbero restituire solo i dati da file che terminano con .log. In genere, quando si eseguono query con hive, si dovrebbero avere solo dati con lo stesso schema all'interno della stessa cartella, tuttavia, questo file di log di esempio viene archiviato con altri formati di dati.
     
     > [!NOTE]
     > È consigliabile usare le tabelle esterne quando si prevede che i dati sottostanti vengano aggiornati da un'origine esterna, ad esempio un processo automatico di caricamento dei dati, oppure da un'altra operazione MapReduce, ma si vuole che le query Hive usino sempre i dati più recenti.
     > 
     > L'eliminazione di una tabella esterna **non** comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.
     > 
     > 
     
     L'output di questo comando dovrebbe essere simile al seguente:

     ```
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
     ```
5. Per uscire da Beeline, usare `!quit`.

## <a name="a-idfilearun-a-hiveql-file"></a><a id="file"></a>Eseguire un file HiveQL
Beeline può anche essere usato per eseguire un file che contiene istruzioni HiveQL. Usare la procedura seguente per creare un file, quindi eseguirlo tramite Beeline.

1. Usare il comando seguente per creare un nuovo file denominato **query.hql**:
   
        nano query.hql
2. All'apertura dell'editor, usare il codice seguente come contenuto del file: Questa query crea una nuova tabella "interna" denominata **errorLogs**:
   
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   
    Di seguito sono elencate le istruzioni che eseguono queste azioni:
   
   * **CREATE TABLE IF NOT EXISTS** : crea una tabella, se non esiste già. Poiché non viene usata la parola chiave **EXTERNAL** , questa è una tabella interna che viene archiviata nel data warehouse di Hive e gestita completamente da Hive.
   * **STORED AS ORC** : archivia i dati nel formato ORC (Optimized Row Columnar). Questo è un formato altamente ottimizzato ed efficiente per l'archiviazione di dati Hive.
   * **INSERT OVERWRITE ... SELECT**: seleziona dalla tabella **log4jLogs** le righe contenenti **[ERROR]**, poi inserisce i dati nella tabella **errorLogs**.
     
     > [!NOTE]
     > A differenza di quanto accade con le tabelle esterne, se si elimina una tabella interna verranno eliminati anche i dati sottostanti.
     > 
     > 
3. Per salvare il file usare **Ctrl**+**_X**, quindi immettere **Y** e infine premere **INVIO**.
4. Usare il codice seguente per eseguire il file tramite Beeline: Sostituire **HOSTNAME** con il nome ottenuto in precedenza per il nodo head e **PASSWORD** con la password per l'account amministratore:
   
        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i query.hql
   
   > [!NOTE]
   > Il parametro `-i` avvia Beeline, esegue le istruzioni nel file query.hql e rimane in Beeline nel prompt dei comandi `jdbc:hive2://localhost:10001/>`. È inoltre possibile eseguire un file utilizzando il parametro `-f` , che consente di tornare a Bash dopo l'elaborazione del file.
   > 
   > 
5. Per verificare che la tabella **errorLogs** sia stata creata, usare l'istruzione seguente per restituire tutte le righe da **errorLogs**:
   
        SELECT * from errorLogs;
   
    Dovrebbero essere restituite tre righe di dati, tutte contenenti **[ERROR]** nella colonna t4.
   
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a name="more-about-beeline-connectivity"></a>Informazioni sulla connettività Beeline
La procedura descritta in questo documento usa `localhost` per connettersi a HiveServer2 in esecuzione sul nodo head del cluster. Sebbene sia anche possibile usare il nome host o il nome di dominio completo del nodo head, questi richiedono passaggi aggiuntivi per eseguire il processo (procedure per individuare il nome host o FQDN). `localhost` è sufficiente quando si usa Beeline dal nodo head.

Se nel cluster è presente un nodo perimetrale e Beeline è installato, per eseguire la connessione è necessario utilizzare il nome host o l'FQDN del nodo head.

Se Beeline è installato in un client esterno al cluster, è possibile connettersi usando il comando seguente. Sostituire **CLUSTERNAME** con il nome del cluster HDInsight. Sostituire **PASSWORD** con la password dell'account amministratore (accesso HTTP).

    beeline -u 'jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=hive2' -n admin -p PASSWORD

Si noti che parametri/URI sono differenti rispetto all'esecuzione diretta in un nodo head o da un nodo perimetrale interno al cluster. Questo avviene perché la connessione al cluster da Internet usa un gateway pubblico che instrada il traffico sulla porta 443. Inoltre, il gateway pubblico sulla porta 443 espone anche molti altri servizi, pertanto l'URI è diverso rispetto alla connessione diretta. Se ci si connette da Internet è necessario anche autenticare la sessione inserendo la password.

## <a name="a-idsummaryaa-idnextstepsanext-steps"></a><a id="summary"></a><a id="nextsteps"></a>Passaggi successivi
Come è possibile osservare, il comando Beeline fornisce un modo semplice per eseguire query Hive in un cluster HDInsight in modo interattivo.

Per informazioni generali su Hive in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Se si usa Tez con Hive, vedere i documenti seguenti per le informazioni di debug:

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


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx




<!--HONumber=Feb17_HO3-->


