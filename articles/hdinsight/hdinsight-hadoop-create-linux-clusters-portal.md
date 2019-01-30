---
title: Creare i cluster Apache Hadoop con un Web browser - Azure HDInsight
description: Informazioni su come creare cluster Apache Hadoop, Apache HBase, Apache Storm, o Apache Spark in Linux per HDInsight usando un Web browser e il portale di anteprima di Azure.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/28/2018
ms.author: hrasheed
ms.openlocfilehash: ca8b398a796284e34ce4e0b577c335552ca75a98
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974268"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Creare cluster basati su Linux in HDInsight tramite il portale di Azure
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Il portale di Azure è uno strumento di gestione basato sul Web per servizi e risorse ospitati nel cloud di Microsoft Azure. In questo articolo viene illustrato come creare cluster HDInsight basati su Linux tramite il portale.

## <a name="prerequisites"></a>Prerequisiti
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Un Web browser moderno**. Il portale di Azure usa HTML5 e Javascript e potrebbe non funzionare correttamente nei Web browser meno recenti.

## <a name="create-clusters"></a>Creare i cluster
Il portale di Azure espone la maggior parte delle proprietà del cluster. Con i modelli di Azure Resource Manager è possibile nascondere molti dettagli. Per altre informazioni, vedere [Creare cluster Apache Hadoop basati su Linux in HDInsight tramite modelli di Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel menu a sinistra selezionare **+ Crea una risorsa**.

1.  In **Azure Marketplace** selezionare **Analisi**.

1.  In **In primo piano** selezionare **HDInsight**.
   
    ![Creazione di un nuovo cluster nel portale di Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "Creazione di un nuovo cluster nel portale di Azure")

1. Nella pagina **HDInsight** selezionare **Personalizzate (dimensioni, impostazioni, app)**.

1. Selezionare **1 Informazioni di base** e quindi immettere le informazioni seguenti:

    ![Creazione di un nuovo cluster nel portale di Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "Creazione di un nuovo cluster nel portale di Azure")

    * Immettere il **nome del cluster**: Il nome deve essere univoco a livello globale.

    * Nell'elenco a discesa **Sottoscrizione** selezionare la sottoscrizione di Azure che viene usata per il cluster.

    * Selezionare **Tipo di cluster** e quindi scegliere il tipo di cluster (Hadoop, Spark e così via) da creare. Il **sistema operativo** sarà **Linux**. Selezionare quindi una versione del tipo di cluster. Usare la versione predefinita, in caso di dubbi. Per altre informazioni, vedere [Versioni del cluster HDInsight](hdinsight-component-versioning.md).
     
        > [!IMPORTANT]  
        > Sono disponibili molti tipi di cluster HDInsight, che corrispondono al carico di lavoro o alla tecnologia per cui è ottimizzato il cluster. Non è disponibile alcun metodo supportato per creare un cluster che combini più tipi, ad esempio Storm e HBase in un cluster.
        
    * Per **Nome utente dell'account di accesso del cluster** e **Password dell'account di accesso del cluster** immettere il nome utente e la password dell'utente amministratore.

    * Immettere un **Nome utente SSH** e se si desidera che la password SSH sia uguale alla password dell'amministratore specificata in precedenza, selezionare la casella di controllo **Usa la stessa password dell'account di accesso del cluster**. In caso contrario, inserire una **PASSWORD** o una **CHIAVE PUBBLICA** da usare per autenticare l'utente SSH. Si consiglia di utilizzare una chiave pubblica. Fare clic su **Seleziona** nella parte inferiore per salvare la configurazione delle credenziali.
   
        Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    * Per **Gruppo di risorse**, specificare se si desidera creare un nuovo gruppo di risorse o usarne uno esistente.

    * Specificare un **percorso** per il data center in cui viene creato il cluster.

    * Selezionare **Avanti** per passare alla pagina successiva.

4. In **2 Sicurezza + rete** è possibile connettere il cluster a una rete virtuale usando l'elenco a discesa fornito. Selezionare una rete virtuale di Azure e la subnet, se si desidera posizionare il cluster in una rete virtuale. Per informazioni sull'uso di HDInsight con una rete virtuale, inclusi i requisiti di configurazione specifici per la rete virtuale, vedere [Estendere le funzionalità di HDInsight usando Rete virtuale di Azure](hdinsight-extend-hadoop-virtual-network.md). Se si desidera usare **Enterprise Security Package**, è anche possibile seguire queste istruzioni: [Configurare un cluster HDInsight con Enterprise Security Package usando Azure Active Directory Domain Services](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    Selezionare **Avanti** per passare alla pagina successiva.


5. In **3 Archiviazione** specificare se si vuole impostare Archiviazione di Azure (WASB) o Data Lake Storage come risorsa di archiviazione predefinita. Per altre informazioni, vedere la tabella di seguito.

     ![Creazione di un nuovo cluster nel portale di Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "Creazione di un nuovo cluster nel portale di Azure")

     | Archiviazione                                      | DESCRIZIONE |
     |----------------------------------------------|-------------|
     | **BLOB del servizio di archiviazione di Azure come risorsa di archiviazione predefinita**   | <ul><li>Per **Tipo di archiviazione primario** selezionare **Archiviazione di Azure**. Successivamente, per **Metodo di selezione**, è possibile scegliere **Sottoscrizioni personali** se si desidera specificare un account di archiviazione che fa parte della sottoscrizione Azure e quindi selezionare l'account di archiviazione. In caso contrario, fare clic su **Chiave di accesso** e fornire le informazioni relative all'account di archiviazione che si desidera scegliere all'esterno della sottoscrizione Azure.</li><li>Per **Contenitore predefinito**, è possibile scegliere di usare il nome del contenitore predefinito suggerito dal portale oppure specificarne uno personale.</li><li>Se si usa WASB come risorsa di archiviazione predefinita, è possibile (facoltativamente) fare clic su **Account archiviazione aggiuntivi** per specificare gli account di archiviazione aggiuntivi da associare al cluster. Per **Chiavi di archiviazione di Azure** fare clic su **Aggiungi una chiave di archiviazione**. Sarà quindi possibile fornire un account di archiviazione da sottoscrizioni Azure o da altre sottoscrizioni (fornendo la chiave di accesso all'account di archiviazione).</li><li>Se si usa WASB come risorsa di archiviazione predefinita, è possibile (facoltativamente) fare clic su **Accesso a Data Lake Storage** per specificare Azure Data Lake Storage come risorsa di archiviazione aggiuntiva. Per altre informazioni, vedere [Avvio rapido: impostazione dei cluster in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
     | **Azure Data Lake Store come risorsa di archiviazione predefinita** | Per **Tipo di archiviazione primario** selezionare **Azure Data Lake Storage Gen1** o **Azure Data Lake Storage Gen2** e quindi fare riferimento all'articolo [Avvio rapido: impostazione dei cluster in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) per le istruzioni. |
     | **Metastore esterni**                      | È possibile, facoltativamente, specificare un database SQL per salvare i metadati Hive e Oozie associati al cluster. Per **Seleziona un database SQL per Hive**, selezionare un database SQL e quindi specificare il nome utente e la password per il database stesso. Ripetere questi passaggi per i metadati Oozie.<br><br>Alcune considerazioni durante l'utilizzo del database SQL di Azure per metastore. <ul><li>Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Microsoft Azure** fare clic su **Sì** e infine su **Salva**.</li><li>Quando si crea un metastore personalizzato, non utilizzare un nome di database che contiene trattini o segni meno, poiché il processo di creazione del cluster non andrebbe a buon fine.</li></ul> |

     > [!WARNING]  
     > L'uso di un account di archiviazione aggiuntivo in una località diversa rispetto al cluster HDInsight non è supportato.

     Selezionare **Avanti** per passare alla pagina successiva.


6. In **4 Applicazioni (facoltativo)** selezionare qualsiasi applicazione desiderata.  Queste applicazioni possono essere sviluppate da Microsoft, da fornitori di software indipendenti (ISV) o dall'utente. Per altre informazioni, vedere [Installare applicazioni HDInsight](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).

    Selezionare **Avanti** per passare alla pagina successiva.


6. In **5 Dimensione del cluster** vengono visualizzate le informazioni sui nodi usati per questo cluster. Impostare il numero di nodi del ruolo di lavoro necessari per il cluster. Viene mostrato anche il costo stimato per l'esecuzione del cluster.
   
    ![Piani tariffari per il nodo](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "Specificare il numero di nodi del cluster")
   
   > [!IMPORTANT]  
   > Se si prevedono più di 32 nodi di lavoro, al momento della creazione del cluster o con il ridimensionamento del cluster dopo la creazione, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM.
   > 
   > Per altre informazioni sulle dimensioni di nodo e i costi associati, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   
    Selezionare **Avanti** per passare alla pagina successiva.

8. In **6 Azioni script** è possibile personalizzare un cluster per installare i componenti personalizzati.  Se si desidera usare uno script personalizzato per personalizzare un cluster durante la creazione. Per altre informazioni sulle azioni di script, vedere [Personalizzare i cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster-linux.md).

   Selezionare **Avanti** per passare alla pagina successiva.

9. In **7 Riepilogo** verificare le informazioni immesse in precedenza e quindi selezionare **Crea**.

     ![Piani tariffari per il nodo](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "Specificare il numero di nodi del cluster")
    
    > [!NOTE]  
    > La creazione del cluster richiede tempo, in genere circa 20 minuti. Monitorare l'area **Notifiche** per controllare il processo di provisioning.

10. Dopo aver completato il processo di creazione, selezionare **Vai alla risorsa** dalla notifica **Distribuzione riuscita**. Nella finestra del cluster sono disponibili le informazioni indicate di seguito.
    
    ![Interfaccia del cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Proprietà del cluster")
    
    Usare le informazioni seguenti per comprendere le icone disponibili nella parte superiore.
    
    * Il pannello **Panoramica** fornisce tutte le informazioni essenziali sul cluster, ad esempio nome, gruppo di risorse a cui appartiene, percorso, sistema operativo, URL per il dashboard del cluster e così via.
    * **Dashboard** permette di passare al portale di Ambari associato al cluster.
    * **Secure Shell**: informazioni necessarie per accedere al cluster tramite SSH.
    * **Ridimensiona cluster** consente di aumentare il numero di nodi di lavoro associati al cluster.
      * **Elimina**: consente di eliminare il cluster HDInsight.
    

## <a name="customize-clusters"></a>Personalizzare i cluster
* Vedere [Personalizzare cluster HDInsight tramite Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
* Vedere [Personalizzare cluster HDInsight usando l'azione script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Eliminare il cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Risolvere problemi

Se si verificano problemi di creazione dei cluster HDInsight, vedere i [requisiti dei controlli di accesso](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato un cluster HDInsight, usare le informazioni seguenti per acquisire familiarità con il cluster:

### <a name="apache-hadoop-clusters"></a>Cluster Apache Hadoop
* [Usare Apache Hive con HDInsight](hadoop/hdinsight-use-hive.md)
* [Usare Apache Pig con HDInsight](hadoop/hdinsight-use-pig.md)
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
* [Apache Spark con BI: Eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](spark/apache-spark-use-bi-tools.md)
* [Apache Spark con Machine Learning: usare Spark in HDInsight per stimare i risultati di controllo degli alimenti](spark/apache-spark-machine-learning-mllib-ipython.md)

