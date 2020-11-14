---
title: Usare gli archivi di metadati esterni - Azure HDInsight
description: Usare archivi di metadati esterni con i cluster HDInsight di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 08/06/2020
ms.openlocfilehash: bcfd14572b632cdc455babf7b9f8d67be904406c
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629955"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Usare gli archivi di metadati esterni in Azure HDInsight

HDInsight consente di assumere il controllo dei dati e dei metadati con archivi dati esterni. Questa funzionalità è disponibile per [Apache hive](#custom-metastore)Metastore, il [Metastore Apache oozie](#apache-oozie-metastore)e il [database Apache Ambari](#custom-ambari-db).

Il metastore di Apache Hive in HDInsight è una parte essenziale dell'architettura di Apache Hadoop. Un Metastore è il repository centrale dello schema. Il Metastore viene usato da altri strumenti di accesso Big Data, ad esempio Apache Spark, Interactive query (LLAP), presto o Apache Pig. HDInsight usa un Database SQL di Azure come il metastore Hive.

![Architettura dell'archivio dei metadati Hive di HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Esistono due modi per configurare un metastore per i cluster HDInsight:

* [Metastore predefinito](#default-metastore)
* [Metastore personalizzato](#custom-metastore)

## <a name="default-metastore"></a>Metastore predefinito

Per impostazione predefinita, HDInsight crea un metastore con ogni tipo di cluster. È invece possibile specificare un metastore personalizzato. Il metastore predefinito include quanto segue:

* Non sono previsti costi aggiuntivi. HDInsight crea un metastore con ogni tipo di cluster senza alcun costo aggiuntivo per l'utente.

* Ogni metastore predefinito fa parte del ciclo di vita del cluster. Quando si elimina un cluster verranno eliminati anche il metastore e i metadati corrispondenti.

* Non è possibile condividere il Metastore predefinito con altri cluster.

* Il Metastore predefinito è consigliato solo per i carichi di lavoro semplici. Carichi di lavoro che non richiedono più cluster e che non necessitano di metadati conservati oltre il ciclo di vita del cluster.

> [!IMPORTANT]
> Il Metastore predefinito fornisce un database SQL di Azure con un **limite di DTU di livello Basic 5 (non aggiornabile)**. Adatto a scopo di test di base. Per carichi di lavoro di grandi dimensioni o di produzione, è consigliabile eseguire la migrazione a un Metastore esterno.

## <a name="custom-metastore"></a>Metastore personalizzato

HDInsight supporta inoltre i metastore personalizzati, che sono consigliati per i cluster di produzione:

* Specificare il database SQL di Azure come metastore.

* Il ciclo di vita del Metastore non è associato a un ciclo di vita dei cluster, pertanto è possibile creare ed eliminare i cluster senza perdere i metadati. I metadati come ad esempio gli schemi di Hive verranno mantenuti anche dopo aver eliminato e ricreato il cluster di HDInsight.

* Un metastore personalizzato consente di collegare più cluster e tipi di cluster al metastore. Ad esempio, un singolo metastore può essere condiviso tra i cluster Interactive Query, Hive e Spark in HDInsight.

* Si paga per il costo di un Metastore (database SQL di Azure) in base al livello di prestazioni scelto.

* È possibile aumentare il metastore in base alle esigenze.

* Il cluster e il Metastore esterno devono essere ospitati nella stessa area.

![Caso d'uso dell'archivio dei metadati Hive di HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Creare e configurare il database SQL di Azure per il Metastore personalizzato

Creare o disporre di un database SQL di Azure esistente prima di configurare un metastore Hive personalizzato per un cluster HDInsight.  Per altre informazioni, vedere [Guida introduttiva: creare un database singolo nel database SQL di Azure](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal).

Durante la creazione del cluster, il servizio HDInsight deve connettersi al Metastore esterno e verificare le credenziali. Configurare le regole del firewall del database SQL di Azure per consentire ai servizi e alle risorse di Azure di accedere al server. Abilitare questa opzione nel portale di Azure selezionando **imposta firewall server**. Selezionare quindi **No** sotto **Nega accesso alla rete pubblica** e **Sì** sottostante **Consenti ai servizi e alle risorse di Azure di accedere al server per il** database SQL di Azure. Per altre informazioni, vedere [creare e gestire regole del firewall IP](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

Gli endpoint privati per gli archivi SQL sono supportati solo nei cluster creati con `outbound` ResourceProviderConnection. Per ulteriori informazioni, vedere la [documentazione](https://docs.microsoft.com/azure/hdinsight/hdinsight-private-link)a.

![pulsante Imposta firewall del server](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![Consenti accesso ai servizi di Azure](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Selezionare un metastore personalizzato durante la creazione del cluster

È possibile puntare il cluster a un database SQL di Azure creato in precedenza in qualsiasi momento. Per la creazione di cluster tramite il portale, l'opzione viene specificata dalle **impostazioni di archiviazione > Metastore**.

![Portale dell'archivio dei metadati Hive di HDInsight](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-guidelines"></a>Linee guida per metastore Hive

> [!NOTE]
> Usare un metastore personalizzato quando possibile per separare le risorse di calcolo (il cluster in esecuzione) e i metadati (archiviati nel metastore). Iniziare con il livello S2, che fornisce 50 DTU e 250 GB di spazio di archiviazione. Se viene visualizzato un collo di bottiglia, è possibile aumentare il database.

* Se si prevede l'accesso di più cluster HDInsight a dati separati, usare un database separato per il metastore in ogni cluster. Se un metastore è condiviso da più cluster HDInsight, significa che i cluster usano gli stessi metadati e file di dati utente sottostanti.

* Eseguire periodicamente il backup del metastore personalizzato. Il database SQL di Azure genera automaticamente i backup, ma il periodo di conservazione dei backup varia. Per altre informazioni, vedere [Informazioni sui backup automatici del database SQL](../azure-sql/database/automated-backups-overview.md).

* Individuare il Metastore e il cluster HDInsight nella stessa area. Questa configurazione fornirà le massime prestazioni e gli addebiti di uscita di rete più bassi.

* Monitora il Metastore per le prestazioni e la disponibilità usando gli strumenti di monitoraggio del database SQL di Azure o i log di monitoraggio di Azure.

* Quando viene creata una nuova versione successiva di Azure HDInsight per un database di Metastore personalizzato esistente, il sistema aggiorna lo schema del Metastore. L'aggiornamento è irreversibile senza ripristinare il database dal backup.

* Se un metastore è condiviso da più cluster, assicurarsi che tutti i cluster abbiano la stessa versione HDInsight. Versioni Hive diverse usano schemi di database del metastore diversi. Ad esempio, non è possibile condividere un Metastore nei cluster hive 2,1 e hive 3,1 con versione.

* In HDInsight 4.0, Spark e Hive usano cataloghi indipendenti per accedere alle tabelle SparkSQL o Hive. Una tabella creata da Spark si trova nel catalogo Spark. Una tabella creata da Hive si trova nel catalogo Hive. Questo comportamento è diverso da quello di HDInsight 3.6 in cui Hive e Spark condividono un catalogo comune. L'integrazione di Hive e Spark in HDInsight 4.0 si basa su Hive Warehouse Connector (HWC). HWC funziona come bridge tra Spark e Hive. Informazioni [sul connettore del warehouse di hive](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

* In HDInsight 4.0 se si vuole condividere il metastore tra Hive e Spark, è possibile modificare la proprietà metastore.catalog.default in hive nel cluster Spark. Questa proprietà è disponibile in Ambari Advanced spark2-hive-site-override. È importante comprendere che la condivisione del metastore funziona solo per le tabelle hive esterne. Questa operazione non funzionerà se sono presenti tabelle hive interne/gestite o tabelle ACID.  

## <a name="apache-oozie-metastore"></a>Metastore Apache OOZIE

Apache Oozie è un sistema di coordinamento dei flussi di lavoro che consente di gestire i processi Hadoop. Oozie supporta i processi Hadoop per Apache MapReduce, Pig, Hive e altri.  Oozie usa un Metastore per archiviare i dettagli sui flussi di lavoro. Per ottenere un miglioramento delle prestazioni quando si usa Oozie, è possibile usare il database SQL di Azure come metastore personalizzato. Il Metastore fornisce l'accesso ai dati del processo di oozie dopo l'eliminazione del cluster.

Per istruzioni sulla creazione di un metastore Oozie con il database SQL di Azure, vedere come [usare Apache Oozie per i flussi di lavoro](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>Database Ambari personalizzato

Per usare il proprio database esterno con Apache Ambari in HDInsight, vedere [database Apache Ambari personalizzato](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Passaggi successivi

* [Configurare i cluster di HDInsight con Apache Hadoop, Apache Spark, Apache Kafka e altro ancora](./hdinsight-hadoop-provision-linux-clusters.md)
