---
title: Usare gli archivi di metadati esterni - Azure HDInsight
description: Usare gli archivi di metadati esterni con i cluster HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 0a6bcb3fb94df56caf4d095097572dd8a704f856
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64701192"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Usare gli archivi di metadati esterni in Azure HDInsight

Il metastore di Apache Hive in HDInsight è una parte essenziale dell'architettura di Apache Hadoop. Un metastore è il repository centrale di schemi che può essere usato da altri strumenti di accesso ai Big Data come Apache Spark, Interactive Query (LLAP), Presto o Apache Pig. HDInsight usa un Database SQL di Azure come il metastore Hive.

![Architettura dell'archivio dei metadati Hive di HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Esistono due modi per configurare un metastore per i cluster HDInsight:

* [Metastore predefinito](#default-metastore)
* [Metastore personalizzato](#custom-metastore)

## <a name="default-metastore"></a>Metastore predefinito

Per impostazione predefinita, HDInsight crea un metastore con ogni tipo di cluster. È invece possibile specificare un metastore personalizzato. Il metastore predefinito include quanto segue:
- Non sono previsti costi aggiuntivi. HDInsight crea un metastore con ogni tipo di cluster senza alcun costo aggiuntivo per l'utente.
- Ogni metastore predefinito fa parte del ciclo di vita del cluster. Quando si elimina un cluster verranno eliminati anche il metastore e i metadati corrispondenti.
- Il metastore predefinito non può essere condiviso con altri cluster.
- Il metastore predefinito usa il database SQL di Azure di base, che ha un limite di 5 DTU (unità di trasmissione database).
Questo metastore predefinito viene usato in genere per carichi di lavoro relativamente semplici che non richiedono più cluster e che non hanno bisogno di conservare i metadati oltre il ciclo di vita del cluster.


## <a name="custom-metastore"></a>Metastore personalizzato

HDInsight supporta inoltre i metastore personalizzati, che sono consigliati per i cluster di produzione:
- Specificare il database SQL di Azure come metastore.
- Il ciclo di vita del metastore non è associato a un ciclo di vita dei cluster, pertanto è possibile creare ed eliminare i cluster senza perdere i metadati. I metadati come ad esempio gli schemi di Hive verranno mantenuti anche dopo aver eliminato e ricreato il cluster di HDInsight.
- Un metastore personalizzato consente di collegare più cluster e tipi di cluster al metastore. Ad esempio, un singolo metastore può essere condiviso tra i cluster Interactive Query, Hive e Spark in HDInsight.
- Si paga il costo di un metastore (database SQL di Azure) in base al livello di prestazioni scelto.
- È possibile aumentare il metastore in base alle esigenze.

![Caso d'uso dell'archivio dei metadati Hive di HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)


### <a name="select-a-custom-metastore-during-cluster-creation"></a>Selezionare un metastore personalizzato durante la creazione del cluster

È possibile indirizzare il cluster a un database SQL di Azure creato in precedenza durante la creazione del cluster, oppure è possibile configurare il database di SQL dopo la creazione del cluster. Questa opzione viene specificata selezionando Archiviazione > Impostazioni metastore durante la creazione di un nuovo cluster Hadoop, Spark o Hive interattivo dal portale di Azure.

![Portale dell'archivio dei metadati Hive di HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

È possibile aggiungere anche altri cluster a un metastore personalizzato dal portale di Azure o dalle configurazioni Ambari (Hive > Avanzate)

![Ambari dell'archivio dei metadati Hive di HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Procedure consigliate metastore Hive

Ecco alcune procedure consigliate generali per il metastore Hive di HDInsight:

- Usare un metastore personalizzato quando possibile per separare le risorse di calcolo (il cluster in esecuzione) e i metadati (archiviati nel metastore).
- Iniziare con un livello S2, che fornisce 50 DTU e 250 GB di spazio di archiviazione. Se viene visualizzato un collo di bottiglia, è possibile aumentare il database.
- Se si prevede l'accesso di più cluster HDInsight a dati separati, usare un database separato per il metastore in ogni cluster. Se un metastore è condiviso da più cluster HDInsight, significa che i cluster usano gli stessi metadati e file di dati utente sottostanti.
- Eseguire periodicamente il backup del metastore personalizzato. Il database SQL di Azure genera automaticamente i backup, ma il periodo di conservazione dei backup varia. Per altre informazioni, vedere [Informazioni sui backup automatici del database SQL](../sql-database/sql-database-automated-backups.md).
- Posizionare il metastore e il cluster HDInsight nella stessa area per ottenere le migliori prestazioni e ridurre i costi dei dati in uscita della rete.
- Monitorare il metastore per prestazioni e disponibilità usando strumenti di monitoraggio di Azure SQL Database, ad esempio il portale di Azure o i registri di monitoraggio di Azure.
- Quando una nuova versione di Azure HDInsight viene creata in un database del metastore personalizzato esistente, il sistema aggiorna lo schema del metastore che è irreversibile senza ripristino del database dal backup.
- Se un metastore è condiviso da più cluster, assicurarsi che tutti i cluster abbiano la stessa versione HDInsight. Versioni Hive diverse usano schemi di database del metastore diversi. Ad esempio, non è possibile condividere un metastore tra cluster Hive 1.2 e Hive 2.1. 

##  <a name="apache-oozie-metastore"></a>Metastore Apache Oozie

Apache Oozie è un sistema di coordinamento dei flussi di lavoro che consente di gestire i processi Hadoop.  Oozie supporta i processi Hadoop per Apache MapReduce, Pig, Hive e altri.  Oozie usa un metastore per archiviare i dettagli sui flussi di lavoro correnti e completati. Per ottenere un miglioramento delle prestazioni quando si usa Oozie, è possibile usare il database SQL di Azure come metastore personalizzato. Il metastore può anche fornire l'accesso ai dati di processo Oozie dopo l'eliminazione del cluster.

Per istruzioni sulla creazione di un metastore Oozie con il database SQL di Azure, vedere come [usare Apache Oozie per i flussi di lavoro](hdinsight-use-oozie-linux-mac.md).

## <a name="next-steps"></a>Passaggi successivi

- [Configurare i cluster di HDInsight con Apache Hadoop, Apache Spark, Apache Kafka e altro ancora](./hdinsight-hadoop-provision-linux-clusters.md)
