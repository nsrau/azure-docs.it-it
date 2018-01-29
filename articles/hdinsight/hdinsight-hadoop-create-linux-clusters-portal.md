---
title: Creare cluster Hadoop con un Web browser - Azure HDInsight | Microsoft Docs
description: Informazioni su come creare cluster Hadoop, HBase, Storm o Spark in Linux per HDInsight usando un browser Web e il portale di anteprima di Azure.
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 697278cf-0032-4f7c-b9b2-a84c4347659e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 309f997174211e1a1a2d0eeea2b9b445c8b37e4d
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Creare cluster basati su Linux in HDInsight tramite il portale di Azure
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Il portale di Azure è uno strumento di gestione basato sul Web per servizi e risorse ospitati nel cloud di Microsoft Azure. In questo articolo viene illustrato come creare cluster HDInsight basati su Linux tramite il portale.

## <a name="prerequisites"></a>prerequisiti
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Un Web browser moderno**. Il portale di Azure usa HTML5 e Javascript e potrebbe non funzionare correttamente nei Web browser meno recenti.

## <a name="create-clusters"></a>Creare i cluster
Il portale di Azure espone la maggior parte delle proprietà del cluster. Con il modello di Azure Resource Manager è possibile nascondere molti dettagli. Per altre informazioni, vedere [Creare cluster Hadoop basati su Linux in HDInsight tramite modelli di Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. Accedere al [Portale di Azure](https://portal.azure.com).
2. Fare clic su **+**, **Intelligence e analisi** e quindi su **HDInsight**.
   
    ![Creazione di un nuovo cluster nel portale di Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster.png "Creazione di un nuovo cluster nel portale di Azure")

3. Nel pannello **HDInsight** fare clic su **Custom (size, settings, apps)** (Personalizza dimensioni, impostazioni,app), scegliere **Nozioni di base**, quindi immettere le informazioni seguenti.

    ![Creazione di un nuovo cluster nel portale di Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-basics.png "Creazione di un nuovo cluster nel portale di Azure")

    * Inserire il **Nome cluster**: il nome deve essere univoco a livello globale.

    * Nell'elenco a discesa **Sottoscrizione** selezionare la sottoscrizione di Azure che viene usata per il cluster.

    * Fare clic su **Tipo di cluster** e quindi selezionare il tipo di cluster (Hadoop, Spark e così via) da creare. Per **Sistema operativo** fare clic su **Linux** e quindi selezionare una versione. Usare la versione predefinita, in caso di dubbi. Per altre informazioni, vedere [Versioni del cluster HDInsight](hdinsight-component-versioning.md).

        Per i tipi di cluster Hadoop, Spark e Interactive Query è anche possibile scegliere di installare il **Pacchetto di sicurezza aziendale**. Il Pacchetto di sicurezza aziendale abilita funzionalità di sicurezza quali l'integrazione con Azure Active Directory e Apache Ranger per i cluster. Per altre informazioni, vedere [Enterprise Security Package in Azure HDInsight](./domain-joined/apache-domain-joined-introduction.md) (Pacchetto di sicurezza aziendale in Azure HDInsight).

        ![Abilitare il Pacchetto di sicurezza aziendale](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-enable-enterprise-security-package.png "Abilitare il Pacchetto di sicurezza aziendale")
     
        > [!IMPORTANT]
        > Sono disponibili molti tipi di cluster HDInsight, che corrispondono al carico di lavoro o alla tecnologia per cui è ottimizzato il cluster. Non è disponibile alcun metodo supportato per creare un cluster che combini più tipi, ad esempio Storm e HBase in un cluster. 
        > 
        > 
        
    * Per **Nome utente dell'account di accesso del cluster** e **Password dell'account di accesso del cluster** immettere il nome utente e la password dell'utente amministratore.

    * Immettere un **Nome utente SSH** e se si desidera che la password SSH sia uguale alla password dell'amministratore specificata in precedenza, selezionare la casella di controllo **Usa la stessa password dell'account di accesso del cluster**. In caso contrario, inserire una **PASSWORD** o una **CHIAVE PUBBLICA** da usare per autenticare l'utente SSH. Si consiglia di utilizzare una chiave pubblica. Fare clic su **Seleziona** nella parte inferiore per salvare la configurazione delle credenziali.
   
    Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    * Per **Gruppo di risorse**, specificare se si desidera creare un nuovo gruppo di risorse o usarne uno esistente.

    * Specificare un **percorso** per il data center in cui viene creato il cluster.

    * Fare clic su **Avanti**.

4. Per **Archiviazione** specificare se si desidera impostare Archiviazione di Azure (WASB) o Data Lake Store come risorsa di archiviazione predefinita. Per altre informazioni, vedere la tabella di seguito.

    ![Creazione di un nuovo cluster nel portale di Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-storage.png "Creazione di un nuovo cluster nel portale di Azure")

    | Archiviazione                                      | DESCRIZIONE |
    |----------------------------------------------|-------------|
    | **BLOB del servizio di archiviazione di Azure come risorsa di archiviazione predefinita**   | <ul><li>Per **Tipo di archiviazione primario** selezionare **Archiviazione di Azure**. Successivamente, per **Metodo di selezione**, è possibile scegliere **Sottoscrizioni personali** se si desidera specificare un account di archiviazione che fa parte della sottoscrizione Azure e quindi selezionare l'account di archiviazione. In caso contrario, fare clic su **Chiave di accesso** e fornire le informazioni relative all'account di archiviazione che si desidera scegliere all'esterno della sottoscrizione Azure.</li><li>Per **Contenitore predefinito**, è possibile scegliere di usare il nome del contenitore predefinito suggerito dal portale oppure specificarne uno personale.</li><li>Se si usa WASB come risorsa di archiviazione predefinita, è possibile (facoltativamente) fare clic su **Account archiviazione aggiuntivi** per specificare gli account di archiviazione aggiuntivi da associare al cluster. Per **Chiavi di archiviazione di Azure** fare clic su **Aggiungi una chiave di archiviazione**. Sarà quindi possibile fornire un account di archiviazione da sottoscrizioni Azure o da altre sottoscrizioni (fornendo la chiave di accesso all'account di archiviazione).</li><li>Se si usa WASB come risorsa di archiviazione predefinita, è possibile (facoltativamente) fare clic su **Accesso a Data Lake Store** per specificare Azure Data Lake Store come risorsa di archiviazione aggiuntiva. Per altre informazioni, vedere [Creare un cluster HDInsight con Data Lake Store tramite il portale di Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).</li></ul> |
    | **Azure Data Lake Store come risorsa di archiviazione predefinita** | Per **Tipo di archiviazione primario** selezionare **Data Lake Store** e quindi fare riferimento all'articolo [Creare un cluster HDInsight con Data Lake Store tramite il portale di Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) per le istruzioni. |
    | **Metastore esterni**                      | È possibile, facoltativamente, specificare un database SQL per salvare i metadati Hive e Oozie associati al cluster. Per **Seleziona un database SQL per Hive**, selezionare un database SQL e quindi specificare il nome utente e la password per il database stesso. Ripetere questi passaggi per i metadati Oozie.<br><br>Alcune considerazioni durante l'utilizzo del database SQL di Azure per metastore. <ul><li>Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Microsoft Azure** fare clic su **Sì** e infine su **Salva**.</li><li>Quando si crea un metastore personalizzato, non utilizzare un nome di database che contiene trattini o segni meno, poiché il processo di creazione del cluster non andrebbe a buon fine.</li></ul> |

    Fare clic su **Avanti**. 

    > [!WARNING]
    > L'uso di un account di archiviazione aggiuntivo in una località diversa rispetto al cluster HDInsight non è supportato.

5. Se lo si desidera, fare clic su **Applicazioni** per installare le applicazioni che funzionano con i cluster HDInsight. Queste applicazioni possono essere sviluppate da Microsoft, da fornitori di software indipendenti (ISV) o dall'utente. Per altre informazioni, vedere [Installare applicazioni HDInsight](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).


6. Fare clic su **Dimensioni del cluster** per visualizzare informazioni sui nodi usati per questo cluster. Impostare il numero di nodi del ruolo di lavoro necessari per il cluster. Viene mostrato anche il costo stimato per l'esecuzione del cluster.
   
    ![Piani tariffari per il nodo](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-nodes.png "Specificare il numero di nodi del cluster")
   
   > [!IMPORTANT]
   > Se si prevedono più di 32 nodi di lavoro, al momento della creazione del cluster o con il ridimensionamento del cluster dopo la creazione, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM.
   > 
   > Per altre informazioni sulle dimensioni di nodo e i costi associati, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   > 
   > 
   
   Fare clic su **Avanti** per salvare la configurazione dei piani tariffari per il nodo.

7. Fare clic su **Impostazioni avanzate** per configurare altre impostazioni facoltative, ad esempio l'utilizzo di **Azioni Script** per personalizzare un cluster per installare i componenti personalizzati o per la partecipazione a una **Rete virtuale**. Per altre informazioni, vedere la tabella di seguito.

    ![Piani tariffari per il nodo](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-advanced.png "Specificare il numero di nodi del cluster")

    | Opzione | DESCRIZIONE |
    |--------|-------------|
    | **Azioni Script** | Se si desidera usare uno script personalizzato per personalizzare un cluster durante la creazione. Per altre informazioni sulle azioni di script, vedere [Personalizzare i cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster-linux.md). |
    | **Rete virtuale** | Selezionare una rete virtuale di Azure e la subnet, se si desidera posizionare il cluster in una rete virtuale. Per informazioni sull'uso di HDInsight con una rete virtuale, inclusi i requisiti di configurazione specifici per la rete virtuale, vedere [Estendere le funzionalità di HDInsight usando Rete virtuale di Azure](hdinsight-extend-hadoop-virtual-network.md). |

    Fare clic su **Avanti**.

8. Per **Riepilogo** verificare le informazioni immesse in precedenza e quindi fare clic su **Crea**.

    ![Piani tariffari per il nodo](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-summary.png "Specificare il numero di nodi del cluster")
    
    > [!NOTE]
    > La creazione del cluster richiede tempo, in genere circa 15 minuti. Usare il riquadro sulla schermata iniziale, o la voce **Notifiche** a sinistra della pagina per controllare il processo di provisioning.
    > 
    > 
12. Al termine della procedura di creazione, fare clic sul riquadro per il cluster dalla Schermata iniziale. Nella finestra del cluster sono disponibili le informazioni indicate di seguito.
    
    ![Interfaccia del cluster](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-completed.png "Proprietà del cluster")
    
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

Se si verificano problemi di creazione dei cluster HDInsight, vedere i [requisiti dei controlli di accesso](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato un cluster HDInsight, usare le informazioni seguenti per acquisire familiarità con il cluster:

### <a name="hadoop-clusters"></a>Cluster Hadoop
* [Usare Hive con HDInsight](hadoop/hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hadoop/hdinsight-use-pig.md)
* [Usare MapReduce con HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Cluster HBase
* [Introduzione a HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Sviluppare applicazioni Java per HBase in HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Cluster Storm
* [Sviluppare topologie Java per Storm in HDInsight](storm/apache-storm-develop-java-topology.md)
* [Usare i componenti di Python in Storm in HDInsight](storm/apache-storm-develop-python-topology.md)
* [Distribuire e monitorare le topologie con Storm in HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Cluster Spark
* [Creare un'applicazione autonoma con Scala](spark/apache-spark-create-standalone-application.md)
* [Eseguire i processi in modalità remota in un cluster Spark utilizzando Livy](spark/apache-spark-livy-rest-interface.md)
* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](spark/apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per stimare i risultati dell'ispezione cibo](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](spark/apache-spark-eventhub-streaming.md)

