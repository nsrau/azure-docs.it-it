---
title: Create Apache Hadoop clusters using web browser, Azure HDInsight
description: Learn how to create Apache Hadoop, Apache HBase, Apache Storm, or Apache Spark clusters on Linux for HDInsight by using a web browser and the Azure portal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 820ddb8d06cfd2aac2b053305f23ad330e4fd7c3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215890"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Creare cluster basati su Linux in HDInsight tramite il portale di Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Il portale di Azure è uno strumento di gestione basato sul Web per servizi e risorse ospitati nel cloud di Microsoft Azure. In questo articolo viene illustrato come creare cluster Azure HDInsight basati su Linux tramite il portale.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure**. Vedere [How to get Azure Free trial for testing Hadoop in HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Come ottenere una versione di valutazione gratuita di Azure per testare Hadoop in HDInsight).
* **Un Web browser moderno**. Il portale di Azure usa HTML5 e JavaScript. Potrebbe non funzionare correttamente in Web browser meno recenti.

## <a name="create-clusters"></a>Creare cluster

Il portale di Azure espone la maggior parte delle proprietà del cluster. Con i modelli di Azure Resource Manager è possibile nascondere molti dettagli. Per altre informazioni, vedere [Creare cluster Apache Hadoop in HDInsight mediante modelli di Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Accedere al [portale di Azure](https://portal.azure.com).

1. From the left menu, navigate to **+ Create a resource** >  **Analytics** > **Azure HDInsight**.

    ![Create a new cluster in the Azure portal](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "Creazione di un nuovo cluster nel portale di Azure")

1. From the **Create HDInsight cluster** page, select **Go to classic create experience**.

    ![Go to classic create experience](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-create-classic.png)

1. Nella pagina **HDInsight** selezionare **Personalizzate (dimensioni, impostazioni, app)** .

1. Selezionare **1 Informazioni di base**. Then enter the following information:

    |Proprietà |Description |
    |---|---|
    |Nome cluster|Il nome deve essere univoco a livello globale.|
    |Sottoscrizione|From the drop-down list, select the Azure subscription that's used for the cluster.|
    |Tipo di cluster|Select the type of cluster you want to create. Ad esempio, Hadoop e Apache Spark. Il **sistema operativo** sarà **Linux**. Selezionare quindi una versione del tipo di cluster. Usare la versione predefinita, in caso di dubbi. Per altre informazioni, vedere [Versioni del cluster HDInsight](hdinsight-component-versioning.md).|
    |Nome utente dell'account di accesso del cluster|Provide the username, default is **admin**.|
    |Password di accesso al cluster|Provide the password.|
    |Nome utente Secure Shell (SSH)|Default is **sshuser**. If you want the same SSH password as the admin password you specified earlier, select the **Use cluster login password for SSH** check box. In caso contrario, specificare una **PASSWORD** o una **CHIAVE PUBBLICA** per autenticare l'utente SSH. Una chiave pubblica è l'approccio consigliabile. Scegliere **Seleziona** nella parte inferiore per salvare la configurazione delle credenziali.  Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Gruppo di risorse|Specificare se si vuole creare un nuovo gruppo di risorse o usarne uno esistente.|
    |Località|Specify a datacenter where the cluster is created.|

    ![HDInsight create cluster basics](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "Creazione di un nuovo cluster nel portale di Azure")

    > [!IMPORTANT]  
    > Sono disponibili svariati tipi di cluster HDInsight, che corrispondono al carico di lavoro o alla tecnologia per cui è ottimizzato il cluster. Non è disponibile alcun metodo supportato per creare un cluster che combini più tipi, ad esempio Storm e HBase in un cluster.

    Selezionare **Avanti** per passare alla pagina successiva.

1. In **2 Sicurezza + rete** è possibile connettere il cluster a una rete virtuale usando il menu a discesa. Selezionare una rete virtuale di Azure e la subnet, se si desidera posizionare il cluster in una rete virtuale. For information on using HDInsight with a virtual network, see [Plan a virtual network deployment for Azure HDInsight clusters](hdinsight-plan-virtual-network-deployment.md). L'articolo include i requisiti di configurazione specifici per la rete virtuale.

    If you want to use the **Enterprise Security Package**, follow these instructions: [Configure a HDInsight cluster with Enterprise Security Package by using Azure Active Directory Domain Services](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    Selezionare **Avanti** per passare alla pagina successiva.

1. From **3 Storage**, for **Storage Account Settings**, specify whether you want Azure Storage or Azure Data Lake Storage as your default storage. Per altre informazioni, vedere la tabella seguente.

    | Primary Storage type | Description |
    |------------------|-------------|
    | Archiviazione di Azure   |  * For **Selection method**, choose **My subscriptions** if you want to specify a storage account that's part of your Azure subscription. Selezionare quindi l'account di archiviazione. In caso contrario, selezionare **Chiave di accesso**. Specificare quindi le informazioni relative all'account di archiviazione che si vuole scegliere all'esterno della sottoscrizione di Azure.</br></br> * For **Default container**, choose the default container name suggested by the portal or specify your own.</br></br> * If Azure Blob storage is your default storage, you can also select **Additional Storage Accounts** to specify additional storage accounts to associate with the cluster. Per **Chiavi di archiviazione di Azure** selezionare **Aggiungi una chiave di archiviazione**. È quindi possibile specificare un account di archiviazione dalle sottoscrizioni di Azure o da altre sottoscrizioni. Specificare la chiave di accesso dell'account di archiviazione.</br></br> * If Blob storage is your default storage, you can also select **Data Lake Storage access** to specify Azure Data Lake Storage as additional storage. Per altre informazioni, vedere [Guida introduttiva: impostazione dei cluster in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
    | Archiviazione Azure Data Lake | Select **Azure Data Lake Storage Gen1** or **Azure Data Lake Storage Gen2**. Then refer to the article [Quickstart: Set up clusters in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) for instructions. |

    **Metastore Settings (optional)**

    È possibile, facoltativamente, specificare un database SQL per salvare i metadati Apache Hive e Apache Oozie associati al cluster. Per **Seleziona un database SQL per Hive** selezionare un database SQL. Specificare quindi il nome utente e la password per il database. Ripetere questi passaggi per i metadati Oozie.

    Di seguito sono riportate alcune considerazioni sull'uso di database SQL di Azure per i metastore:
    * Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure selezionare il nome del server. Questo è il server su cui viene eseguita l'istanza di database SQL. Nella visualizzazione del server selezionare **Configura**. Per **Servizi di Azure** selezionare quindi **Sì**. Selezionare quindi **Salva**.
    * Quando si crea un metastore, non usare trattini o segni meno nel nome del database, perché a causa di questi caratteri il processo di creazione del cluster non andrebbe a buon fine.

    ![HDInsight create cluster storage](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "Creazione di un nuovo cluster nel portale di Azure")

    > [!WARNING]  
    > L'uso di un account di archiviazione aggiuntivo in una località diversa rispetto al cluster HDInsight non è supportato.

    Selezionare **Avanti** per passare alla pagina successiva.

1. In **4 Applicazioni (facoltativo)** selezionare qualsiasi applicazione desiderata. Può trattarsi di applicazioni Microsoft, di fornitori di software indipendenti o sviluppate internamente. Per altre informazioni, vedere [Installare applicazioni durante la creazione del cluster](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).

    Selezionare **Avanti** per passare alla pagina successiva.

1. In **5 Dimensione del cluster** vengono visualizzate le informazioni sui nodi usati per questo cluster. Impostare il numero di nodi del ruolo di lavoro necessari per il cluster. Viene mostrato anche il costo stimato per l'esecuzione del cluster.

    ![HDInsight create cluster nodes](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "Specify number of cluster nodes")

   > [!IMPORTANT]  
   > Se si prevedono più di 32 nodi di lavoro, selezionare una dimensione del nodo head con almeno otto core e 14 GB di RAM. Pianificare i nodi al momento della creazione del cluster o ridimensionando il cluster dopo la creazione.
   >
   > Per altre informazioni sulle dimensioni dei nodi e i costi associati, vedere [Prezzi di Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

    Selezionare **Avanti** per passare alla pagina successiva.

1. In **6 Azioni script** è possibile personalizzare un cluster per installare i componenti personalizzati. Questa opzione è appropriata se si vuole usare uno script personalizzato per personalizzare un cluster durante la creazione. Per altre informazioni sulle azioni script, vedere [Personalizzare i cluster HDInsight basati su Linux tramite azioni script](hdinsight-hadoop-customize-cluster-linux.md).

   Selezionare **Avanti** per passare alla pagina successiva.

1. In **7 Riepilogo** verificare le informazioni immesse in precedenza. Selezionare quindi **Crea**.

    ![HDInsight create cluster summary](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "Specify number of cluster nodes")

    > [!NOTE]  
    > La creazione del cluster richiede tempo, in genere circa 20 minuti. Monitorare l'area **Notifiche** per controllare il processo di provisioning.

1. Dopo aver completato il processo di creazione, selezionare **Vai alla risorsa** dalla notifica **Distribuzione riuscita**. Nella finestra del cluster sono disponibili le informazioni indicate di seguito.

    ![HDI Azure portal cluster overview](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Proprietà del cluster")

    Some of the icons in the window are explained as follows:

    |Proprietà | Description |
    |---|---|
    |Panoramica|Provides all the essential information about the cluster. Ad esempio, il nome, il gruppo di risorse a cui appartiene, il percorso, il sistema operativo e l'URL per il dashboard del cluster.|
    |Dashboard del cluster|Directs you to the Ambari portal associated with the cluster.|
    |SSH + Cluster login|Provides information needed to access the cluster by using SSH.|
    |Eliminazione|consente di eliminare il cluster HDInsight.|

## <a name="customize-clusters"></a>Personalizzare i cluster

* [Personalizzare cluster HDInsight tramite Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Personalizzare i cluster HDInsight basati su Linux tramite azioni script](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Eliminare il cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Risolvere problemi

Se si verificano problemi di creazione dei cluster HDInsight, vedere i [requisiti dei controlli di accesso](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Passaggi successivi

È stato creato un cluster HDInsight. È ora possibile procedere e scoprire come utilizzare il cluster.

### <a name="apache-hadoop-clusters"></a>Cluster Apache Hadoop

* [Usare Apache Hive con HDInsight](hadoop/hdinsight-use-hive.md)
* [Usare MapReduce con HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Cluster Apache HBase

* [Introduzione ad Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Sviluppare applicazioni Java per Apache HBase in HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Cluster Apache Storm

* [Sviluppare topologie Java per Apache Storm in HDInsight](storm/apache-storm-develop-java-topology.md)
* [Usare i componenti di Python in Apache Storm in HDInsight](storm/apache-storm-develop-python-topology.md)
* [Distribuzione e monitoraggio di topologie con Apache Storm in HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Cluster Apache Spark

* [Creare un'applicazione autonoma con Scala](spark/apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Apache Spark usando Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](spark/apache-spark-use-bi-tools.md)
* [Apache Spark con Machine Learning: utilizzare Spark in HDInsight per stimare i risultati dell'ispezione cibo](spark/apache-spark-machine-learning-mllib-ipython.md)
