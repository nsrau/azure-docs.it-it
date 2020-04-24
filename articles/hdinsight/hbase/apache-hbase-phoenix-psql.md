---
title: Caricamento bulk di dati in Apache Phoenix tramite psql - Azure HDInsight
description: Usare lo strumento PSQL per caricare i dati di caricamento bulk in tabelle Apache Phoenix in Azure HDInsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 845c4a62aee04a8acdc645ba4c41f1f5496537c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552611"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Caricamento bulk di dati in Apache Phoenix tramite psql

[Apache Phoenix](https://phoenix.apache.org/) è un database relazionale open source altamente parallelo basato su [Apache HBase](../hbase/apache-hbase-overview.md). Phoenix fornisce query simili a SQL tramite HBase. Phoenix usa i driver JDBC per consentire agli utenti di creare, eliminare e modificare tabelle, indici, viste e sequenze, nonché righe upsert SQL singolarmente e in blocco. Phoenix usa la compilazione nativa noSQL anziché MapReduce per compilare query e crea applicazioni a bassa latenza basate su HBase. Phoenix aggiunge coprocessori per supportare l'esecuzione del codice fornito dal client nello spazio degli indirizzi del server, eseguendo il codice che si trova nello stesso percorso dei dati. In questo modo viene ridotto al minimo il trasferimento di dati client/server.  Per usare i dati tramite Phoenix in HDInsight, creare innanzitutto le tabelle e quindi caricarci i dati.

## <a name="bulk-loading-with-apache-phoenix"></a>Caricamento bulk di dati con Apache Phoenix

Esistono diversi modi per caricare i dati in HBase, tra cui l'uso di API client, un processo MapReduce con TableOutputFormat o l'immissione manuale dei dati tramite la shell di HBase. Phoenix offre due metodi per caricare i dati CSV nelle tabelle di Phoenix: uno strumento di caricamento client denominato `psql` e uno strumento di caricamento bulk basato su MapReduce.

`psql` è uno strumento a thread singolo ed è ideale per il caricamento di megabyte o gigabyte di dati. Tutti i file CSV da caricare devono avere l'estensione csv.  È anche possibile specificare i file di script SQL nella riga di comando `psql` con l'estensione sql.

Il caricamento bulk con MapReduce viene usato per volumi di dati maggiori, in genere negli scenari di produzione, in quanto MapReduce usa più thread.

Prima di avviare il caricamento dei dati, verificare che Phoenix sia abilitato e che le impostazioni di timeout delle query siano quelle previste.  Accedere al dashboard [Apache Ambari](https://ambari.apache.org/) del cluster HDInsight, selezionare HBase e quindi la scheda configurazione.  Scorrere verso il basso per verificare che Apache Phoenix sia `enabled` impostato su come illustrato:

![Impostazioni del cluster HDInsight di Apache Phoenix](./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Usare `psql` per il caricamento bulk delle tabelle

1. Creare un file denominato `createCustomersTable.sql`e copiare il codice seguente nel file. Salvare quindi il file e chiuderlo.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

1. Creare un file denominato `listCustomers.sql`e copiare il codice seguente nel file. Salvare quindi il file e chiuderlo.

    ```sql
    SELECT * from Customers;
    ```

1. Creare un file denominato `customers.csv`e copiare il codice seguente nel file. Salvare quindi il file e chiuderlo.

    ```txt
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,42,Norway
    ```

1. Creare un file denominato `customers2.csv`e copiare il codice seguente nel file. Salvare quindi il file e chiuderlo.

    ```txt
    4,Nicolle,180000.0,22,US
    5,Kate,210000.5,24,Canada
    6,Ben,45000.0,32,Poland
    ```

1. Aprire un prompt dei comandi e passare alla directory in cui si trova il percorso dei file appena creati. Sostituire CLUSTERname, sotto, con il nome effettivo del cluster HBase. Eseguire quindi il codice per caricare i file nel nodo head del cluster:

    ```cmd
    scp customers.csv customers2.csv createCustomersTable.sql listCustomers.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/tmp
    ```

1. Usare il [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) per connettersi al cluster. Modificare il comando seguente sostituendo CLUSTERname con il nome del cluster e quindi immettere il comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Dalla sessione SSH passare alla directory del percorso dello strumento **PSQL** . Eseguire il comando seguente:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

1. Caricamento bulk dei dati. Il codice riportato di seguito creerà la tabella **Customers** e quindi caricherà i dati.

    ```bash
    python psql.py /tmp/createCustomersTable.sql /tmp/customers.csv
    ```

    Al termine `psql` dell'operazione, verrà visualizzato un messaggio simile al seguente:

    ```output
    csv columns from database.
    CSV Upsert complete. 3 rows upserted
    Time: 0.081 sec(s)
    ```

1. È possibile continuare a utilizzare `psql` per visualizzare il contenuto della tabella Customers. Eseguire il codice seguente:

    ```bash
    python psql.py /tmp/listCustomers.sql
    ```

    In alternativa, è possibile usare [HBase Shell](./query-hbase-with-hbase-shell.md)o [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md) per eseguire query sui dati.

1. Caricare dati aggiuntivi. Ora che la tabella esiste già, il comando specifica la tabella. Eseguire il comando seguente:

    ```bash
    python psql.py -t CUSTOMERS /tmp/customers2.csv
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Usare MapReduce per il caricamento bulk delle tabelle

Per un caricamento con velocità effettiva più elevata distribuito nel cluster, usare lo strumento di caricamento di MapReduce. Questo caricatore innanzitutto converte tutti i dati in HFiles e quindi fornisce i HFiles creati a HBase.

1. Questa sezione continua con la sessione SSH e gli oggetti creati in precedenza. Creare la tabella **Customers** e il file **Customers. csv** in base alle esigenze seguendo la procedura descritta sopra. Se necessario, ristabilire la connessione SSH.

1. Troncare il contenuto della tabella **Customers** . Dalla sessione ssh aperta, eseguire i comandi seguenti:

    ```bash
    hbase shell
    truncate 'CUSTOMERS'
    exit
    ```

1. Copiare il `customers.csv` file da nodo head in archiviazione di Azure.

    ```bash
    hdfs dfs -put /tmp/customers.csv wasbs:///tmp/customers.csv
    ```

1. Passare alla directory di esecuzione per il comando di caricamento bulk di MapReduce:

    ```bash
    cd /usr/hdp/current/phoenix-client
    ```

1. Avviare il caricatore CSV di MapReduce usando il comando `hadoop` con il file jar del client Phoenix:

    ```bash
    HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar phoenix-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /tmp/customers.csv
    ```

    Al termine del caricamento, verrà visualizzato un messaggio simile al seguente:

    ```output
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing master protocol: MasterService
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing zookeeper sessionid=0x26f15dcceff02c3
    19/12/18 18:30:57 INFO zookeeper.ZooKeeper: Session: 0x26f15dcceff02c3 closed
    19/12/18 18:30:57 INFO zookeeper.ClientCnxn: EventThread shut down
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Incremental load complete for table=CUSTOMERS
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Removing output directory /tmp/50254426-aba6-400e-88eb-8086d3dddb6
    ```

1. Per usare MapReduce con Azure Data Lake Storage, individuare la directory radice di Azure Data Lake Storage, ovvero il valore `hbase.rootdir` in `hbase-site.xml`. Nel comando seguente, la directory radice di Azure Data Lake Storage è `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`. In questo comando specificare le cartelle di input e di output di Azure Data Lake Storage come parametri:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

1. Per eseguire una query e visualizzare i dati, è possibile usare **PSQL** come descritto in precedenza. È anche possibile usare [HBase Shell](./query-hbase-with-hbase-shell.md)o [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md).

## <a name="recommendations"></a>Consigli

* Usare lo stesso supporto di archiviazione per le cartelle di input e di output: Archiviazione di Azure (WASB) o Azure Data Lake Storage (ADL). Per trasferire i dati da Archiviazione di Azure ad Azure Data Lake Storage, è possibile usare il comando `distcp`:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Usare nodi di lavoro di dimensioni maggiori. I processi di mapping della copia bulk di MapReduce producono grandi quantità di file di output temporanei che riempiono lo spazio disponibile non-DFS. Per un caricamento bulk di grandi dimensioni, usare più nodi di lavoro e nodi di lavoro più grandi. Il numero di nodi di lavoro allocati al cluster influisce direttamente sulla velocità di elaborazione.

* Suddividere i file di input in blocchi di circa 10 GB. Il caricamento bulk è un'operazione con uso intensivo delle risorse di archiviazione, pertanto la suddivisione dei file di input in più blocchi garantisce prestazioni migliori.

* Evitare le aree sensibili del server di area. Se la chiave di riga aumenta in maniera monotona, le scritture sequenziali di HBase possono causare la formazione di aree sensibili nel server di area. L'uso di *valori salt* nella chiave di riga consente di ridurre le scritture sequenziali. Phoenix offre un modo per usare valori salt in modo trasparente nella chiave di riga con un byte di salt per una determinata tabella, come illustrato negli articoli di seguito.

## <a name="next-steps"></a>Passaggi successivi

* [Bulk Data Loading with Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html) (Caricamento bulk di dati con Apache Phoenix)
* [Usare Apache Phoenix con cluster Apache HBase basati su Linux in HDInsight](../hbase/apache-hbase-query-with-phoenix.md)
* [Tabelle con salting](https://phoenix.apache.org/salted.html)
* [Grammatica Apache Phoenix](https://phoenix.apache.org/language/index.html)
