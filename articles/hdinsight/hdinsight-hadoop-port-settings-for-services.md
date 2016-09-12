<properties
pageTitle="Porte usate da HDInsight | Azure"
description="Un elenco di porte usate dai servizi Hadoop in esecuzione su HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/30/2016"
ms.author="larryfr"/>

# Porte e URI usati da HDInsight

Questo documento fornisce un elenco delle porte usate dai servizi Hadoop in esecuzione nei cluster HDInsight basati su Linux. Fornisce anche informazioni sulle porte usate per connettersi al cluster tramite SSH.

## Porte pubbliche e porte non pubbliche

I cluster HDInsight basati su Linux espongono pubblicamente solo tre porte su Internet: 22, 23 e 443. Queste porte vengono usate per accedere in modo sicuro al cluster tramite SSH e ai servizi esposti tramite il protocollo HTTPS protetto.

Internamente, HDInsight viene implementato da più macchine virtuali di Azure (i nodi all'interno del cluster) in esecuzione su una rete virtuale di Azure. Dall'interno della rete virtuale è possibile accedere alle porte non esposte a Internet. Ad esempio, se ci si connette a uno dei nodi head tramite SSH, dal nodo head è possibile accedere direttamente ai servizi in esecuzione sui nodi del cluster.

> [AZURE.IMPORTANT] Quando si crea un cluster HDInsight, se non si specifica una rete virtuale di Azure come opzione di configurazione, ne viene creata una. Tuttavia, non è possibile aggiungere altre macchine (ad esempio altre macchine virtuali Azure o computer di sviluppo client) a questa rete virtuale creata in modo automatico.

Per aggiungere altre macchine alla rete virtuale, creare innanzitutto la rete virtuale e specificarla durante la creazione del cluster HDInsight. Per altre informazioni, vedere [Estendere le funzionalità di HDInsight usando Rete virtuale di Azure](hdinsight-extend-hadoop-virtual-network.md).

## Porte pubbliche

Tutti i nodi di un cluster HDInsight si trovano all'interno di una rete virtuale Azure e non sono accessibile direttamente da Internet. Un gateway pubblico fornisce accesso a Internet per le porte seguenti, comuni a tutti i tipi di cluster HDInsight.

| Service | Port | Protocol | Descrizione |
| ---- | ---------- | -------- | ----------- | ----------- |
| sshd | 22 | SSH | Connette i client a sshd sul nodo head 0. Vedere [Uso di SSH con HDInsight basato su Linux](hdinsight-hadoop-linux-use-ssh-windows.md) |
| sshd | 22 | SSH | Connette i client a sshd sul nodo perimetrale (solo HDInsight Premium). Vedere [Introduzione all'uso di R Server su HDInsight](hdinsight-hadoop-r-server-get-started.md) |
| sshd | 23 | SSH | Connette i client a sshd sul nodo head 1. Vedere [Uso di SSH con HDInsight basato su Linux](hdinsight-hadoop-linux-use-ssh-windows.md) |
| Ambari | 443 | HTTPS | Interfaccia utente Web Ambari Vedere [Gestire i cluster HDInsight mediante l'utilizzo dell'interfaccia utente Web Ambari](hdinsight-hadoop-manage-ambari.md). |
| Ambari | 443 | HTTPS | API REST Ambari Vedere [Gestire i cluster HDInsight mediante l'API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md). |
| WebHCat | 443 | HTTPS | API REST HCatalog Vedere [Use Hive with Curl](hdinsight-hadoop-use-Pig-curl.md) (Usare Hive con Curl), [Use Pig with Curl](hdinsight-hadoop-use-Pig-curl.md) (Usare Pig con Curl) e [Use MapReduce with Curl](hdinsight-hadoop-use-mapreduce-curl.md) (Usare MapReduce con Curl). |
| HiveServer2 | 443 | ODBC | Esegue la connessione ad Hive tramite ODBC. Vedere [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver](hdinsight-connect-excel-hive-odbc-driver.md). |
| HiveServer2 | 443 | JDBC | Esegue la connessione ad Hive tramite JDBC. Vedere [Connettersi a Hive in Azure HDInsight con il driver Hive JDBC](hdinsight-connect-hive-jdbc-driver.md) |

Gli elementi seguenti sono disponibili per tipi di cluster specifici:

| Service | Port | Protocol |Tipo di cluster | Descrizione |
| ------------ | ---- |  ----------- | --- | ----------- |
| Stargate | 443 | HTTPS | HBase | API REST HBase Vedere [Introduzione all'uso di HBase](hdinsight-hbase-tutorial-get-started-linux.md) |
| Livy | 443 | HTTPS | Spark | API REST Spark Vedere [Inviare processi Spark in modalità remota mediante Livy](hdinsight-apache-spark-livy-rest-interface.md) |
| Storm | 443 | HTTPS | Storm | Interfaccia utente Web di Storm Vedere [Distribuzione e gestione di topologie Storm in HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### Autenticazione

Tutti i servizi esposti pubblicamente su Internet devono essere autenticati:

| Port | Credenziali |
| ---- | ----------- |
| 22 o 23 | Le credenziali utente SSH specificate durante la creazione del cluster |
| 443 | Il nome di accesso (impostazione predefinita: admin) e la password impostati durante la creazione del cluster |

## Porte non pubbliche

> [AZURE.NOTE] Alcuni servizi sono disponibili solo su tipi di cluster specifici. Ad esempio, HBase è disponibile solo su tipi di cluster HBase.

### Porte HDFS

| Service | Nodo/i | Port | Protocol | Descrizione |
| ------- | ------- | ---- | -------- | ----------- | 
| Interfaccia utente Web NameNode | Nodi head | 30070 | HTTPS | Interfaccia utente Web per visualizzare lo stato corrente |
| Servizio metadati NameNode | nodi head | 8020 | IPC | Metadati del file system 
| DataNode | Tutti i nodi di lavoro | 30075 | HTTPS | Interfaccia utente Web per visualizzare lo stato, i log e così via. |
| DataNode | Tutti i nodi di lavoro | 30010 | &nbsp; | Trasferimento dati |
| DataNode | Tutti i nodi di lavoro | 30020 | IPC | Operazioni sui metadati |
| NameNode secondario | Nodi head | 50090 | HTTP | Checkpoint per i metadati NameNode |

### Porte YARN

| Service | Nodo/i | Port | Protocol | Descrizione |
| ------- | ------- | ---- | -------- | ----------- |
| Interfaccia utente Web di Resource Manager | Nodi head | 8088 | HTTP | Interfaccia utente Web per Resource Manager |
| Interfaccia utente Web di Resource Manager | Nodi head | 8090 | HTTPS | Interfaccia utente Web per Resource Manager |
| Interfaccia di amministrazione di Resource Manager | nodi head | 8141 | IPC | Per gli invii delle applicazioni (Hive, server Hive, Pig e così via) |
| Utilità di pianificazione di Resource Manager | nodi head | 8030 | HTTP | Interfaccia di amministrazione |
| Interfaccia dell'applicazione Resource Manager | nodi head | 8050 | HTTP |Indirizzo dell'interfaccia di gestione delle applicazioni |
| NodeManager | Tutti i nodi di lavoro | 30050 | &nbsp; | L'indirizzo del gestore di contenitore |
| Interfaccia utente Web di NodeManager | Tutti i nodi di lavoro | 30060 | HTTP | Interfaccia di Resource Manager |
| Indirizzo di Timeline | Nodi head | 10200 | RPC | Il servizio RPC del servizio Timeline. |
| Interfaccia utente Web di Timeline | Nodi head | 8181 | HTTP | L'interfaccia utente Web del servizio Timeline |

### Porte Hive

| Service | Nodo/i | Port | Protocol | Descrizione |
| ------- | ------- | ---- | -------- | ----------- |
| HiveServer2 | Nodi head | 10001 | Thrift | Servizio per la connessione a livello di programmazione ad Hive (Thrift/JDBC) |
| HiveServer | Nodi head | 10000 | Thrift | Servizio per la connessione a livello di programmazione ad Hive (Thrift/JDBC) |
| Metastore Hive | Nodi head | 9083 | Thrift | Servizio per la connessione a livello di programmazione ai metadati Hive (Thrift/JDBC) |

### Porte WebHCat

| Service | Nodo/i | Port | Protocol | Descrizione |
| ------- | ------- | ---- | -------- | ----------- |
| Server WebHCat | Nodi head | 30111 | HTTP | API Web su HCatalog e su altri servizi Hadoop |

### Porte MapReduce

| Service | Nodo/i | Port | Protocol | Descrizione |
| ------- | ------- | ---- | -------- | ----------- |
| JobHistory | Nodi head | 19888 | HTTP | Interfaccia utente Web di MapReduce JobHistory |
| JobHistory | Nodi head | 10020 | &nbsp; | Server di MapReduce JobHistory |
| ShuffleHandler | &nbsp; | 13562 | &nbsp; | Trasferisce output intermedi di Map ai reducer che eseguono la richiesta |

### Oozie

| Service | Nodo/i | Port | Protocol | Descrizione |
| ------- | ------- | ---- | -------- | ----------- |
| Server di Oozie | Nodi head | 11000 | HTTP | URL per il servizio Oozie |
| Server di Oozie | Nodi head | 11001 | HTTP | Porta per l'amministrazione di Oozie |

### Metriche di Ambari

| Service | Nodo/i | Port | Protocol | Descrizione |
| ------- | ------- | ---- | -------- | ----------- |
| TimeLine (cronologia delle applicazioni) | Nodi head | 6188 | HTTP | L'interfaccia utente Web del servizio TimeLine |
| TimeLine (cronologia delle applicazioni) | Nodi head | 30200 | RPC | L'interfaccia utente Web del servizio TimeLine |

### Porte HBase

| Service | Nodo/i | Port | Protocol | Descrizione |
| ------- | ------- | ---- | -------- | ----------- |
| HMaster | Nodi head | 16000 | &nbsp; | &nbsp; |
| Interfaccia utente Web informativa di HMaster | Nodi head | 16010 | HTTP | La porta per l'interfaccia utente Web Master HBase |
| Server dell'area | Tutti i nodi di lavoro | 16020 | &nbsp; | &nbsp; |
| &nbsp; | &nbsp; | 2181 | &nbsp; | La porta usata dai client per connettersi a ZooKeeper |

### Porte Kafka

| Service | Nodo/i | Port | Protocol | Descrizione |
| ------- | ------- | ---- | -------- | ----------- |
| Gestore | Nodi di lavoro | 9092 | [Protocollo di trasmissione Kafka](http://kafka.apache.org/protocol.html) | Usato per la comunicazione di client |
| &nbsp; | Nodi Zookeeper | 2181 | &nbsp; | La porta usata dai client per connettersi a ZooKeeper |

<!---HONumber=AcomDC_0831_2016-->