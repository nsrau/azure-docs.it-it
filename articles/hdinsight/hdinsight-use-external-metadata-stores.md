---
title: Usare gli archivi di metadati esterni - Azure HDInsight
description: Usare gli archivi di metadati esterni con i cluster di Azure HDInsight.Use external metadata stores with Azure HDInsight clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/03/2020
ms.openlocfilehash: 0cadf3930008868fe223e6e1024a2d14d17d8131
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657113"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Usare gli archivi di metadati esterni in Azure HDInsight

HDInsight consente di assumere il controllo dei dati e dei metadati con archivi dati esterni. Questa funzione è disponibile per il [metastore Apache Hive](#custom-metastore), [Il metastore Apache Oozie](#apache-oozie-metastore)e il [database Apache Ambari](#custom-ambari-db).

Il metastore di Apache Hive in HDInsight è una parte essenziale dell'architettura di Apache Hadoop. Un metastore è il repository centrale dello schema. Il metastore viene utilizzato da altri strumenti di accesso ai Big Data come Apache Spark, Interactive Query (LLAP), Presto o Apache Pig. HDInsight usa un Database SQL di Azure come il metastore Hive.

![Architettura dell'archivio dei metadati Hive di HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Esistono due modi per configurare un metastore per i cluster HDInsight:

* [Metastore predefinito](#default-metastore)
* [Metastore personalizzato](#custom-metastore)

## <a name="default-metastore"></a>Metastore predefinito

Per impostazione predefinita, HDInsight crea un metastore con ogni tipo di cluster. È invece possibile specificare un metastore personalizzato. Il metastore predefinito include quanto segue:

* Non sono previsti costi aggiuntivi. HDInsight crea un metastore con ogni tipo di cluster senza alcun costo aggiuntivo per l'utente.

* Ogni metastore predefinito fa parte del ciclo di vita del cluster. Quando si elimina un cluster verranno eliminati anche il metastore e i metadati corrispondenti.

* Non è possibile condividere il metastore predefinito con altri cluster.

* Il metastore predefinito usa il database SQL di Azure di base, che ha un limite di 5 DTU (unità di trasmissione database).
Questo metastore predefinito viene in genere utilizzato per carichi di lavoro relativamente semplici. Carichi di lavoro che non richiedono più cluster e non richiedono metadati conservati oltre il ciclo di vita del cluster.

## <a name="custom-metastore"></a>Metastore personalizzato

HDInsight supporta inoltre i metastore personalizzati, che sono consigliati per i cluster di produzione:

* Specificare il database SQL di Azure come metastore.

* Il ciclo di vita del metastore non è legato a un ciclo di vita dei cluster, pertanto è possibile creare ed eliminare cluster senza perdere i metadati. I metadati come ad esempio gli schemi di Hive verranno mantenuti anche dopo aver eliminato e ricreato il cluster di HDInsight.

* Un metastore personalizzato consente di collegare più cluster e tipi di cluster al metastore. Ad esempio, un singolo metastore può essere condiviso tra i cluster Interactive Query, Hive e Spark in HDInsight.

* Si paga il costo di un metastore (database SQL di Azure) in base al livello di prestazioni scelto.

* È possibile aumentare il metastore in base alle esigenze.

* Il cluster e il metastore esterno devono essere ospitati nella stessa area.

![Caso d'uso dell'archivio dei metadati Hive di HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Creare e configare il database SQL di Azure per il metastore personalizzatoCreate and config Azure SQL Database for the custom metastore

Creare o avere un database SQL di Azure esistente prima di configurare un metastore Hive personalizzato per un cluster HDInsight.Create or have an existing Azure SQL Database before setting up a custom Hive metastore for a HDInsight cluster.  Per altre informazioni, vedere Guida introduttiva: Creare un singolo database nel database SQL di Azure.For more information, see [Quickstart: Create a single database in Azure SQL DB.](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal)

Configurare le regole del firewall del database SQL di Azure per consentire ai servizi e alle risorse di Azure di accedere al server. Abilitare questa opzione nel portale di Azure selezionando **Imposta firewall server**. Selezionare quindi ON sotto **Consenti ai servizi e alle risorse** di Azure di accedere a questo server per il database o il database del database SQL di Azure.Then select **ON** underneath Allow Azure services and resources to access this server for the Azure SQL Database server or database. Per altre informazioni, vedere [Creare e gestire le regole del firewall IPFor more information,](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) see Create and manage IP firewall rules

![pulsante Imposta firewall server](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![consentire l'accesso ai servizi azure](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Selezionare un metastore personalizzato durante la creazione del cluster

È possibile puntare il cluster a un database SQL di Azure creato in precedenza in qualsiasi momento. Per la creazione di cluster tramite il portale, l'opzione è specificata dalle **impostazioni di Storage > Metastore**.

![Portale dell'archivio dei metadati Hive di HDInsight](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-guidelines"></a>Linee guida per i metastore Hive

* Usare un metastore personalizzato quando possibile per separare le risorse di calcolo (il cluster in esecuzione) e i metadati (archiviati nel metastore).

* Iniziare con un livello S2, che fornisce 50 DTU e 250 GB di spazio di archiviazione. Se viene visualizzato un collo di bottiglia, è possibile aumentare il database.

* Se si prevede l'accesso di più cluster HDInsight a dati separati, usare un database separato per il metastore in ogni cluster. Se un metastore è condiviso da più cluster HDInsight, significa che i cluster usano gli stessi metadati e file di dati utente sottostanti.

* Eseguire periodicamente il backup del metastore personalizzato. Il database SQL di Azure genera automaticamente i backup, ma il periodo di conservazione dei backup varia. Per altre informazioni, vedere [Informazioni sui backup automatici del database SQL](../sql-database/sql-database-automated-backups.md).

* Individuare il cluster metastore e HDInsight nella stessa area. Questa configurazione fornirà le prestazioni più elevate e i costi di uscita della rete più bassi.

* Monitorare il metastore per le prestazioni e la disponibilità usando gli strumenti di monitoraggio del database SQL di Azure o i log di monitoraggio di Azure.Monitor your metastore for performance and availability using Azure SQL Database Monitoring tools, or Azure Monitor logs.

* Quando viene creata una nuova versione superiore di Azure HDInsight in un database di metastore personalizzato esistente, il sistema aggiorna lo schema del metastore. L'aggiornamento è irreversibile senza ripristinare il database dal backup.

* Se un metastore è condiviso da più cluster, assicurarsi che tutti i cluster abbiano la stessa versione HDInsight. Versioni Hive diverse usano schemi di database del metastore diversi. Ad esempio, non è possibile condividere un metastore tra i cluster con controllo delle versioni Hive 2.1 e Hive 3.1.For example, you can't share a metastore across Hive 2.1 and Hive 3.1 controllod clusters.

* In HDInsight 4.0, Spark e Hive usano cataloghi indipendenti per accedere alle tabelle SparkSQL o Hive.In HDInsight 4.0, Spark and Hive use independent catalogs for accessing SparkSQL or Hive tables. Una tabella creata da Spark si trova nel catalogo Spark. Una tabella creata da Hive vive nel catalogo Hive. Questo comportamento è diverso da HDInsight 3.6 in cui Hive e Spark hanno condiviso il catalogo comune. Hive and Spark Integration in HDInsight 4.0 si basa su Hive Warehouse Connector (HWC). HWC funge da ponte tra Spark e Hive. [Ulteriori informazioni su Hive Warehouse Connector](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

## <a name="apache-oozie-metastore"></a>Metastore Apache Oozie

Apache Oozie è un sistema di coordinamento dei flussi di lavoro che consente di gestire i processi Hadoop. Oozie supporta i processi Hadoop per Apache MapReduce, Pig, Hive e altri.  Oozie utilizza un metastore per archiviare i dettagli sui flussi di lavoro. Per ottenere un miglioramento delle prestazioni quando si usa Oozie, è possibile usare il database SQL di Azure come metastore personalizzato. Il metastore fornisce l'accesso ai dati del processo di Oozie dopo l'eliminazione del cluster.

Per istruzioni sulla creazione di un metastore Oozie con il database SQL di Azure, vedere come [usare Apache Oozie per i flussi di lavoro](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>Database Ambari personalizzato

Per utilizzare il proprio database esterno con Apache Ambari su HDInsight, vedere [Database Apache Ambari personalizzato](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Passaggi successivi

* [Configurare i cluster di HDInsight con Apache Hadoop, Apache Spark, Apache Kafka e altro ancora](./hdinsight-hadoop-provision-linux-clusters.md)
