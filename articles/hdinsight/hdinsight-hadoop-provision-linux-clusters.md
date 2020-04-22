---
title: Configurare i cluster di HDInsight con Apache Hadoop, Apache Spark, Apache Kafka e altro ancora
description: Configurare i cluster Hadoop, Kafka, Spark, HBase, R Server o Storm per HDInsight da un browser, dall'interfaccia della riga di comando classica di Azure, da Azure PowerShell, REST o SDK.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.date: 02/12/2020
ms.openlocfilehash: 6bba71ab35920027cc7296e774936c3d1deb8f92
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770322"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Configurare i cluster di HDInsight con Apache Hadoop, Apache Spark, Apache Kafka e altro ancora

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Informazioni su come impostare e configurare Apache Hadoop, Apache Spark, Apache Kafka, Interactive Query, Apache HBase, ML Services o Apache Storm in HDInsight. Informazioni su come personalizzare i cluster e proteggerli aggiungendoli a un dominio.

Un cluster Hadoop è costituito da alcune macchine virtuali (nodi) che vengono usate per l'elaborazione distribuita di attività. Azure HDInsight gestisce i dettagli di implementazione dell'installazione e della configurazione dei singoli nodi. È quindi necessario specificare solo le informazioni di configurazione generali.

> [!IMPORTANT]  
> La fatturazione del cluster HDInsight inizia dopo la creazione del cluster e si interrompe solo quando questo viene eliminato. La fatturazione avviene con tariffa oraria, perciò si deve sempre eliminare il cluster in uso quando non lo si usa più. Informazioni su come [eliminare un cluster](hdinsight-delete-cluster.md)

## <a name="cluster-setup-methods"></a>Metodi di installazione del cluster

La tabella seguente illustra i diversi metodi che è possibile usare per configurare un cluster HDInsight.

| Cluster creati con | Web browser | Riga di comando | API REST | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Interfaccia della riga di comando di Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Curl](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Modelli di Gestione risorse di Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

Questo articolo illustra come eseguire l'installazione nel portale di [Azure,](https://portal.azure.com)in cui è possibile creare un cluster HDInsight.This article walks you through setup in the Azure portal , where you can create an HDInsight cluster.

## <a name="basics"></a>Nozioni di base

![hdinsight creare opzioni personalizzate rapida](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png)

### <a name="project-details"></a>Dettagli del progetto

[Azure Resource Manager](../azure-resource-manager/management/overview.md) consente di usare le risorse nell'applicazione come gruppo, definito gruppo di risorse di Azure.Azure Resource Manager helps you work with the resources in your application as an group, referred to as an Azure [resource group](../azure-resource-manager/management/overview.md#resource-groups). È quindi possibile distribuire, aggiornare, monitorare o eliminare tutte le risorse per l'applicazione in un'unica operazione coordinata.

### <a name="cluster-details"></a>Dettagli dei cluster

#### <a name="cluster-name"></a>Nome cluster

I nomi dei cluster HDInsight hanno le restrizioni seguenti:HDInsight cluster names have the following restrictions:

* Caratteri consentiti: a-z, 0-9, A-z
* Lunghezza massima: 59
* Nomi riservati: app
* L'ambito di denominazione del cluster è per tutte le azure, in tutte le sottoscrizioni. Pertanto, il nome del cluster deve essere univoco in tutto il mondo.
* I primi sei caratteri devono essere univoci all'interno di una rete virtuale

#### <a name="region"></a>Region

Non è necessario specificare il percorso del cluster in modo esplicito: il cluster si trova nella stessa posizione delle risorse di archiviazione predefinite. Per un elenco delle aree supportate, selezionare l'elenco a discesa **Area** sui [prezzi DI HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

#### <a name="cluster-type"></a>Tipo di cluster

In Azure HDInsight sono attualmente disponibili i tipi di cluster seguenti, ognuno con un set di componenti per offrire determinate funzionalità.

> [!IMPORTANT]  
> I cluster HDInsight sono disponibili i vari tipi, ognuno per un carico di lavoro o una tecnologia specifici. Non è disponibile alcun metodo supportato per creare un cluster che combini più tipi, ad esempio Storm e HBase in un cluster. Se la soluzione richiede tecnologie che vengono distribuite tra più tipi di cluster HDInsight, una [rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network) è in grado di connettere i tipi di cluster necessari.

| Tipo di cluster | Funzionalità |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Query batch e analisi dei dati archiviati |
| [hbase](hbase/apache-hbase-overview.md) |Elaborazione di grandi quantità di dati NoSQL senza schema |
| [Interactive Query](./interactive-query/apache-interactive-query-get-started.md) |Caching in memoria per query Hive interattive e più rapide |
| [Kafka](kafka/apache-kafka-introduction.md) | Piattaforma di streaming open source distribuita che può essere usata per compilare applicazioni e pipeline di dati in streaming in tempo reale. |
| [ML Services](r-server/r-server-overview.md) |Ampia gamma di statistiche di Big Data, modellazione predittiva e funzionalità di Machine Learning |
| [Spark](spark/apache-spark-overview.md) |Elaborazione in memoria, query interattive, elaborazione di flussi di micro batch |
| [Storm](storm/apache-storm-overview.md) |Elaborazione di eventi in tempo reale |

#### <a name="version"></a>Versione

Scegliere la versione di HDInsight per questo cluster. Per altre informazioni, vedere [Versioni supportate di HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="cluster-credentials"></a>Credenziali del cluster

Con i cluster HDInsight è possibile configurare due account utente durante la creazione del cluster:

* Nome utente di accesso al cluster: il nome utente predefinito è *admin*. Usa la configurazione di base nel portale di Azure.It uses the basic configuration on the Azure portal. A volte viene chiamato "Utente del cluster" o "Utente HTTP".
* Nome utente Secure Shell (SSH): utilizzato per connettersi al cluster tramite SSH. Per ulteriori informazioni, consultate [Usare SSH con HDInsight.](hdinsight-hadoop-linux-use-ssh-unix.md)

Il nome utente HTTP presenta le restrizioni seguenti:

* Caratteri speciali `_` consentiti: e`@`
* Caratteri non consentiti: ."',\/:'<>!{}& di spazio
* Lunghezza massima: 20

Il nome utente SSH presenta le seguenti restrizioni:

* Caratteri speciali`_` consentiti: e`@`
* Caratteri non consentiti: ."',\/:'<>!{}& di spazio
* Lunghezza massima: 64
* Nomi riservati: hadoop, users, oozie, hive, mapred, ambari-qa, zookeeper, tez, hdfs, sqoop, filato, hcat, ams, hbase, storm, administrator, admin, user, user1, test, user2, test1, user3, admin1, 1, 123, a, actuser, adm, admin2, aspnet, backup, console, david, guest, john, proprietario, root, server, SQL, supporto, support_388945a0, sys, test2, test3, utente4, utente s5

## <a name="storage"></a>Archiviazione

![Impostazioni di archiviazione cluster: endpoint compatibili con HDFS](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png)

Sebbene l'installazione locale di Hadoop usi Hadoop Distributed File System (HDFS) per l'archiviazione nel cluster, nel cloud vengono usati degli endpoint di archiviazione connessi al cluster. L'uso dell'archiviazione cloud significa che è possibile eliminare in modo sicuro i cluster HDInsight usati per il calcolo pur conservando i dati.

I cluster HDInsight possono usare le opzioni di archiviazione seguenti:HDInsight clusters can use the following storage options:

* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1
* Scopo generale dell'archiviazione di Azure v2Azure Storage General Purpose v2
* Scopo generale dell'archiviazione di Azure v1Azure Storage General Purpose v1
* BLOB blocco archiviazione di Azure **(supportato solo come archiviazione secondaria)**

Per altre informazioni sulle opzioni di archiviazione con HDInsight, vedere Confrontare le opzioni di [archiviazione per l'uso con i cluster di Azure HDInsight.For](hdinsight-hadoop-compare-storage-options.md)more information on storage options with HDInsight, see Compare storage options for use with Azure HDInsight clusters.

> [!WARNING]  
> L'uso di un account di archiviazione aggiuntivo in una località diversa rispetto al cluster HDInsight non è supportato.

Durante la configurazione, per l'endpoint di archiviazione predefinito si specifica un contenitore BLOB di un account di archiviazione di Azure o Data Lake Storage. L'archiviazione predefinita include log di sistema e applicazioni. Facoltativamente, è possibile specificare degli account di archiviazione di Azure aggiuntivi e degli account di Data Lake Storage a cui il cluster può accedere. Il cluster HDInsight e l'account di archiviazione da esso dipendente devono trovarsi nella stessa posizione di Azure.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

### <a name="metastore-settings"></a>Impostazioni metastore

È possibile creare dei metastore Hive o Apache Oozie facoltativi. Tuttavia, non tutti i tipi di cluster supportano i metastore e Azure SQL Data Warehouse non è compatibile con i metastore.

Per altre informazioni, vedere [Use external metadata stores in Azure HDInsight (Usare archivi di metadati esterni in Azure HDInsight)](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> Quando si crea un metastore personalizzato, non usare un nome di database contenente trattini, segni meno o spazi. perché in quel caso il processo di creazione del cluster non andrebbe a buon fine.

#### <a name="sql-database-for-hive"></a>Database SQL per Hive

Per conservare le tabelle Hive dopo aver eliminato il cluster HDInsight, usare un metastore personalizzato. Sarà quindi possibile associare il metastore a un altro cluster HDInsight.

Un metastore HDInsight creato per una versione del cluster HDInsight non può essere condiviso tra diverse versioni del cluster HDInsight.An HDInsight metastore that is created for one HDInsight cluster version can't be shared across different HDInsight cluster versions. Per un elenco di versioni di HDInsight, vedere [Versioni supportate di HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

#### <a name="sql-database-for-oozie"></a>Database SQL per Oozie

Per ottenere un miglioramento delle prestazioni quando si usa Oozie, usare un metastore personalizzato. Un metastore può anche fornire l'accesso ai dati di processo Oozie dopo l'eliminazione del cluster.

#### <a name="sql-database-for-ambari"></a>Database SQL per Ambari

Ambari viene utilizzato per monitorare i cluster HDInsight, apportare modifiche alla configurazione e archiviare le informazioni di gestione dei cluster, nonché la cronologia dei processi. La funzionalità personalizzata Di ambari DB consente di distribuire un nuovo cluster e di configurare Ambari in un database esterno gestito. Per ulteriori informazioni, vedere [Custom Ambari DB](./hdinsight-custom-ambari-db.md).

> [!IMPORTANT]  
> Non è possibile riutilizzare un metastore Oozie personalizzato. Per usare un metastore Oozie personalizzato, è necessario specificare un database SQL di Azure vuoto al momento della creazione del cluster HDInsight.

## <a name="security--networking"></a>Sicurezza e networking

![Opzioni di creazione di HDInsight Scegliere il pacchetto di sicurezza aziendale](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png)

### <a name="enterprise-security-package"></a>Pacchetto di sicurezza aziendale

Per i tipi di cluster Hadoop, Spark, HBase, Kafka e Interactive Query, è possibile scegliere di abilitare **Enterprise Security Package**. Questo pacchetto consente di eseguire una configurazione cluster più sicura usando Apache Ranger e integrando Azure Active Directory. Per altre informazioni, vedere [Panoramica della sicurezza aziendale in Azure HDInsight.For more information, see Overview of enterprise security in Azure HDInsight.](./domain-joined/hdinsight-security-overview.md)

Il Pacchetto di sicurezza aziendale consente di integrare HDInsight con Active Directory e Apache Ranger. È possibile creare più utenti usando il Pacchetto di sicurezza aziendale.

Per altre informazioni sulla creazione di un cluster HDInsight aggiunto al dominio, vedere [Create domain-joined HDInsight sandbox environment](./domain-joined/apache-domain-joined-configure.md) (Creare un ambiente sandbox HDInsight aggiunto al dominio).

### <a name="tls"></a>TLS

Per altre informazioni, vedere [Transport Layer SecurityFor](./transport-layer-security.md) more information, see Transport Layer Security

### <a name="virtual-network"></a>Rete virtuale

Se la soluzione richiede tecnologie che vengono distribuite tra più tipi di cluster HDInsight, una [rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network) è in grado di connettere i tipi di cluster necessari. Questa configurazione consente ai cluster e al codice in essi distribuito di comunicare direttamente tra loro.

Per altre informazioni sull'uso di una rete virtuale di Azure con HDInsight, vedere [Pianificare una rete virtuale per HDInsight.For](hdinsight-plan-virtual-network-deployment.md)more information on using an Azure virtual network with HDInsight, see Plan a virtual network for HDInsight.

Per un esempio dell'uso di due tipi di cluster in una rete virtuale di Azure, vedere [Usare lo streaming strutturato Apache Spark con Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md). Per altre informazioni sull'uso di HDInsight con una rete virtuale, inclusi i requisiti di configurazione specifici per la rete virtuale, vedere [Pianificare una rete virtuale per HDInsight.For](hdinsight-plan-virtual-network-deployment.md)more information about using HDInsight with a virtual network, including specific configuration requirements for the virtual network, see Plan a virtual network for HDInsight.

### <a name="disk-encryption-setting"></a>Impostazione crittografia disco

Per ulteriori informazioni, vedere Crittografia del [disco di chiave gestita dal cliente](./disk-encryption.md).

### <a name="kafka-rest-proxy"></a>Proxy REST Kafka

Questa impostazione è disponibile solo per il tipo di cluster Kafka. Per ulteriori informazioni, vedere [Utilizzo di un proxy REST](./kafka/rest-proxy.md).

### <a name="identity"></a>Identità

Per altre informazioni, vedere [Identità gestite in Azure HDInsight.For more information, see Managed identities in Azure HDInsight.](./hdinsight-managed-identities.md)

## <a name="configuration--pricing"></a>Configurazione e prezzi

![HDInsight scegliere le dimensioni del nodoHDInsight choose your node size](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png)

L'utilizzo del nodo viene fatturato per tutto il tempo dell'attività del cluster. La fatturazione inizia con la creazione del cluster e si interrompe quando il cluster viene eliminato. I cluster non possono essere deallocati o messi in attesa.

### <a name="node-configuration"></a>Configurazione nodi

Ogni tipo di cluster ha il proprio numero di nodi, una terminologia specifica per i nodi e dimensioni predefinite delle macchine virtuali. Nella tabella seguente, il numero di nodi per ogni tipo di nodo è indicato tra parentesi.

| Type | Nodi | Diagramma |
| --- | --- | --- |
| Hadoop |Nodo head (2), nodo di lavoro (1o) |![Nodi del cluster HDInsight Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| hbase |Server head (2), server di area (1+), nodo master/ZooKeeper (3) |![Configurazione del tipo di cluster HBase HDInsightHDInsight HBase type setup](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nodo Nimbus (2), server supervisore (1+), nodo ZooKeeper (3) |![Configurazione del tipo di cluster tempesta HDInsightHDInsight storm cluster type setup](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |Nodo di testa (2), nodo di lavoro (1o), nodo di Giardino zoologico (gratuito per la dimensione della macchina virtuale di A1 zooKeeper) |![Configurazione del tipo di cluster di spark HDInsightHDInsight spark cluster type setup](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

Per altre informazioni, vedere [Configurazione del nodo predefinito e dimensioni della macchina virtuale per i cluster](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) in "Componenti e versioni di Hadoop in HDInsight"

Il costo del cluster HDInsight è determinato dal numero di nodi e dalle dimensioni delle macchine virtuali per i nodi.

Diversi tipi di cluster hanno diversi tipi, numeri e dimensioni di nodi:
* Tipo predefinito di cluster Hadoop:
    * Due *nodi head*  
    * Quattro *nodi di lavoro*
* Tipo predefinito di cluster Storm:
    * Due *nodi Nimbus*
    * Tre *nodi ZooKeeper*
    * Quattro *nodi Supervisor*

Se si sta solo provando HDInsight, è consigliabile usare un nodo Worker.If you're just trying out HDInsight, we recommend you use one Worker node. Per altre informazioni sui prezzi di HDInsight, vedere [Prezzi di HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]  
> Il limite relativo alle dimensioni del cluster dipende dalla sottoscrizione di Azure. Per aumentare il limite, contattare il team del [supporto fatturazione di Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

Quando si usa il portale di Azure per configurare il cluster, la dimensione del nodo è disponibile tramite la scheda **Configurazione e determinazione dei prezzi.** Nel portale è inoltre possibile visualizzare il costo associato alle diverse dimensioni dei nodi.

### <a name="virtual-machine-sizes"></a>Dimensioni delle macchine virtuali

Quando si distribuiscono i cluster, scegliere le risorse di calcolo in base alla soluzione da distribuire. Per i cluster HDInsight vengono usate le macchine virtuali seguenti:

* Macchine virtuali serie A e D1-4: [Dimensioni delle macchine virtuali Linux per l'uso generico](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* Macchine virtuali serie D11-14: [Dimensioni ottimizzate per la memoria delle macchine virtuali Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Per scoprire quale valore usare per specificare le dimensioni di macchina virtuale durante la creazione di un cluster tramite SDK diversi o quando si usa Azure PowerShell, vedere [VM sizes to use for HDInsight clusters](../cloud-services/cloud-services-sizes-specs.md#size-tables) (Dimensioni delle macchine virtuali da usare per i cluster HDInsight). In questo articolo collegato, usare il valore della casella **Dimensioni** delle tabelle.

> [!IMPORTANT]  
> Se sono necessari più di 32 nodi di lavoro in un cluster, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM.

Per altre informazioni, vedere [Dimensioni delle macchine virtuali in Azure](../virtual-machines/windows/sizes.md). Per informazioni sui prezzi delle varie dimensioni, vedere [Prezzi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).

### <a name="add-application"></a>Aggiunta di un'applicazione

Un'applicazione HDInsight è un'applicazione che gli utenti possono installare in un cluster HDInsight basato su Linux. È possibile usare applicazioni fornite da Microsoft o terze parti o sviluppate in modo indipendente. Per altre informazioni, vedere [Installare applicazioni Apache Hadoop di terze parti in Azure HDInsight](hdinsight-apps-install-applications.md).

La maggior parte delle applicazioni HDInsight viene installata in un nodo perimetrale vuoto.  Un nodo perimetrale vuoto è una macchina virtuale Linux in cui sono installati e configurati gli stessi strumenti client del nodo head. Il nodo perimetrale può essere usato per accedere al cluster e per testare e ospitare le applicazioni client. Per altre informazioni, vedere [Usare nodi perimetrali vuoti in HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="script-actions"></a>Azioni script

L'uso di script durante la creazione consente di installare componenti aggiuntivi o personalizzare la configurazione di un cluster. Gli script vengono chiamati tramite un' **azione script**, ovvero un'opzione di configurazione che può essere usata da portale di Azure, dai cmdlet di Windows PowerShell per HDInsight o da .NET SDK per HDInsight. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster-linux.md).

Nel cluster è possibile eseguire alcuni componenti Java nativi, come Apache Mahout e Cascading, sotto forma di file JAR (Java Archive). Questi file JAR possono essere distribuiti in Archiviazione di Azure e inviati ai cluster HDInsight usando i meccanismi di invio dei processi Hadoop. Per altre informazioni, vedere [Inviare processi Apache Hadoop a livello di codice](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]  
> In caso di problemi durante la distribuzione di file JAR in cluster HDInsight o nella chiamata di file JAR in cluster HDInsight, contattare il [Supporto Microsoft](https://azure.microsoft.com/support/options/).
>
> Cascading non è supportato da HDInsight, quindi in caso di problemi non è possibile rivolgersi al Supporto Microsoft. Per gli elenchi dei componenti supportati, vedere [Novità delle versioni del cluster fornite da HDInsight](hdinsight-component-versioning.md).

In alcuni casi è opportuno configurare i file di configurazione seguenti durante il processo di creazione:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Per altre informazioni, vedere [Personalizzare cluster HDInsight tramite Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere gli errori di creazione del cluster con Azure HDInsightTroubleshoot cluster creation failures with Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [Definizioni di HDInsight, ecosistema di Apache Hadoop e cluster Hadoop](hadoop/apache-hadoop-introduction.md)
* [Introduzione all'uso di Apache Hadoop in HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Lavorare da un computer Windows in Apache Hadoop su HDInsight](hdinsight-hadoop-windows-tools.md)
