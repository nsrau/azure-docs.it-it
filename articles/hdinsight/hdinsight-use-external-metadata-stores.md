---
title: Usare gli archivi di metadati esterni - Azure HDInsight
description: Use external metadata stores with Azure HDInsight clusters, and best practices.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 14b36a391778649e96694f1cb1d3a1b4e7ee89ba
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327359"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Usare gli archivi di metadati esterni in Azure HDInsight

HDInsight allows you to take control of your data and metadata by deploying key metadata solutions and management databases to external data stores. This feature is currently available for [Apache Hive metastore](#custom-metastore), [Apache Oozie metastore](#apache-oozie-metastore) and [Apache Ambari database](#custom-ambari-db).

Il metastore di Apache Hive in HDInsight è una parte essenziale dell'architettura di Apache Hadoop. Un metastore è il repository centrale di schemi che può essere usato da altri strumenti di accesso ai Big Data come Apache Spark, Interactive Query (LLAP), Presto o Apache Pig. HDInsight usa un Database SQL di Azure come il metastore Hive.

![Architettura dell'archivio dei metadati Hive di HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Esistono due modi per configurare un metastore per i cluster HDInsight:

* [Metastore predefinito](#default-metastore)
* [Metastore personalizzato](#custom-metastore)

## <a name="default-metastore"></a>Metastore predefinito

Per impostazione predefinita, HDInsight crea un metastore con ogni tipo di cluster. È invece possibile specificare un metastore personalizzato. Il metastore predefinito include quanto segue:

* Non sono previsti costi aggiuntivi. HDInsight crea un metastore con ogni tipo di cluster senza alcun costo aggiuntivo per l'utente.

* Ogni metastore predefinito fa parte del ciclo di vita del cluster. Quando si elimina un cluster verranno eliminati anche il metastore e i metadati corrispondenti.

* You can't share the default metastore with other clusters.

* Il metastore predefinito usa il database SQL di Azure di base, che ha un limite di 5 DTU (unità di trasmissione database).
Questo metastore predefinito viene usato in genere per carichi di lavoro relativamente semplici che non richiedono più cluster e che non hanno bisogno di conservare i metadati oltre il ciclo di vita del cluster.

## <a name="custom-metastore"></a>Metastore personalizzato

HDInsight supporta inoltre i metastore personalizzati, che sono consigliati per i cluster di produzione:

* Specificare il database SQL di Azure come metastore.

* The lifecycle of the metastore isn't tied to a clusters lifecycle, so you can create and delete clusters without losing metadata. I metadati come ad esempio gli schemi di Hive verranno mantenuti anche dopo aver eliminato e ricreato il cluster di HDInsight.

* Un metastore personalizzato consente di collegare più cluster e tipi di cluster al metastore. Ad esempio, un singolo metastore può essere condiviso tra i cluster Interactive Query, Hive e Spark in HDInsight.

* Si paga il costo di un metastore (database SQL di Azure) in base al livello di prestazioni scelto.

* È possibile aumentare il metastore in base alle esigenze.

![Caso d'uso dell'archivio dei metadati Hive di HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Create and config Azure SQL Database for the custom metastore

You need to create or have an existing Azure SQL Database before setting up a custom Hive metastore for a HDInsight cluster.  For more information, see [Quickstart: Create a single database in Azure SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal).

To make sure that your HDInsight cluster can access the connected Azure SQL Database, configure Azure SQL Database firewall rules to allow Azure services and resources to access the server.

You can enable this option in the Azure portal by clicking **Set server firewall**, and clicking **ON** underneath **Allow Azure services and resources to access this server** for the Azure SQL Database server or database. For more information, see [Create and manage IP firewall rules](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

![set server firewall button](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![allow azure services access](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Selezionare un metastore personalizzato durante la creazione del cluster

È possibile indirizzare il cluster a un database SQL di Azure creato in precedenza durante la creazione del cluster, oppure è possibile configurare il database di SQL dopo la creazione del cluster. This option is specified with the **Storage > Metastore settings** while creating a new Hadoop, Spark, or interactive Hive cluster from Azure portal.

![Portale dell'archivio dei metadati Hive di HDInsight](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-best-practices"></a>Procedure consigliate metastore Hive

Ecco alcune procedure consigliate generali per il metastore Hive di HDInsight:

* Usare un metastore personalizzato quando possibile per separare le risorse di calcolo (il cluster in esecuzione) e i metadati (archiviati nel metastore).

* Iniziare con un livello S2, che fornisce 50 DTU e 250 GB di spazio di archiviazione. Se viene visualizzato un collo di bottiglia, è possibile aumentare il database.

* Se si prevede l'accesso di più cluster HDInsight a dati separati, usare un database separato per il metastore in ogni cluster. Se un metastore è condiviso da più cluster HDInsight, significa che i cluster usano gli stessi metadati e file di dati utente sottostanti.

* Eseguire periodicamente il backup del metastore personalizzato. Il database SQL di Azure genera automaticamente i backup, ma il periodo di conservazione dei backup varia. Per altre informazioni, vedere [Informazioni sui backup automatici del database SQL](../sql-database/sql-database-automated-backups.md).

* Posizionare il metastore e il cluster HDInsight nella stessa area per ottenere le migliori prestazioni e ridurre i costi dei dati in uscita della rete.

* Monitor your metastore for performance and availability using Azure SQL Database Monitoring tools, such as the Azure portal or Azure Monitor logs.

* Quando una nuova versione di Azure HDInsight viene creata in un database del metastore personalizzato esistente, il sistema aggiorna lo schema del metastore che è irreversibile senza ripristino del database dal backup.

* Se un metastore è condiviso da più cluster, assicurarsi che tutti i cluster abbiano la stessa versione HDInsight. Versioni Hive diverse usano schemi di database del metastore diversi. For example, you can't share a metastore across Hive 2.1 and Hive 3.1 versioned clusters.

* In HDInsight 4.0, Spark and Hive use independent catalogs for accessing SparkSQL or Hive tables. A table created by Spark resides in the Spark catalog. A table created by Hive resides in the Hive catalog. This is different than HDInsight 3.6 where Hive and Spark shared common catalog. Hive and Spark Integration in HDInsight 4.0 relies on Hive Warehouse Connector (HWC). HWC works as a bridge between Spark and Hive. [Learn about Hive Warehouse Connector](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

## <a name="apache-oozie-metastore"></a>Apache Oozie metastore

Apache Oozie è un sistema di coordinamento dei flussi di lavoro che consente di gestire i processi Hadoop.  Oozie supporta i processi Hadoop per Apache MapReduce, Pig, Hive e altri.  Oozie usa un metastore per archiviare i dettagli sui flussi di lavoro correnti e completati. Per ottenere un miglioramento delle prestazioni quando si usa Oozie, è possibile usare il database SQL di Azure come metastore personalizzato. Il metastore può anche fornire l'accesso ai dati di processo Oozie dopo l'eliminazione del cluster.

Per istruzioni sulla creazione di un metastore Oozie con il database SQL di Azure, vedere come [usare Apache Oozie per i flussi di lavoro](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>Database Ambari personalizzato

To use your own external database with Apache Ambari on HDInsight, see [Custom Apache Ambari database](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Passaggi successivi

* [Configurare i cluster di HDInsight con Apache Hadoop, Apache Spark, Apache Kafka e altro ancora](./hdinsight-hadoop-provision-linux-clusters.md)
