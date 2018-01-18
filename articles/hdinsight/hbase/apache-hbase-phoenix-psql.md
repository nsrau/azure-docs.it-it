---
title: Caricamento bulk di dati in Apache Phoenix tramite psql - Azure HDInsight | Microsoft Docs
description: Usare lo strumento psql per il caricamento bulk dei dati nelle tabelle di Phoenix.
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: 0a623113ee9f3fe2c0f5f616ecd79b8311a8ffc1
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="bulk-load-data-into-phoenix-using-psql"></a>Caricamento bulk di dati in Phoenix tramite psql

[Apache Phoenix](http://phoenix.apache.org/) è un database relazionale open source altamente parallelo basato su [HBase](../hbase/apache-hbase-overview.md). Phoenix fornisce query simili a SQL tramite HBase. Phoenix usa i driver JDBC per consentire agli utenti di creare, eliminare e modificare tabelle, indici, viste e sequenze, nonché righe upsert SQL singolarmente e in blocco. Phoenix usa la compilazione nativa noSQL anziché MapReduce per compilare query e crea applicazioni a bassa latenza basate su HBase. Phoenix aggiunge coprocessori per supportare l'esecuzione del codice fornito dal client nello spazio degli indirizzi del server, eseguendo il codice che si trova nello stesso percorso dei dati. In questo modo viene ridotto al minimo il trasferimento di dati client/server.  Per usare i dati tramite Phoenix in HDInsight, creare innanzitutto le tabelle e quindi caricarci i dati.

## <a name="bulk-loading-with-phoenix"></a>Caricamento bulk con Phoenix

Esistono diversi modi per caricare i dati in HBase, tra cui l'uso di API client, un processo MapReduce con TableOutputFormat o l'immissione manuale dei dati tramite la shell di HBase. Phoenix offre due metodi per caricare i dati CSV nelle tabelle di Phoenix: uno strumento di caricamento client denominato `psql` e uno strumento di caricamento bulk basato su MapReduce.

`psql` è uno strumento a thread singolo ed è ideale per il caricamento di megabyte o gigabyte di dati. Tutti i file CSV da caricare devono avere l'estensione csv.  È anche possibile specificare i file di script SQL nella riga di comando `psql` con l'estensione sql.

Il caricamento bulk con MapReduce viene usato per volumi di dati maggiori, in genere negli scenari di produzione, in quanto MapReduce usa più thread.

Prima di avviare il caricamento dei dati, verificare che Phoenix sia abilitato e che le impostazioni di timeout delle query siano quelle previste.  Accedere al dashboard Ambari del cluster HDInsight, selezionare HBase e quindi scegliere la scheda di configurazione.  Scorrere verso il basso per verificare che Apache Phoenix sia impostato su `enabled` come illustrato di seguito:

![Impostazioni del cluster HDInsight di Apache Phoenix](./media/apache-hbase-phoenix-psql/ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Usare `psql` per il caricamento bulk delle tabelle

1. Creare una nuova tabella, quindi salvare la query con nome file `createCustomersTable.sql`.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. Copiare il file CSV (contenuti di esempio illustrati di seguito) come `customers.csv` in una directory `/tmp/` per il caricamento nella tabella appena creata.  Usare il comando `hdfs` per copiare il file CSV nel percorso di origine desiderato.

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. Creare una query SQL SELECT per verificare che i dati di input siano stati caricati correttamente, quindi salvare la query con nome file `listCustomers.sql`. È possibile usare qualsiasi query SQL.
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. Eseguire il caricamento bulk dei dati aprendo un *nuova* finestra di comando Hadoop. Passare al percorso di esecuzione della directory con il comando `cd`, quindi usare lo strumento `psql` (comando Python `psql.py`). 

    Nell'esempio seguente si presume che il file `customers.csv` sia stato copiato da un account di archiviazione alla directory temporanea locale tramite `hdfs`, come illustrato in precedenza nel passaggio 2.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE] 
    > Per determinare il nome di `ZookeeperQuorum`, individuare la stringa del quorum Zookeeper nel file `/etc/hbase/conf/hbase-site.xml` con nome di proprietà `hbase.zookeeper.quorum`.

5. Al termine dell'operazione `psql`, verrà visualizzato un messaggio nella finestra di comando:

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Usare MapReduce per il caricamento bulk delle tabelle

Per un caricamento con velocità effettiva più elevata distribuito nel cluster, usare lo strumento di caricamento di MapReduce. Questo caricatore innanzitutto converte tutti i dati in HFiles e quindi fornisce i HFiles creati a HBase.

1. Avviare il caricatore CSV di MapReduce usando il comando `hadoop` con il file jar del client Phoenix:

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. Creare una nuova tabella con un'istruzione SQL, analogamente a quanto fatto per `CreateCustomersTable.sql` nel passaggio 1.

3. Per verificare lo schema della tabella, eseguire `!describe inputTable`.

4. Determinare il percorso dei dati di input, come nel file `customers.csv` di esempio. I file di input possono trovarsi nell'account di archiviazione WASB/ADLS. In questo scenario di esempio i file di input si trovano nella directory `<storage account parent>/inputFolderBulkLoad`.

5. Passare alla directory di esecuzione per il comando di caricamento bulk di MapReduce:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. Individuare il valore `ZookeeperQuorum` in `/etc/hbase/conf/hbase-site.xml`, con il nome di proprietà `hbase.zookeeper.quorum`.

7. Impostare il classpath ed eseguire lo strumento di comando `CsvBulkLoadTool`:

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. Per usare MapReduce con ADLS, individuare la directory radice ADLS, che corrisponde al valore `hbase.rootdir` in `hbase-site.xml`. Nel comando seguente la directory radice ADLS è `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`. In questo comando specificare le cartelle ADLS di input e di output come parametri:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>Raccomandazioni

* Usare lo stesso supporto di archiviazione per le cartelle di input e di output WASB o ADLS. Per trasferire dati da WASB ad ADLS, usare il comando `distcp`:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Usare nodi di lavoro di dimensioni maggiori. I processi di mapping della copia bulk di MapReduce producono grandi quantità di file di output temporanei che riempiono lo spazio disponibile non-DFS. Per un caricamento bulk di grandi dimensioni, usare più nodi di lavoro e nodi di lavoro più grandi. Il numero di nodi di lavoro allocati al cluster influisce direttamente sulla velocità di elaborazione.

* Suddividere i file di input in blocchi di circa 10 GB. Il caricamento bulk è un'operazione con uso intensivo delle risorse di archiviazione, pertanto la suddivisione dei file di input in più blocchi garantisce prestazioni migliori.

* Evitare le aree sensibili del server di area. Se la chiave di riga aumenta in maniera monotona, le scritture sequenziali di HBase possono causare la formazione di aree sensibili nel server di area. L'uso di *valori salt* nella chiave di riga consente di ridurre le scritture sequenziali. Phoenix offre un modo per usare valori salt in modo trasparente nella chiave di riga con un byte di salt per una determinata tabella, come illustrato negli articoli di seguito.

## <a name="next-steps"></a>Passaggi successivi

* [Bulk Data Loading with Apache Phoenix](http://phoenix.apache.org/bulk_dataload.html) (Caricamento bulk di dati con Apache Phoenix)
* [Usare Apache Phoenix con cluster HBase basati su Linux in HDInsight](../hbase/apache-hbase-phoenix-squirrel-linux.md)
* [Salted Tables](https://phoenix.apache.org/salted.html) (Tabelle con valori salt)
* [Phoenix Grammar](http://phoenix.apache.org/language/index.html) (Grammatica di Phoenix)
