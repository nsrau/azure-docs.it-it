---
title: Creare cluster di Apache Hadoop usando il Web browser, Azure HDInsight
description: Informazioni su come creare cluster Apache Hadoop, Apache HBase, Apache Storm o Apache Spark in Linux per HDInsight usando un Web browser e la portale di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/19/2020
ms.openlocfilehash: bcc71b7244ba3498b2fcde023d372e67f41d6117
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623218"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Creare cluster basati su Linux in HDInsight tramite il portale di Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Il portale di Azure è uno strumento di gestione basato sul Web per servizi e risorse ospitati nel cloud di Microsoft Azure. In questo articolo viene illustrato come creare cluster Azure HDInsight basati su Linux tramite il portale. Ulteriori dettagli sono disponibili nella pagina relativa alla [creazione di cluster HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Il portale di Azure espone la maggior parte delle proprietà del cluster. Con i modelli di Azure Resource Manager è possibile nascondere molti dettagli. Per altre informazioni, vedere [Creare cluster Apache Hadoop in HDInsight mediante modelli di Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-clusters"></a>Creare i cluster

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Dal menu in alto selezionare **+ Crea una risorsa**.

    ![Creare un nuovo cluster nel portale di Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png "Creazione di un nuovo cluster nel portale di Azure")

1. Selezionare **Analytics** > **Azure HDInsight** per passare alla pagina **Crea cluster HDInsight** .

## <a name="basics"></a>Nozioni di base

![HDInsight creare le nozioni di base del cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png "Creazione di un nuovo cluster nel portale di Azure")

Dalla scheda informazioni di **base** specificare le informazioni seguenti:

|Proprietà |Descrizione |
|---|---|
|Subscription|Dall'elenco a discesa selezionare la sottoscrizione di Azure usata per il cluster.|
|Gruppo di risorse|Dall'elenco a discesa selezionare il gruppo di risorse esistente oppure selezionare **Crea nuovo**.|
|Nome cluster|Immettere un nome univoco globale.|
|Region|Nell'elenco a discesa selezionare un'area in cui viene creato il cluster.|
|Tipo di cluster|Selezionare **Seleziona tipo di cluster** per aprire un elenco. Nell'elenco selezionare il tipo di cluster desiderato. Sono disponibili svariati tipi di cluster HDInsight, che corrispondono al carico di lavoro o alla tecnologia per cui è ottimizzato il cluster. Non è disponibile alcun metodo supportato per creare un cluster che combini più tipi,|
|Versione|Selezionare una **versione**dall'elenco a discesa. Usare la versione predefinita, in caso di dubbi. Per altre informazioni, vedere [Versioni del cluster HDInsight](hdinsight-component-versioning.md).|
|Nome utente dell'account di accesso del cluster|Specificare il nome utente. il valore predefinito è **admin**.|
|Password di accesso al cluster|Specificare la password.|
|Conferma password di accesso al cluster|Immettere nuovamente la password|
|Nome utente Secure Shell (SSH)|Specificare il nome utente, il valore predefinito è **sshuser**|
|Usare la password di accesso del cluster per SSH|Se si vuole usare la stessa password SSH della password di amministratore specificata in precedenza, selezionare la casella **di controllo Usa password di accesso al cluster per SSH** . In caso contrario, specificare una **PASSWORD** o una **CHIAVE PUBBLICA** per autenticare l'utente SSH. Una chiave pubblica è l'approccio consigliabile. Scegliere **Seleziona** nella parte inferiore per salvare la configurazione delle credenziali.  Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|

Selezionare **Next: Storage > >** per passare alla scheda successiva.

## <a name="storage"></a>Archiviazione

![HDInsight creare l'archiviazione cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png "Creazione di un nuovo cluster nell'archivio di portale di Azure")

### <a name="primary-storage"></a>Archiviazione primaria

Dall'elenco a discesa per tipo di **archiviazione primario**selezionare il tipo di archiviazione predefinito. I campi successivi da completare variano in base alla selezione effettuata. Per **archiviazione di Azure**:

1. Per **metodo di selezione**scegliere **Seleziona dall'elenco**oppure usare la **chiave di accesso**.
    * Per **selezionare un elenco**, selezionare l' **account di archiviazione primario** dall'elenco a discesa oppure selezionare **Crea nuovo**.
    * Per **usare la chiave di accesso**immettere il **nome dell'account di archiviazione**. Fornire quindi la **chiave di accesso**.

1. Per **contenitore**accettare il valore predefinito o immetterne uno nuovo.

### <a name="additional-azure-storage"></a>Archiviazione di Azure aggiuntiva

Facoltativo: selezionare **Aggiungi archiviazione di Azure** per l'archiviazione cluster aggiuntiva. L'uso di un account di archiviazione aggiuntivo in un'area diversa rispetto al cluster HDInsight non è supportato.

### <a name="metastore-settings"></a>Impostazioni Metastore

Facoltativo: specificare un database SQL esistente per salvare i metadati Apache Hive, Apache oozie e/o Apache Ambari all'esterno del cluster. Il database SQL di Azure usato per il Metastore deve consentire la connettività ad altri servizi di Azure, tra cui Azure HDInsight. Quando si crea un metastore, non usare trattini o segni meno nel nome del database, perché a causa di questi caratteri il processo di creazione del cluster non andrebbe a buon fine.

Selezionare **Avanti: sicurezza e rete > >** passare alla scheda successiva.

## <a name="security--networking"></a>Sicurezza e rete

![HDInsight creare reti di sicurezza del cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png "HDInsight creare reti di sicurezza del cluster")

Nella scheda **sicurezza e rete** specificare le informazioni seguenti:

|Proprietà |Descrizione |
|---|---|
|Pacchetto di sicurezza aziendale|Facoltativo: selezionare la casella di controllo per usare **Enterprise Security Package**. Per altre informazioni, vedere [configurare un cluster HDInsight con Enterprise Security Package usando Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).|
|TLS|Facoltativo: selezionare una versione di TLS dall'elenco a discesa. Per ulteriori informazioni, vedere [Transport Layer Security](./hdinsight-plan-virtual-network-deployment.md#transport-layer-security).|
|Rete virtuale|Facoltativo: selezionare una rete virtuale e una subnet esistenti nell'elenco a discesa. Per informazioni, vedere [pianificare una distribuzione di rete virtuale per i cluster HDInsight di Azure](hdinsight-plan-virtual-network-deployment.md). L'articolo include i requisiti di configurazione specifici per la rete virtuale.|
|Impostazioni di crittografia del disco|Facoltativo: selezionare la casella di controllo per utilizzare la crittografia. Per ulteriori informazioni, vedere [crittografia del disco della chiave gestita dal cliente](./disk-encryption.md).|
|Proxy REST Kafka|Questa impostazione è disponibile solo per il tipo di cluster Kafka. Per altre informazioni, vedere [uso di un proxy Rest](./kafka/rest-proxy.md).|
|Identity|Facoltativo: selezionare un'identità del servizio assegnata dall'utente esistente dall'elenco a discesa. Per altre informazioni, vedere [identità gestite in Azure HDInsight](./hdinsight-managed-identities.md).|

Selezionare **Avanti: configurazione + prezzi > >** per passare alla scheda successiva.

## <a name="configuration--pricing"></a>Configurazione e prezzi

![HDInsight creare la configurazione del cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png "Scheda configurazione e prezzi")

Dalla scheda **configurazione e prezzi** specificare le informazioni seguenti:

|Proprietà |Descrizione |
|---|---|
|+ Aggiungi applicazione|Facoltativo: selezionare le applicazioni desiderate. Può trattarsi di applicazioni Microsoft, di fornitori di software indipendenti o sviluppate internamente. Per altre informazioni, vedere [Installare applicazioni durante la creazione del cluster](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).|
|Dimensioni del nodo|Facoltativo: selezionare un nodo di dimensioni diverse.|
|Numero di nodi|Facoltativo: immettere il numero di nodi per il tipo di nodo specificato. Se si prevedono più di 32 nodi di lavoro, selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM. Pianificare i nodi al momento della creazione del cluster o ridimensionando il cluster dopo la creazione.|
|Abilitare il ridimensionamento automatico|Facoltativo: selezionare la casella di controllo per abilitare la funzionalità. Per altre informazioni, vedere [ridimensionare automaticamente i cluster HDInsight di Azure](./hdinsight-autoscale-clusters.md).|
|+ Aggiungi azione script|Facoltativo: questa opzione funziona se si vuole usare uno script personalizzato per personalizzare un cluster, durante la creazione del cluster. Per altre informazioni sulle azioni script, vedere [Personalizzare i cluster HDInsight basati su Linux tramite azioni script](hdinsight-hadoop-customize-cluster-linux.md).|

Selezionare **Verifica + crea > >** per convalidare la configurazione del cluster e passare alla scheda finale.

## <a name="review--create"></a>Rivedi e crea

![HDInsight crea Riepilogo cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png "Specificare il numero di nodi del cluster")

Esaminare le impostazioni. Selezionare **Crea** per creare il cluster.

La creazione del cluster richiede tempo, in genere circa 20 minuti. Monitorare l'area **Notifiche** per controllare il processo di provisioning.

## <a name="post-creation"></a>Post-creazione

Dopo aver completato il processo di creazione, selezionare **Vai alla risorsa** dalla notifica **Distribuzione riuscita**. Nella finestra del cluster sono disponibili le informazioni indicate di seguito.

![Panoramica del cluster HDI portale di Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Proprietà del cluster")

Alcune delle icone della finestra sono illustrate di seguito:

|Proprietà | Descrizione |
|---|---|
|Panoramica|Fornisce tutte le informazioni essenziali sul cluster. Ad esempio, il nome, il gruppo di risorse a cui appartiene, il percorso, il sistema operativo e l'URL per il dashboard del cluster.|
|Dashboard del cluster|Indirizza l'utente al portale di Ambari associato al cluster.|
|Accesso SSH + cluster|Fornisce le informazioni necessarie per accedere al cluster tramite SSH.|
|Delete|consente di eliminare il cluster HDInsight.|

## <a name="customize-clusters"></a>Personalizzare i cluster

* [Personalizzare cluster HDInsight tramite Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Personalizzare i cluster HDInsight basati su Linux tramite azioni script](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Eliminare il cluster

Vedere [eliminare un cluster HDInsight usando il browser, PowerShell o l'interfaccia della](./hdinsight-delete-cluster.md)riga di comando di Azure.

## <a name="troubleshoot"></a>Risoluzione dei problemi

Se si verificano problemi di creazione dei cluster HDInsight, vedere i [requisiti dei controlli di accesso](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

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
* [Apache Spark con Machine Learning: utilizzare Spark in HDInsight per prevedere i risultati di un controllo alimentare](spark/apache-spark-machine-learning-mllib-ipython.md)
