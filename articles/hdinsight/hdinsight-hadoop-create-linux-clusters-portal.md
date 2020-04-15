---
title: Creare cluster Apache Hadoop usando il Web browser, Azure HDInsightCreate Apache Hadoop clusters using web browser, Azure HDInsight
description: Informazioni su come creare cluster Apache Hadoop, Apache HBase, Apache Storm o Apache Spark su HDInsight. Usare il Web browser e il portale di Azure.Use web browser and the Azure portal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/13/2020
ms.openlocfilehash: a5f6ac76d509a0a63c2d641f91cd91cdb2e0d19d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313816"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Creare cluster basati su Linux in HDInsight tramite il portale di Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Il portale di Azure è uno strumento di gestione basato sul Web per servizi e risorse ospitati nel cloud di Microsoft Azure. In questo articolo viene illustrato come creare cluster Azure HDInsight basati su Linux tramite il portale. Ulteriori dettagli sono disponibili in [Creare cluster HDInsight.](./hdinsight-hadoop-provision-linux-clusters.md)

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Il portale di Azure espone la maggior parte delle proprietà del cluster. Con i modelli di Azure Resource Manager è possibile nascondere molti dettagli. Per altre informazioni, vedere [Creare cluster Apache Hadoop in HDInsight mediante modelli di Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-clusters"></a>Creare i cluster

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Dal menu in alto, selezionare **Crea una risorsa**.

    ![Creare un nuovo cluster nel portale di AzureCreate a new cluster in the Azure portal](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png "Creazione di un nuovo cluster nel portale di Azure")

1. Selezionare Analytics Azure HDInsight per passare alla pagina Crea cluster HDInsight.Select **Analytics** > **Azure HDInsight** to go to the Create **HDInsight cluster page.**

## <a name="basics"></a>Nozioni di base

![Nozioni di base sulla creazione di HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png "Creazione di un nuovo cluster nel portale di Azure")

Nella scheda **Nozioni di base,** fornire le seguenti informazioni:

|Proprietà |Descrizione |
|---|---|
|Subscription|Nell'elenco a discesa selezionare la sottoscrizione di Azure usata per il cluster.|
|Resource group|Nell'elenco a discesa selezionare il gruppo di risorse esistente oppure **selezionare Crea nuovo**.|
|Nome cluster|Immettere un nome univoco globale.|
|Region|Nell'elenco a discesa selezionare un'area in cui viene creato il cluster.|
|Tipo di cluster|Fare clic su **Seleziona tipo di cluster** per aprire un elenco. Nell'elenco selezionare il tipo di cluster desiderato. I cluster HDInsight sono disponibili in tipi diversi. che corrispondono al carico di lavoro o alla tecnologia per cui è ottimizzato il cluster. Non è disponibile alcun metodo supportato per creare un cluster che combini più tipi,|
|Versione|Nell'elenco a discesa selezionare una **versione**. Usare la versione predefinita, in caso di dubbi. Per altre informazioni, vedere [Versioni del cluster HDInsight](hdinsight-component-versioning.md).|
|Nome utente dell'account di accesso del cluster|Specificare il nome utente, il valore predefinito è **admin**.|
|Password di accesso al cluster|Fornire la password.|
|Confermare la password di accesso al cluster|Immettere nuovamente la password|
|Nome utente Secure Shell (SSH)|Fornire il nome utente, il valore predefinito è **sshuser**|
|Usare la password di accesso del cluster per SSH|Se si desidera la stessa password SSH della password di amministratore specificata in precedenza, selezionare la casella di controllo **Usa password di accesso al cluster per SSH.** In caso contrario, specificare una **PASSWORD** o una **CHIAVE PUBBLICA** per autenticare l'utente SSH. Una chiave pubblica è l'approccio consigliabile. Scegliere **Seleziona** nella parte inferiore per salvare la configurazione delle credenziali.  Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|

Selezionare **Avanti: Archiviazione >>** per passare alla scheda successiva.

## <a name="storage"></a>Archiviazione

![HDInsight crea l'archiviazione cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png "Creazione di un nuovo cluster nel portale di Azure - archiviazioneCreating a new cluster in the Azure portal - storage")

### <a name="primary-storage"></a>Archiviazione primaria

Nell'elenco a discesa tipo **di archiviazione primaria**selezionare il tipo di archiviazione predefinito. I campi successivi da completare variano in base alla selezione effettuata. Per **Archiviazione di Azure:**

1. Per **Metodo di selezione**, scegliere Seleziona **dall'elenco**o **Usa tasto**di scelta .
    * Per **Seleziona dall'elenco**, selezionare l'account **di archiviazione primario** dall'elenco a discesa oppure selezionare **Crea nuovo**.
    * Per Usa chiave di **accesso,** immettere il **nome dell'account di archiviazione**. Quindi specificare il **tasto Access**.

1. Per **Contenitore**, accettare il valore predefinito o immetterne uno nuovo.

### <a name="additional-azure-storage"></a>Archiviazione di Azure aggiuntivaAdditional Azure Storage

Facoltativo: selezionare **Aggiungi Archiviazione di Azure** per un'archiviazione cluster aggiuntiva. L'uso di un account di archiviazione aggiuntivo in un'area diversa rispetto al cluster HDInsight non è supportato.

### <a name="metastore-settings"></a>Impostazioni metastore

Facoltativo: specificare un database SQL esistente per salvare i metadati Apache Hive, Apache Oozie e Opache Ambari all'esterno del cluster. The Azure SQL Database that's used for the metastore must allow connectivity to other Azure services, including Azure HDInsight. Quando si crea un metastore, non usare trattini o segni meno nel nome del database, perché a causa di questi caratteri il processo di creazione del cluster non andrebbe a buon fine.

Selezionare **Avanti: Sicurezza e >>di rete** per passare alla scheda successiva.

## <a name="security--networking"></a>Sicurezza e networking

![HDInsight crea una rete di sicurezza clusterHDInsight create cluster security networking](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png "HDInsight crea una rete di sicurezza clusterHDInsight create cluster security networking")

Nella scheda **Sicurezza e rete,** fornire le seguenti informazioni:

|Proprietà |Descrizione |
|---|---|
|Pacchetto di sicurezza aziendale|Facoltativo: selezionare la casella di controllo per utilizzare **Enterprise Security Package**. Per altre informazioni, vedere [Configurare un cluster HDInsight con un pacchetto](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)di sicurezza aziendale tramite Servizi di dominio Azure Active Directory.For more information, see Configure a HDInsight cluster with Enterprise Security Package by using Azure Active Directory Domain Services .|
|TLS|Facoltativo: selezionare una versione TLS dall'elenco a discesa. Per ulteriori informazioni, vedere [Transport Layer Security](./hdinsight-plan-virtual-network-deployment.md#transport-layer-security).|
|Rete virtuale|Facoltativo: selezionare una rete virtuale e una subnet esistenti dall'elenco a discesa. Per informazioni, vedere Pianificare una distribuzione di rete virtuale per i cluster di Azure HDInsight.For information, see Plan a [virtual network deployment for Azure HDInsight clusters.](hdinsight-plan-virtual-network-deployment.md) L'articolo include i requisiti di configurazione specifici per la rete virtuale.|
|Impostazioni di crittografia del disco|Facoltativo: selezionare la casella di controllo per utilizzare la crittografia. Per ulteriori informazioni, vedere Crittografia del [disco di chiave gestita dal cliente](./disk-encryption.md).|
|Proxy REST Kafka|Questa impostazione è disponibile solo per il tipo di cluster Kafka. Per ulteriori informazioni, vedere [Utilizzo di un proxy REST](./kafka/rest-proxy.md).|
|Identità|Facoltativo: selezionare un'identità di servizio assegnata dall'utente esistente dall'elenco a discesa. Per altre informazioni, vedere [Identità gestite in Azure HDInsight.For more information, see Managed identities in Azure HDInsight.](./hdinsight-managed-identities.md)|

Selezionare **Successivo: Configurazione e prezzi >>** per passare alla scheda successiva.

## <a name="configuration--pricing"></a>Configurazione e prezzi

![HDInsight crea la configurazione del cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png "Scheda Configurazione e prezzi")

Nella scheda **Configurazione e prezzi,** fornire le seguenti informazioni:

|Proprietà |Descrizione |
|---|---|
|Aggiungi applicazione|Facoltativo: selezionare le applicazioni desiderate. Può trattarsi di applicazioni Microsoft, di fornitori di software indipendenti o sviluppate internamente. Per altre informazioni, vedere [Installare applicazioni durante la creazione del cluster](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).|
|Dimensioni nodo|Facoltativo: selezionare un nodo di dimensioni diverse.|
|Numero di nodi|Facoltativo: immettere il numero di nodi per il tipo di nodo specificato. Se si prevede di più di 32 nodi di lavoro, selezionare una dimensione del nodo head con almeno otto core e 14 GB di RAM. Pianificare i nodi al momento della creazione del cluster o ridimensionando il cluster dopo la creazione.|
|Abilitare il ridimensionamento automatico|Facoltativo: selezionare la casella di controllo per abilitare la funzione. Per altre informazioni, vedere Ridimensionare automaticamente i cluster di Azure HDInsight.For more information, see [Automatically scale Azure HDInsight clusters](./hdinsight-autoscale-clusters.md).|
|- Aggiungi azione script|Facoltativo: questa opzione funziona se si desidera utilizzare uno script personalizzato per personalizzare un cluster durante la creazione del cluster. Per altre informazioni sulle azioni script, vedere [Personalizzare i cluster HDInsight basati su Linux tramite azioni script](hdinsight-hadoop-customize-cluster-linux.md).|

Selezionare **Revisione: creare >>** per convalidare la configurazione del cluster e passare alla scheda finale.

## <a name="review--create"></a>Rivedi e crea

![HDInsight crea un riepilogo del cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png "Specificare il numero di nodi del clusterSpecify number of cluster nodes")

Rivedere le impostazioni. Selezionare **Crea** per creare il cluster.

La creazione del cluster richiede tempo, in genere circa 20 minuti. Monitorare l'area **Notifiche** per controllare il processo di provisioning.

## <a name="post-creation"></a>Creazione post

Dopo aver completato il processo di creazione, selezionare **Vai alla risorsa** dalla notifica **Distribuzione riuscita**. Nella finestra del cluster sono disponibili le informazioni indicate di seguito.

![Panoramica del cluster del portale di Azure HDIHDI Azure portal cluster overview](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Proprietà del cluster")

Alcune delle icone nella finestra sono spiegate come segue:

|Proprietà | Descrizione |
|---|---|
|Panoramica|Fornisce tutte le informazioni essenziali sul cluster. Ad esempio, il nome, il gruppo di risorse a cui appartiene, il percorso, il sistema operativo e l'URL per il dashboard del cluster.|
|Dashboard del cluster|Consente di accedere al portale Ambari associato al cluster.|
|SSH - Accesso al cluster|Fornisce le informazioni necessarie per accedere al cluster tramite SSH.|
|Delete|consente di eliminare il cluster HDInsight.|

## <a name="customize-clusters"></a>Personalizzare i cluster

* [Personalizzare cluster HDInsight tramite Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Personalizzare i cluster HDInsight basati su Linux tramite azioni script](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Eliminare il cluster

Vedere [Eliminare un cluster HDInsight usando il browser, PowerShell o l'interfaccia della riga di comando di Azure.See Delete an HDInsight cluster using your browser, PowerShell, or the Azure CLI.](./hdinsight-delete-cluster.md)

## <a name="troubleshoot"></a>Risolvere problemi

Se si verificano problemi di creazione dei cluster HDInsight, vedere i [requisiti dei controlli di accesso](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Passaggi successivi

È stato creato un cluster HDInsight. È ora possibile procedere e scoprire come utilizzare il cluster.

* [Usare Apache Hive con HDInsight](hadoop/hdinsight-use-hive.md)
* [Introduzione ad Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Sviluppare topologie Java per Apache Storm in HDInsight](storm/apache-storm-develop-java-topology.md)
* [Creare un'applicazione autonoma con Scala](spark/apache-spark-create-standalone-application.md)
