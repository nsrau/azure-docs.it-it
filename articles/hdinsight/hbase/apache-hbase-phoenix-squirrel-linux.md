---
title: 'Guida introduttiva: Eseguire query Apache HBase in Azure HDInsight - Apache Phoenix'
description: In questo avvio rapido si impara a usare Apache Phoenix in HDInsight. Vengono fornite anche informazioni su come installare e configurare SQLLine nel computer in uso per connettersi a un cluster HBase in HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 20af6d32d03ae5d4fe37b1a37198ef1f2c50ec95
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137411"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>Guida introduttiva: Eseguire query Apache HBase in Azure HDInsight con Apache Phoenix

In questo argomento di avvio rapido si apprenderà come usare Apache Phoenix per eseguire query HBase in Azure HDInsight. Apache Phoenix è un motore di query SQL per Apache HBase. Vi si accede come un'unità JDBC e permette di eseguire query e di gestire le tabelle HBase tramite SQL. [SQLLine](http://sqlline.sourceforge.net/) è un'utilità della riga di comando per eseguire SQL.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache HBase. Per creare un cluster HDInsight, vedere [Creare un cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).  Assicurarsi di scegliere il tipo di cluster **HBase**.

* Un client SSH. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="identify-a-zookeeper-node"></a>Identificare un nodo ZooKeeper

Quando ci si connette a un cluster HBase, è necessario connettersi a uno dei nodi Apache ZooKeeper. Ogni cluster HDInsight ha tre nodi ZooKeeper. È possibile usare curl per identificare rapidamente un nodo ZooKeeper. Modificare il comando curl seguente sostituendo `PASSWORD` e `CLUSTERNAME` con i valori appropriati e quindi digitare il comando al prompt dei comandi:

```cmd
curl -u admin:PASSWORD -sS -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER
```

Una parte dell'output sarà simile a quanto segue:

```output
    {
      "href" : "http://hn1-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net:8080/api/v1/clusters/myCluster/hosts/zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net/host_components/ZOOKEEPER_SERVER",
      "HostRoles" : {
        "cluster_name" : "myCluster",
        "component_name" : "ZOOKEEPER_SERVER",
        "host_name" : "zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net"
      }
```

Prendere nota del valore di `host_name` per usarlo in seguito.

## <a name="create-a-table-and-manipulate-data"></a>Creare una tabella e manipolare i dati

È possibile usare SSH per connettersi ai cluster HBase e quindi usare Apache Phoenix per creare tabelle HBase, inserire dati ed eseguire query sui dati.

1. Usare il comando `ssh` per connettersi al cluster HBase. Modificare il comando seguente sostituendo `CLUSTERNAME` con il nome del cluster in uso e quindi immettere il comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Passare alla directory del client Phoenix. Immettere il comando seguente:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. Avviare [SQLLine](http://sqlline.sourceforge.net/). Modificare il comando seguente sostituendo `ZOOKEEPER` con il nodo ZooKeeper identificato in precedenza e quindi immettere il comando:

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. Creare una tabella HBase. Immettere il comando seguente:

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. Usare il comando SQLLine `!tables` per elencare tutte le tabelle in HBase. Immettere il comando seguente:

    ```sqlline
    !tables
    ```

6. Inserire i valori nella tabella. Immettere il comando seguente:

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. Eseguire una query sulla tabella. Immettere il comando seguente:

    ```sql
    SELECT * FROM Company;
    ```

8. Eliminare un record. Immettere il comando seguente:

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. Eliminare la tabella. Immettere il comando seguente:

    ```hbase
    DROP TABLE Company;
    ```

10. Usare il comando SQLLine `!quit` per uscire da SQLLine. Immettere il comando seguente:

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'argomento di avvio rapido, può essere opportuno eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non viene usato. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati.

Per eliminare un cluster, vedere [Eliminare un cluster HDInsight tramite browser, PowerShell o l'interfaccia della riga di comando di Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come usare Apache Phoenix per eseguire query HBase in Azure HDInsight. Per altre informazioni su Apache Phoenix, leggere l'articolo seguente che contiene una descrizione più approfondita.

> [!div class="nextstepaction"]
> [Apache Phoenix in HDInsight](../hdinsight-phoenix-in-hdinsight.md)
