---
title: Creare cluster Apache Hadoop con un Web browser, Azure HDInsight
description: Informazioni su come creare cluster Apache Hadoop, Apache HBase, Apache Storm, o Apache Spark in Linux per HDInsight usando un Web browser e il portale di anteprima di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: b2a5168cbda209d1d975db5e5f1c810a798ddb97
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098701"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Creare cluster basati su Linux in HDInsight tramite il portale di Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Il portale di Azure è uno strumento di gestione basato sul Web per servizi e risorse ospitati nel cloud di Microsoft Azure. In questo articolo viene illustrato come creare cluster Azure HDInsight basati su Linux tramite il portale.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Una sottoscrizione di Azure**. Vedere [How to get Azure Free trial for testing Hadoop in HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Come ottenere una versione di valutazione gratuita di Azure per testare Hadoop in HDInsight).
* **Un Web browser moderno**. Il portale di Azure usa HTML5 e JavaScript. Potrebbe non funzionare correttamente in Web browser meno recenti.

## <a name="create-clusters"></a>Creare i cluster

Il portale di Azure espone la maggior parte delle proprietà del cluster. Con i modelli di Azure Resource Manager è possibile nascondere molti dettagli. Per altre informazioni, vedere [Creare cluster Apache Hadoop in HDInsight mediante modelli di Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel menu a sinistra selezionare **+ Crea una risorsa**.

1. In **Azure Marketplace** selezionare **Analisi**.

1. In **In primo piano** selezionare **HDInsight**.

    ![Creare un nuovo cluster nel portale di Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "Creazione di un nuovo cluster nel portale di Azure")

1. Nella pagina **HDInsight** selezionare **Personalizzate (dimensioni, impostazioni, app)** .

1. Selezionare **1 Informazioni di base**. Immettere quindi le informazioni seguenti.

    ![HDInsight creare le nozioni di base del cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "Creazione di un nuovo cluster nel portale di Azure")

    * Immettere il **Nome cluster**. Il nome deve essere univoco a livello globale.

    * Nell'elenco a discesa **Sottoscrizione** selezionare la sottoscrizione di Azure che viene usata per il cluster.

    * Selezionare **Tipo di cluster**. Selezionare quindi il tipo di cluster che si vuole creare. Ad esempio, Hadoop e Apache Spark. Il **sistema operativo** sarà **Linux**. Selezionare quindi una versione del tipo di cluster. Usare la versione predefinita, in caso di dubbi. Per altre informazioni, vedere [Versioni del cluster HDInsight](hdinsight-component-versioning.md).
     
        > [!IMPORTANT]  
        > Sono disponibili svariati tipi di cluster HDInsight, che corrispondono al carico di lavoro o alla tecnologia per cui è ottimizzato il cluster. Non è disponibile alcun metodo supportato per creare un cluster che combini più tipi, ad esempio Storm e HBase in un cluster.
        
    * Per **Nome utente dell'account di accesso del cluster** e **Password dell'account di accesso del cluster** immettere il nome utente e la password dell'utente amministratore.

    * Immettere un **nome utente SSH**. Se si vuole che la password SSH sia uguale alla password dell'amministratore specificata in precedenza, selezionare la casella di controllo **Usa la stessa password dell'account di accesso del cluster**. In caso contrario, specificare una **PASSWORD** o una **CHIAVE PUBBLICA** per autenticare l'utente SSH. Una chiave pubblica è l'approccio consigliabile. Scegliere **Seleziona** nella parte inferiore per salvare la configurazione delle credenziali.
   
        Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

    * Per **Gruppo di risorse**, specificare se si desidera creare un nuovo gruppo di risorse o usarne uno esistente.

    * Specificare un **percorso** per il data center in cui viene creato il cluster.

    * Selezionare **Avanti** per passare alla pagina successiva.

4. In **2 Sicurezza + rete** è possibile connettere il cluster a una rete virtuale usando il menu a discesa. Selezionare una rete virtuale di Azure e la subnet, se si desidera posizionare il cluster in una rete virtuale. Per informazioni sull'uso di HDInsight con una rete virtuale, vedere [pianificare una distribuzione di rete virtuale per i cluster Azure HDInsight](hdinsight-plan-virtual-network-deployment.md). L'articolo include i requisiti di configurazione specifici per la rete virtuale. 

    Se si vuole usare **Enterprise Security Package**, seguire queste istruzioni: [Configurare un cluster HDInsight con Enterprise Security Package usando Azure Active Directory Domain Services](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    Selezionare **Avanti** per passare alla pagina successiva.

5. In **3 Archiviazione** specificare se si vuole impostare Archiviazione di Azure o Azure Data Lake Storage come risorsa di archiviazione predefinita. Per altre informazioni, vedere la tabella seguente.

     ![HDInsight creare l'archiviazione cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "Creazione di un nuovo cluster nel portale di Azure")

     | Archiviazione                                      | DESCRIZIONE |
     |----------------------------------------------|-------------|
     | **BLOB del servizio di archiviazione di Azure come risorsa di archiviazione predefinita**   | <ul><li>Per **Tipo di archiviazione primario** selezionare **Archiviazione di Azure**. Per **Metodo di selezione** scegliere **Sottoscrizioni personali** se si vuole specificare un account di archiviazione che fa parte della sottoscrizione di Azure. Selezionare quindi l'account di archiviazione. In caso contrario, selezionare **Chiave di accesso**. Specificare quindi le informazioni relative all'account di archiviazione che si vuole scegliere all'esterno della sottoscrizione di Azure.</li><li>Per **Contenitore predefinito** scegliere il nome del contenitore predefinito suggerito dal portale oppure specificarne uno personale.</li><li>Se si usa l'archiviazione BLOB di Azure come risorsa di archiviazione predefinita, è anche possibile selezionare **Account di archiviazione aggiuntivi** per specificare gli account di archiviazione aggiuntivi da associare al cluster. Per **Chiavi di archiviazione di Azure** selezionare **Aggiungi una chiave di archiviazione**. È quindi possibile specificare un account di archiviazione dalle sottoscrizioni di Azure o da altre sottoscrizioni. Specificare la chiave di accesso dell'account di archiviazione.</li><li>Se si usa l'archiviazione BLOB come risorsa di archiviazione predefinita, è anche possibile selezionare **Accesso a Data Lake Storage** per specificare Azure Data Lake Storage come risorsa di archiviazione aggiuntiva. Per altre informazioni, vedere [Avvio rapido: Impostazione dei cluster in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)</li></ul> |
     | **Azure Data Lake Storage come risorsa di archiviazione predefinita** | Per **Tipo di archiviazione primario** selezionare **Azure Data Lake Storage Gen1** o **Azure Data Lake Storage Gen2** e quindi fare riferimento all'articolo [Avvio rapido: impostazione dei cluster in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) per le istruzioni. |
     | **Metastore esterni**                      | È possibile, facoltativamente, specificare un database SQL per salvare i metadati Apache Hive e Apache Oozie associati al cluster. Per **Seleziona un database SQL per Hive** selezionare un database SQL. Specificare quindi il nome utente e la password per il database. Ripetere questi passaggi per i metadati Oozie.<br><br>Di seguito sono riportate alcune considerazioni sull'uso di database SQL di Azure per i metastore: <ul><li>Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure selezionare il nome del server. Questo è il server su cui viene eseguita l'istanza di database SQL. Nella visualizzazione del server selezionare **Configura**. Per **Servizi di Azure** selezionare quindi **Sì**. Selezionare quindi **Salva**.</li><li>Quando si crea un metastore, non usare trattini o segni meno nel nome del database, perché a causa di questi caratteri il processo di creazione del cluster non andrebbe a buon fine.</li></ul> |

     > [!WARNING]  
     > L'uso di un account di archiviazione aggiuntivo in una località diversa rispetto al cluster HDInsight non è supportato.

     Selezionare **Avanti** per passare alla pagina successiva.

6. In **4 Applicazioni (facoltativo)** selezionare qualsiasi applicazione desiderata. Può trattarsi di applicazioni Microsoft, di fornitori di software indipendenti o sviluppate internamente. Per altre informazioni, vedere [Installare applicazioni durante la creazione del cluster](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).

    Selezionare **Avanti** per passare alla pagina successiva.

6. In **5 Dimensione del cluster** vengono visualizzate le informazioni sui nodi usati per questo cluster. Impostare il numero di nodi del ruolo di lavoro necessari per il cluster. Viene mostrato anche il costo stimato per l'esecuzione del cluster.

    ![HDInsight creare nodi cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "Specificare il numero di nodi del cluster")

   > [!IMPORTANT]  
   > Se si prevedono più di 32 nodi di lavoro, selezionare una dimensione del nodo head con almeno otto core e 14 GB di RAM. Pianificare i nodi al momento della creazione del cluster o ridimensionando il cluster dopo la creazione.
   >
   > Per altre informazioni sulle dimensioni dei nodi e i costi associati, vedere [Prezzi di Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

    Selezionare **Avanti** per passare alla pagina successiva.

8. In **6 Azioni script** è possibile personalizzare un cluster per installare i componenti personalizzati. Questa opzione è appropriata se si vuole usare uno script personalizzato per personalizzare un cluster durante la creazione. Per altre informazioni sulle azioni script, vedere [Personalizzare i cluster HDInsight basati su Linux tramite azioni script](hdinsight-hadoop-customize-cluster-linux.md).

   Selezionare **Avanti** per passare alla pagina successiva.

9. In **7 Riepilogo** verificare le informazioni immesse in precedenza. Selezionare quindi **Crea**.

     ![HDInsight crea Riepilogo cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "Specificare il numero di nodi del cluster")
    
    > [!NOTE]  
    > La creazione del cluster richiede tempo, in genere circa 20 minuti. Monitorare l'area **Notifiche** per controllare il processo di provisioning.

10. Dopo aver completato il processo di creazione, selezionare **Vai alla risorsa** dalla notifica **Distribuzione riuscita**. Nella finestra del cluster sono disponibili le informazioni indicate di seguito.

    ![Panoramica del cluster HDI portale di Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Proprietà del cluster")

    Di seguito sono descritte le icone nella finestra:

    * La scheda **Panoramica** fornisce tutte le informazioni essenziali sul cluster. Ad esempio, il nome, il gruppo di risorse a cui appartiene, il percorso, il sistema operativo e l'URL per il dashboard del cluster.
    * **Dashboard** permette di passare al portale di Ambari associato al cluster.
    * **Secure Shell** offre le informazioni necessarie per accedere al cluster tramite SSH.
    * **Ridimensiona cluster** consente di aumentare il numero di nodi di lavoro associati al cluster.
    * **Elimina** consente di eliminare il cluster HDInsight.

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
* [Usare Pig con Hadoop in HDInsight](hadoop/hdinsight-use-pig.md)
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
* [Apache Spark con BI: eseguire l’analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](spark/apache-spark-use-bi-tools.md)
* [Apache Spark con Machine Learning: usare Spark in HDInsight per stimare i risultati di controllo degli alimenti](spark/apache-spark-machine-learning-mllib-ipython.md)

