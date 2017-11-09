---
title: Setup del cluster per Hadoop, Spark, Kafka, HBase o R Server - Azure HDInsight | Documentazione Microsoft
description: Imposta i cluster Hadoop, Kafka, Spark, HBase, R Server o Storm per HDInsight da un browser, dall'interfaccia della riga di comando di Azure, da Azure PowerShell, REST o SDK.
keywords: impostazione del cluster hadoop, impostazione del cluster kafka, impostazione del cluster spark, caratteristiche del cluster in hadoop
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 23a01938-3fe5-4e2e-8e8b-3368e1bbe2ca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: jgao
ms.openlocfilehash: 8118570071aa5af6ec74c971a711b2eab07fb1b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-clusters-in-hdinsight-with-hadoop-spark-kafka-and-more"></a>Configurare i cluster di HDInsight con Hadoop, Spark, Kafka e altro ancora

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Informazioni su come impostare e configurare i cluster di HDInsight con Hadoop, Spark, Kafka, Interactive Query, HBase, R Server o Storm. Informazioni su come personalizzare i cluster e proteggerli aggiungendoli a un dominio.

Un cluster Hadoop è costituito da alcune macchine virtuali (nodi) che vengono usate per l'elaborazione distribuita di attività. Azure HDInsight gestisce i dettagli di implementazione dell'installazione e della configurazione dei singoli nodi. È quindi necessario specificare solo le informazioni di configurazione generali. 

> [!IMPORTANT]
>La fatturazione del cluster HDInsight inizia dopo la creazione del cluster e si interrompe solo quando questo viene eliminato. La fatturazione avviene con tariffa oraria, perciò si deve sempre eliminare il cluster in uso quando non lo si usa più. Informazioni su come [eliminare un cluster](hdinsight-delete-cluster.md)
>

## <a name="cluster-setup-methods"></a>Metodi di installazione del cluster
La tabella seguente illustra i diversi metodi che è possibile usare per configurare un cluster HDInsight.

| Cluster creati con | Web browser | Riga di comando | API REST | SDK | 
| --- |:---:|:---:|:---:|:---:|
| [Portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Data factory di Azure](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Interfaccia della riga di comando di Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |
| [Modelli di Gestione risorse di Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

## <a name="quick-create-basic-cluster-setup"></a>Creazione rapida: configurazione base del cluster
In questo articolo viene illustrata la configurazione nel [portale di Azure](https://portal.azure.com), in cui è possibile creare un cluster HDInsight con le opzioni *Creazione rapida* o *Personalizzato*. 

Seguire le istruzioni sullo schermo per eseguire una configurazione base del cluster. Di seguito sono riportati dettagli relativi a:

* [Nome del gruppo di risorse](#resource-group-name)
* [Tipi di cluster e configurazione](#cluster-types) 
* [Account di accesso del cluster e nome utente SSH](#cluster-login-and-ssh-username)
* [Posizione](#location)

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [Ritiro di HDInsight 3.3](hdinsight-component-versioning.md#hdinsight-windows-retirement).
>

## <a name="resource-group-name"></a>Nome del gruppo di risorse 

[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) consente di usare le risorse dell'applicazione come gruppo, denominato gruppo di risorse di Azure. È quindi possibile distribuire, aggiornare, monitorare o eliminare tutte le risorse per l'applicazione in un'unica operazione coordinata.

## <a name="cluster-types"></a> Tipi di cluster e configurazione
In Azure HDInsight sono attualmente disponibili i tipi di cluster seguenti, ognuno con un set di componenti per offrire determinate funzionalità.

> [!IMPORTANT]
> I cluster HDInsight sono disponibili i vari tipi, ognuno per un carico di lavoro o una tecnologia specifici. Non è disponibile alcun metodo supportato per creare un cluster che combini più tipi, ad esempio Storm e HBase in un cluster. Se la soluzione richiede tecnologie che vengono distribuite tra più tipi di cluster HDInsight, una [rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network) è in grado di connettere i tipi di cluster necessari. 
>
>

| Tipo di cluster | Funzionalità |
| --- | --- |
| [Hadoop](hdinsight-hadoop-introduction.md) |Query batch e analisi dei dati archiviati |
| [HBase](hdinsight-hbase-overview.md) |Elaborazione di grandi quantità di dati NoSQL senza schema |
| [Storm](hdinsight-storm-overview.md) |Elaborazione di eventi in tempo reale |
| [Spark](hdinsight-apache-spark-overview.md) |Elaborazione in memoria, query interattive, elaborazione di flussi di micro batch |
| [Kafka (anteprima)](hdinsight-apache-kafka-introduction.md) | Piattaforma di streaming open source distribuita che può essere usata per compilare applicazioni e pipeline di dati in streaming in tempo reale. |
| [R Server](hdinsight-hadoop-r-server-overview.md) |Ampia gamma di statistiche di Big Data, modellazione predittiva e funzionalità di Machine Learning |
| [Interactive Query](hdinsight-hadoop-use-interactive-hive.md) |Caching in memoria per query Hive interattive e più rapide |

### <a name="number-of-nodes-for-each-cluster-type"></a>Numero di nodi per ogni tipo di cluster
Ogni tipo di cluster ha il proprio numero di nodi, una terminologia specifica per i nodi e dimensioni predefinite delle macchine virtuali. Nella tabella seguente, il numero di nodi per ogni tipo di nodo è indicato tra parentesi.

| Tipo | Nodi | Diagramma |
| --- | --- | --- |
| Hadoop |Nodo head (2), nodo dati (1+) |![Nodi del cluster HDInsight Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Server head (2), server di area (1+), nodo master/ZooKeeper (3) |![Nodi del cluster HDInsight HBase](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nodo Nimbus (2), server supervisore (1+), nodo ZooKeeper (3) |![Nodi del cluster HDInsight Storm](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |Nodo head (2), nodo Worker (1+), nodo ZooKeeper (3), gratuito per le macchine virtuali ZooKeeper con dimensioni A1 |![Nodi del cluster HDInsight Spark](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

Per altre informazioni, vedere [Configurazione del nodo predefinito e dimensioni della macchina virtuale per i cluster](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) in "Componenti e versioni di Hadoop in HDInsight"

### <a name="hdinsight-version"></a>Versione HDInsight
Scegliere la versione di HDInsight per questo cluster. Per altre informazioni, vedere [Versioni supportate di HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="cluster-tiers"></a>Livello cluster: livelli di servizio HDInsight

Azure HDInsight presenta le offerte cloud per i Big Data in due livelli di servizio: Standard e Premium.  Per altre informazioni, vedere [HDInsight Standard e HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium).

La schermata seguente mostra le informazioni sul portale di Azure per la scelta dei tipi di cluster.

![Configurazione di HDInsight Premium](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-type-configuration.png)


## <a name="cluster-login-and-ssh-user-name"></a>Account di accesso del cluster e nome utente SSH
Con i cluster HDInsight è possibile configurare due account utente durante la creazione del cluster:

* Utente HTTP: il nome utente predefinito è *admin*. e usa la configurazione di base nel portale di Azure. In alcuni casi, viene chiamato "utente cluster".
* Utente SSH (cluster Linux): usato per la connessione ai cluster tramite SSH. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="location"></a>Posizione (regioni) per cluster e risorse di archiviazione

Non è necessario specificare il percorso del cluster in modo esplicito: il cluster si trova nella stessa posizione delle risorse di archiviazione predefinite. Per un elenco di aree supportate, fare clic sull'elenco a discesa **Area** in [Prezzi di HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

## <a name="storage-endpoints-for-clusters"></a>Endpoint di archiviazione per i cluster

Sebbene l'installazione locale di Hadoop usi Hadoop Distributed File System (HDFS) per l'archiviazione nel cluster, nel cloud vengono usati degli endpoint di archiviazione connessi al cluster. I cluster HDInsight usano [Azure Data Lake Store](hdinsight-hadoop-use-data-lake-store.md) o [i BLOB in Archiviazione di Azure](hdinsight-hadoop-use-blob-storage.md). L'uso di Archiviazione di Azure o Data Lake Store consente di eliminare in modo sicuro i cluster HDInsight usati per il calcolo, pur mantenendo i dati. 

> [!WARNING]
> L'uso di un account di archiviazione aggiuntivo in una località diversa rispetto al cluster HDInsight non è supportato.

Durante la configurazione, per l'endpoint di archiviazione predefinito si specifica un contenitore blob di un account di archiviazione di Azure o un Data Lake Store. L'archiviazione predefinita include registri di sistema e applicazioni. Facoltativamente, è possibile specificare degli account di Archiviazione di Azure aggiuntivi e degli account di Data Lake Store a cui il cluster può accedere. Il cluster HDInsight e l'account di archiviazione da esso dipendente devono trovarsi nella stessa posizione di Azure.

![Impostazioni di archiviazione del cluster: endpoint di archiviazione compatibili con HDFS](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-creation-storage.png)

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


### <a name="optional-metastores"></a>Metastore facoltativi
È possibile creare dei metastore Hive o Oozie facoltativi. Tuttavia, non tutti i tipi di cluster supportano i metastore e Azure SQL Data Warehouse non è compatibile con i metastore. 

> [!IMPORTANT]
> Quando si crea un metastore personalizzato, non usare un nome di database contenente trattini, segni meno o spazi. perché in quel caso il processo di creazione del cluster non andrebbe a buon fine.

### <a name="use-hiveoozie-metastore"></a>Metastore Hive

Per conservare le tabelle Hive dopo aver eliminato il cluster HDInsight, usare un metastore personalizzato. Sarà quindi possibile associare il metastore a un altro cluster HDInsight.

Un metastore HDInsight creato per una versione del cluster HDInsight non può essere condiviso in versioni diverse del cluster HDInsight. Per un elenco di versioni di HDInsight, vedere [Versioni supportate di HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="oozie-metastore"></a>Metastore Oozie

Per ottenere un miglioramento delle prestazioni quando si usa Oozie, usare un metastore personalizzato. Un metastore può anche fornire l'accesso ai dati di processo Oozie dopo l'eliminazione del cluster. 

> [!IMPORTANT]
> Non è possibile riutilizzare un metastore Oozie personalizzato. Per usare un metastore Oozie personalizzato, è necessario specificare un database SQL di Azure vuoto al momento della creazione del cluster HDInsight.

## <a name="configure-cluster-size"></a>Configurare le dimensioni del cluster

Verrà addebitato l'uso del nodo, purché il cluster esista. La fatturazione inizia con la creazione del cluster e si interrompe quando il cluster viene eliminato. I cluster non possono essere deallocati o messi in attesa.

Il costo del cluster HDInsight è determinato dal numero di nodi e dalle dimensioni delle macchine virtuali per i nodi. 

Diversi tipi di cluster hanno diversi tipi, numeri e dimensioni di nodi:
* Tipo predefinito di cluster Hadoop: 
    * Due *nodi head*  
    * Quattro *nodi dati*
* Tipo predefinito di cluster Storm: 
    * Due *nodi Nimbus*
    * Tre *nodi ZooKeeper*
    * Quattro *nodi Supervisor* 

Se si sta solo provando HDInsight, è consigliabile usare un nodo di dati. Per altre informazioni sui prezzi di HDInsight, vedere [Prezzi di HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]
> Il limite relativo alle dimensioni del cluster dipende dalla sottoscrizione di Azure. Per aumentare il limite, contattare il team del [supporto fatturazione di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).
>

Quando si usa il portale di Azure per configurare il cluster, le dimensioni del nodo vengono esposte tramite il pannello **Piani tariffari per il nodo**. Nel portale è anche possibile visualizzare il costo associato alle diverse dimensioni dei nodi. 

![Dimensioni dei nodi delle VM di HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-node-sizes.png)

### <a name="virtual-machine-sizes"></a>Dimensioni delle macchine virtuali 
Quando si distribuiscono i cluster, scegliere le risorse di calcolo in base alla soluzione da distribuire. Per i cluster HDInsight vengono usate le macchine virtuali seguenti:
* Macchine virtuali serie A e D1-4: [Dimensioni delle macchine virtuali Linux per l'uso generico](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* Macchine virtuali serie D11-14: [Dimensioni ottimizzate per la memoria delle macchine virtuali Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Per scoprire quale valore usare per specificare le dimensioni di macchina virtuale durante la creazione di un cluster tramite SDK diversi o quando si usa Azure PowerShell, vedere [VM sizes to use for HDInsight clusters](../cloud-services/cloud-services-sizes-specs.md#size-tables) (Dimensioni delle macchine virtuali da usare per i cluster HDInsight). In questo articolo collegato, usare il valore della casella **Dimensioni** delle tabelle.

> [!IMPORTANT]
> Se si prevedono più di 32 nodi di lavoro, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM.
>
>

Per altre informazioni, vedere [Dimensioni delle macchine virtuali in Azure](../virtual-machines/windows/sizes.md). Per informazioni sui prezzi delle varie dimensioni, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).   

## <a name="custom-cluster-setup"></a>Personalizzare la configurazione del cluster
La configurazione di un cluster personalizzato si basa sulle impostazioni di Creazione rapida e aggiunge le opzioni seguenti:
- [Applicazioni HDInsight](#hdinsight-applications)
- [Dimensione del cluster](#cluster-size)
- Impostazioni avanzate
  - [Azioni script](#customize-clusters-using-script-action)
  - [Rete virtuale](#use-virtual-network)

## <a name="install-hdinsight-applications-on-clusters"></a>Installare applicazioni HDInsight in cluster

Un'applicazione HDInsight è un'applicazione che gli utenti possono installare in un cluster HDInsight basato su Linux. È possibile usare applicazioni fornite da Microsoft o terze parti o sviluppate in modo indipendente. Per altre informazioni, vedere [Installare applicazioni Hadoop di terze parti in Azure HDInsight](hdinsight-apps-install-applications.md).

La maggior parte delle applicazioni HDInsight viene installata in un nodo perimetrale vuoto.  Un nodo perimetrale vuoto è una macchina virtuale Linux in cui sono installati e configurati gli stessi strumenti client del nodo head. Il nodo perimetrale può essere usato per accedere al cluster e per testare e ospitare le applicazioni client. Per altre informazioni, vedere [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md)(Usare nodi perimetrali vuoti in HDInsight).

## <a name="advanced-settings-script-actions"></a>Impostazioni avanzate: azioni Script

L'uso di script durante la creazione consente di installare componenti aggiuntivi o personalizzare la configurazione di un cluster. Gli script vengono chiamati tramite un' **azione script**, ovvero un'opzione di configurazione che può essere usata da portale di Azure, dai cmdlet di Windows PowerShell per HDInsight o da .NET SDK per HDInsight. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster-linux.md).

Nel cluster è possibile eseguire alcuni componenti Java nativi, come Mahout e Cascading, sotto forma di file JAR (Java Archive). Questi file JAR possono essere distribuiti in Archiviazione di Azure e inviati ai cluster HDInsight usando i meccanismi di invio dei processi Hadoop. Per altre informazioni, vedere [Inviare processi Hadoop a livello di codice](hdinsight-submit-hadoop-jobs-programmatically.md).

> [!NOTE]
> In caso di problemi durante la distribuzione di file JAR in cluster HDInsight o nella chiamata di file JAR in cluster HDInsight, contattare il [Supporto Microsoft](https://azure.microsoft.com/support/options/).
>
> Cascading non è supportato da HDInsight, quindi in caso di problemi non è possibile rivolgersi al Supporto Microsoft. Per gli elenchi dei componenti supportati, vedere [Novità delle versioni cluster incluse con HDInsight](hdinsight-component-versioning.md).
>
>

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

## <a name="advanced-settings-extend-clusters-with-a-virtual-network"></a>Impostazioni avanzate: estendere i cluster con una rete virtuale
Se la soluzione richiede tecnologie che vengono distribuite tra più tipi di cluster HDInsight, una [rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network) è in grado di connettere i tipi di cluster necessari. Questa configurazione consente ai cluster e al codice in essi distribuito di comunicare direttamente tra loro.

Per altre informazioni sull'uso di una rete virtuale di Azure con HDInsight, vedere [Estendere HDInsight con le reti virtuali di Azure](hdinsight-extend-hadoop-virtual-network.md).

Per un esempio dell'uso di due tipi di cluster in una rete virtuale di Azure, vedere [Analizzare i dati del sensore con Storm e HBase](hdinsight-storm-sensor-data-analysis.md). Per altre informazioni sull'uso di HDInsight con una rete virtuale, inclusi i requisiti di configurazione specifici per la rete virtuale, vedere [Estendere le funzionalità di HDInsight usando Rete virtuale di Azure](hdinsight-extend-hadoop-virtual-network.md).

## <a name="troubleshoot-access-control-issues"></a>Risolvere i problemi relativi al controllo di accesso

Se si verificano problemi di creazione dei cluster HDInsight, vedere i [requisiti dei controlli di accesso](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Passaggi successivi

- [Definizioni di HDInsight, ecosistema di Hadoop e cluster Hadoop](hdinsight-hadoop-introduction.md)
- [Introduzione all'uso di Hadoop in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Lavorare da un computer Windows in Hadoop su HDInsight](hdinsight-hadoop-windows-tools.md)
