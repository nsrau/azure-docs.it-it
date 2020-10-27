---
title: Connettersi o installare Apache caeline-Azure HDInsight
description: Informazioni su come connettersi al client Apache coeline per eseguire query hive con Hadoop in HDInsight. Beeline è un'utilità per l'utilizzo di HiveServer2 rispetto a JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperfq1
ms.date: 05/27/2020
ms.openlocfilehash: be33c968499052b2b254754f37e5163012bcadd0
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547589"
---
# <a name="connect-to-apache-beeline-on-hdinsight-or-install-it-locally"></a>Connettersi ad Apache coeline in HDInsight o installarlo localmente

[Apache caeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) è un client hive incluso nei nodi head del cluster HDInsight. In questo articolo viene descritto come connettersi al client dell'oggetto con estensione HDInsight installato nel cluster con diversi tipi di connessioni. Viene inoltre illustrato come [installare il client di Oneline localmente](#install-beeline-client). 

## <a name="types-of-connections"></a>Tipi di connessioni

### <a name="from-an-ssh-session"></a>Da una sessione SSH

Quando ci si connette da una sessione SSH a un cluster nodo Head, è possibile connettersi all' `headnodehost` indirizzo sulla porta `10001` :

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

### <a name="over-an-azure-virtual-network"></a>Su una rete virtuale di Azure

Quando ci si connette da un client a HDInsight tramite una rete virtuale di Azure, è necessario fornire il nome di dominio completo (FQDN) di un nodo head del cluster. Poiché questa connessione viene eseguita direttamente ai nodi del cluster, la connessione usa la porta `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Sostituire `<headnode-FQDN>` con il nome di dominio completo di un cluster nodo head. Per trovare il nome di dominio completo di un nodo head, usare le informazioni contenute nel documento [Gestire i cluster HDInsight mediante l'API REST Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes).

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Per HDInsight Enterprise Security Package cluster (ESP) con Kerberos

Quando ci si connette da un client a un cluster Enterprise Security Package (ESP) aggiunto al Azure Active Directory (AAD)-DS in un computer nella stessa area di autenticazione del cluster, è necessario specificare anche il nome `<AAD-Domain>` di dominio e il nome di un account utente di dominio con le autorizzazioni per accedere al cluster `<username>` :

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Sostituire `<username>` con il nome di un account nel dominio che disponga delle autorizzazioni per accedere al cluster. Sostituire `<AAD-DOMAIN>` con il nome dell'istanza di Azure Active Directory (AAD) a cui viene aggiunto il cluster. Usare una stringa in maiuscolo per il valore `<AAD-DOMAIN>` per trovare le credenziali. Se necessario, selezionare `/etc/krb5.conf` per i nomi dell'area di autenticazione.

Per trovare l'URL JDBC da Ambari:

1. In un Web browser passare a `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` dove `CLUSTERNAME` è il nome del cluster. Verificare che HiveServer2 sia in esecuzione.

1. Usare gli Appunti per copiare l'URL JDBC HiveServer2.

### <a name="over-public-or-private-endpoints"></a>Su endpoint pubblici o privati

Quando ci si connette a un cluster usando gli endpoint pubblici o privati, è necessario fornire il nome dell'account di accesso al cluster (impostazione predefinita `admin` ) e la password. Ad esempio, l'uso di Beeline da un sistema client per connettersi all'indirizzo `clustername.azurehdinsight.net`. Questa connessione viene eseguita sulla porta `443` e viene crittografata tramite TLS/SSL.

Sostituire `clustername` con il nome del cluster HDInsight. Sostituire `admin` con l'account di accesso del cluster. Per i cluster ESP, usare il nome UPN completo (ad esempio, user@domain.com ). Sostituire `password` con la password dell'account di accesso del cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

o per l'endpoint privato:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Gli endpoint privati puntano a un servizio di bilanciamento del carico di base, a cui è possibile accedere solo dal reti virtuali con peering nella stessa area. Per altre informazioni, vedere [vincoli sul peering VNet globale e sui bilanciamenti del carico](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . È possibile utilizzare il `curl` comando con l' `-v` opzione per risolvere eventuali problemi di connettività con endpoint pubblici o privati prima di utilizzare l'oggetto.

### <a name="use-beeline-with-apache-spark"></a>Usare Beeline con Apache Spark

Apache Spark fornisce la propria implementazione di HiveServer2, spesso definita come server Spark Thrift. Questo servizio usa Spark SQL per risolvere le query anziché hive. E possono garantire prestazioni migliori a seconda della query.

#### <a name="through-public-or-private-endpoints"></a>Tramite endpoint pubblici o privati

La stringa di connessione utilizzata è leggermente diversa. Anziché contenerne l' `httpPath=/hive2` utilizzo `httpPath/sparkhive2` . Sostituire `clustername` con il nome del cluster HDInsight. Sostituire `admin` con l'account di accesso del cluster. Per i cluster ESP, usare il nome UPN completo (ad esempio, user@domain.com ). Sostituire `password` con la password dell'account di accesso del cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

o per l'endpoint privato:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Gli endpoint privati puntano a un servizio di bilanciamento del carico di base, a cui è possibile accedere solo dal reti virtuali con peering nella stessa area. Per altre informazioni, vedere [vincoli sul peering VNet globale e sui bilanciamenti del carico](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . È possibile utilizzare il `curl` comando con l' `-v` opzione per risolvere eventuali problemi di connettività con endpoint pubblici o privati prima di utilizzare l'oggetto.

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Dall'intestazione del cluster o all'interno della rete virtuale di Azure con Apache Spark

Quando ci si connette direttamente dal nodo head del cluster o da una risorsa all'interno della stessa istanza di Rete virtuale di Azure del cluster HDInsight, è necessario usare la porta `10002` per il server Spark Thrift invece di `10001`. Nell'esempio seguente viene illustrato come connettersi direttamente al nodo head:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

## <a name="install-beeline-client"></a>Installare il client di Oneline

Sebbene sia incluso nei nodi head, è consigliabile installarlo localmente.  I passaggi di installazione per un computer locale sono basati su un [sottosistema Windows per Linux](/windows/wsl/install-win10).

1. Aggiornare gli elenchi di pacchetti. Immettere il comando seguente nella shell bash:

    ```bash
    sudo apt-get update
    ```

1. Installare Java se non è installato. È possibile verificare con il `which java` comando.

    1. Se non è installato alcun pacchetto Java, immettere il comando seguente:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Aprire il file bashrc (spesso disponibile in ~/.bashrc): `nano ~/.bashrc` .

    1. Modificare il file bashrc. Aggiungere la riga seguente alla fine del file:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Quindi premere **CTRL + X** , quindi **Y** , quindi INVIO.

1. Scaricare Hadoop e gli archivi di Oneline, immettere i comandi seguenti:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Decomprimere gli archivi, immettere i comandi seguenti:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Modificare ulteriormente il file bashrc. È necessario identificare il percorso in cui gli archivi sono stati decompressi. Se si usa il [sottosistema Windows per Linux](/windows/wsl/install-win10)e sono stati seguiti i passaggi esatti, il percorso sarà `/mnt/c/Users/user/` , dove `user` è il nome utente.

    1. Aprire il file: `nano ~/.bashrc`

    1. Modificare i comandi seguenti con il percorso appropriato e quindi immetterli alla fine del file bashrc:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Quindi premere **CTRL + X** , quindi **Y** , quindi INVIO.

1. Chiudere e riaprire la sessione bash.

1. Testare la connessione. Usare il formato di connessione da [endpoint pubblici o privati](#over-public-or-private-endpoints), sopra.

## <a name="next-steps"></a>Passaggi successivi

* Per esempi sull'uso del client di Apache Hive, vedere [usare Apache con Apache hive](apache-hadoop-use-hive-beeline.md)
* Per informazioni generali su hive in HDInsight, vedere [usare Apache hive con Apache Hadoop in HDInsight](hdinsight-use-hive.md)