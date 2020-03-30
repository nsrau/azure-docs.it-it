---
title: Usare gli archivi di metadati esterni - Azure HDInsight
description: Usare gli archivi di metadati esterni con i cluster di Azure HDInsight e le procedure consigliate.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: edb2d256d3e5d98c52dbdff1162e0e030ebe2be3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272162"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Usare gli archivi di metadati esterni in Azure HDInsight

HDInsight consente di assumere il controllo dei dati e dei metadati distribuendo soluzioni di metadati chiave e database di gestione in archivi dati esterni. Questa funzione è attualmente disponibile per [il metastore Apache Hive](#custom-metastore), [Apache Oozie metastore](#apache-oozie-metastore) e [Database Apache Ambari](#custom-ambari-db).

Il metastore di Apache Hive in HDInsight è una parte essenziale dell'architettura di Apache Hadoop. Un metastore è il repository centrale di schemi che può essere usato da altri strumenti di accesso ai Big Data come Apache Spark, Interactive Query (LLAP), Presto o Apache Pig. HDInsight usa un Database SQL di Azure come il metastore Hive.

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
Questo metastore predefinito viene usato in genere per carichi di lavoro relativamente semplici che non richiedono più cluster e che non hanno bisogno di conservare i metadati oltre il ciclo di vita del cluster.

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

È necessario creare o disporre di un database SQL di Azure esistente prima di configurare un metastore Hive personalizzato per un cluster HDInsight.You need to create or have an existing Azure SQL Database before setting up a custom Hive metastore for a HDInsight cluster.  Per altre informazioni, vedere Guida introduttiva: Creare un singolo database nel database SQL di Azure.For more information, see [Quickstart: Create a single database in Azure SQL DB.](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal)

Per assicurarsi che il cluster HDInsight possa accedere al database SQL di Azure connesso, configurare le regole del firewall del database SQL di Azure per consentire ai servizi e alle risorse di Azure di accedere al server.

È possibile abilitare questa opzione nel portale di Azure facendo clic su Imposta firewall server e facendo clic su ON **Consenti ai servizi e** alle risorse di Azure di accedere a questo server per il server o il database SQL di Azure.You can enable this option in the Azure portal by clicking Set server **firewall**, and clicking **ON** on Allow Azure services and resources to access this server for the Azure SQL Database server or database. Per altre informazioni, vedere [Creare e gestire le regole del firewall IPFor more information,](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) see Create and manage IP firewall rules

![pulsante Imposta firewall server](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![consentire l'accesso ai servizi azure](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Selezionare un metastore personalizzato durante la creazione del cluster

È possibile indirizzare il cluster a un database SQL di Azure creato in precedenza durante la creazione del cluster, oppure è possibile configurare il database di SQL dopo la creazione del cluster. Questa opzione viene specificata con le **impostazioni di Archiviazione > Metastore** durante la creazione di un nuovo cluster Hadoop, Spark o Hive interattivo dal portale di Azure.This option is specified with the Storage > Metastore settings while creating a new Hadoop, Spark, or interactive Hive cluster from Azure portal.

![Portale dell'archivio dei metadati Hive di HDInsight](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-best-practices"></a>Procedure consigliate metastore Hive

Ecco alcune procedure consigliate generali per il metastore Hive di HDInsight:

* Usare un metastore personalizzato quando possibile per separare le risorse di calcolo (il cluster in esecuzione) e i metadati (archiviati nel metastore).

* Iniziare con un livello S2, che fornisce 50 DTU e 250 GB di spazio di archiviazione. Se viene visualizzato un collo di bottiglia, è possibile aumentare il database.

* Se si prevede l'accesso di più cluster HDInsight a dati separati, usare un database separato per il metastore in ogni cluster. Se un metastore è condiviso da più cluster HDInsight, significa che i cluster usano gli stessi metadati e file di dati utente sottostanti.

* Eseguire periodicamente il backup del metastore personalizzato. Il database SQL di Azure genera automaticamente i backup, ma il periodo di conservazione dei backup varia. Per altre informazioni, vedere [Informazioni sui backup automatici del database SQL](../sql-database/sql-database-automated-backups.md).

* Posizionare il metastore e il cluster HDInsight nella stessa area per ottenere le migliori prestazioni e ridurre i costi dei dati in uscita della rete.

* Monitorare il metastore per le prestazioni e la disponibilità usando gli strumenti di monitoraggio del database SQL di Azure, ad esempio il portale di Azure o i log di Monitoraggio di Azure.Monitor your metastore for performance and availability using Azure SQL Database Monitoring tools, such as the Azure portal or Azure Monitor logs.

* Quando una nuova versione di Azure HDInsight viene creata in un database del metastore personalizzato esistente, il sistema aggiorna lo schema del metastore che è irreversibile senza ripristino del database dal backup.

* Se un metastore è condiviso da più cluster, assicurarsi che tutti i cluster abbiano la stessa versione HDInsight. Versioni Hive diverse usano schemi di database del metastore diversi. Ad esempio, non è possibile condividere un metastore tra i cluster con controllo delle versioni Hive 2.1 e Hive 3.1.For example, you can't share a metastore across Hive 2.1 and Hive 3.1 controllod clusters.

* In HDInsight 4.0, Spark e Hive usano cataloghi indipendenti per accedere alle tabelle SparkSQL o Hive.In HDInsight 4.0, Spark and Hive use independent catalogs for accessing SparkSQL or Hive tables. Una tabella creata da Spark si trova nel catalogo Spark. Una tabella creata da Hive si trova nel catalogo Hive. Questo è diverso da HDInsight 3.6 in cui Hive e Spark hanno condiviso il catalogo comune. Hive and Spark Integration in HDInsight 4.0 si basa su Hive Warehouse Connector (HWC). HWC funge da ponte tra Spark e Hive. [Ulteriori informazioni su Hive Warehouse Connector](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

## <a name="apache-oozie-metastore"></a>Metastore Apache Oozie

Apache Oozie è un sistema di coordinamento dei flussi di lavoro che consente di gestire i processi Hadoop.  Oozie supporta i processi Hadoop per Apache MapReduce, Pig, Hive e altri.  Oozie usa un metastore per archiviare i dettagli sui flussi di lavoro correnti e completati. Per ottenere un miglioramento delle prestazioni quando si usa Oozie, è possibile usare il database SQL di Azure come metastore personalizzato. Il metastore può anche fornire l'accesso ai dati di processo Oozie dopo l'eliminazione del cluster.

Per istruzioni sulla creazione di un metastore Oozie con il database SQL di Azure, vedere come [usare Apache Oozie per i flussi di lavoro](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>Database Ambari personalizzato

Per utilizzare il proprio database esterno con Apache Ambari su HDInsight, vedere [Database Apache Ambari personalizzato](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Passaggi successivi

* [Configurare i cluster di HDInsight con Apache Hadoop, Apache Spark, Apache Kafka e altro ancora](./hdinsight-hadoop-provision-linux-clusters.md)
