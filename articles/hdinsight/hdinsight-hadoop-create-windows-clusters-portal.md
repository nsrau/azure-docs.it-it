<properties
   pageTitle="Creare cluster Hadoop in HDInsight | Microsoft Azure"
   	description="Informazioni su come creare cluster per Azure HDInsight usando il portale di Azure."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/02/2016"
   ms.author="jgao"/>

# Creare cluster Hadoop basati su Windows in HDInsight tramite il portale di Azure

[AZURE.INCLUDE [selettore](../../includes/hdinsight-selector-create-clusters.md)]

Informazioni su come creare un cluster Hadoop in HDInsight tramite il portale di Azure. Il [portale di Microsoft Azure](../azure-portal-overview.md) è una posizione centrale in cui è possibile effettuare il provisioning delle risorse di Azure e gestirle. Il portale di Azure è uno degli strumenti che è possibile usare per creare cluster Hadoop basati su Linux o Windows in HDInsight. Per altri strumenti e funzionalità per la creazione di cluster, fare clic sulla scheda Seleziona nella parte superiore di questa pagina o vedere [Metodi di creazione di cluster](hdinsight-provision-clusters.md#cluster-creation-methods).

##Prerequisiti:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Per poter eseguire le istruzioni descritte nell'articolo è necessario disporre di:

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## Creare i cluster


**Per creare un cluster HDInsight**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **NUOVO**, fare clic su **Analisi di dati** e quindi fare clic su **HDInsight**.

    ![Creazione di un nuovo cluster nel portale di Azure](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Creazione di un nuovo cluster nel portale di Azure")

3. Digitare o selezionare i valori seguenti:

  * **Nome del cluster**: Immettere un nome per il cluster. Un segno di spunta verde verrà visualizzato accanto al nome del cluster, se disponibile.
  * **Tipo di cluster**: selezionare **Hadoop**. Altre opzioni includono **HBase**, **Storm** e **Spark**.
  * **Sistema operativo cluster**: selezionare **Windows**. Per creare un cluster basato su Linux, selezionare **Linux**.
  * **Versione**: vedere [Versioni di HDInsight](hdinsight-component-versioning.md).
  * **Sottoscrizione**: selezionare la sottoscrizione di Azure che verrà usata per creare il cluster.
  * **Gruppo di risorse**: selezionare un gruppo di risorse esistente o crearne uno nuovo. Questa voce sarà impostata su uno dei gruppi di risorse esistenti, se disponibili.
  * **Credenziali**: configurare il nome utente e la password per l'utente di Hadoop (utente HTTP). Se si abilita desktop remoto per il cluster, è necessario configurare il nome utente e la password di desktop remoto nonché la data di scadenza dell'account. Fare clic su **Seleziona** in basso per salvare le modifiche.

	   	![Provide cluster credentials](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")

  * **Origine dati**: creare un nuovo account di archiviazione di Azure o selezionarne uno esistente da usare come file system predefinito per il cluster.

   		![Data source blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")

  		* **Selection Method**: Set this to **From all subscriptions** to enable browsing of storage accounts from all your subscriptions. Set this to **Access Key** if you want to enter the **Storage Name** and **Access Key** of an existing storage account.
  		* **Select storage account / Create New**: Click **Select storage account** to browse and select an existing storage account you want to associate with the cluster. Or, click **Create New** to create a new storage account. Use the field that appears to enter the name of the storage account. A green check will appear if the name is available.
  		* **Choose Default Container**: Use this to enter the name of the default container to use for the cluster. While you can enter any name here, we recommend using the same name as the cluster so that you can easily recognize that the container is used for this specific cluster.
  		* **Location**: The geographic region that the storage account is in, or will be created in. This location will determine the cluster location.  The cluster and its default storage account must co-locate in the same Azure data center.
  	
  * **Piani tariffari per il nodo**: Impostare il numero di nodi del ruolo di lavoro necessari per il cluster. Verrà visualizzato il costo stimato del cluster all'interno del pannello.
  

		![Node pricing tiers blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


  * **Configurazione facoltativa**: per selezionare la versione del cluster, nonché configurare altre impostazioni facoltative, ad esempio l'aggiunta a una **Rete virtuale** o la configurazione di un **Metastore esterno** per contenere i dati per Hive e Oozie, usare azioni di script per personalizzare un cluster per l'installazione di componenti personalizzati, oppure usare altri account di archiviazione con il cluster.

  		* **HDInsight Version**: Select the version you want to use for the cluster. For more information, see [HDInsight cluster versions](hdinsight-component-versioning.md).
  		* **Virtual Network**: Select an Azure virtual network and the subnet if you want to place the cluster into a virtual network.  

			![Virtual network blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

			For information on using HDInsight with a Virtual Network, including specific configuration requirements for the Virtual Network, see [Extend HDInsight capbilities by using an Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).
  

  		
		* **External Metastores**: Specify an Azure SQL database to store Hive and Oozie metadata associated with the cluster.
 
            > [AZURE.NOTE] Metastore configuration is not available for HBase cluster types.

			![Custom metastores blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")


			For **Use an existing SQL DB for Hive** metadata, click **Yes**, select a SQL database, and then provide the username/password for the database. Repeat these steps if you want to **Use an existing SQL DB for Oozie metadata**. Click **Select** till you are back on the **Optional Configuration** blade.


			>[AZURE.NOTE] The Azure SQL database used for the metastore must allow connectivity to other Azure services, including Azure HDInsight. On the Azure SQL database dashboard, on the right side, click the server name. This is the server on which the SQL database instance is running. Once you are on the server view, click **Configure**, and then for **Azure Services**, click **Yes**, and then click **Save**.

            &nbsp;

            > [AZURE.IMPORTANT] When creating a metastore, do not use a database name that contains dashes or hyphens, as this can cause the cluster creation process to fail.
		
  		* **Script Actions** if you want to use a custom script to customize a cluster, as the cluster is being created. For more information about script actions, see [Customize HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster.md). On the Script Actions blade provide the details as shown in the screen capture.
  	

			![Script action blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


    	* **Azure Storage Keys**: Specify additional storage accounts to associate with the cluster. In the **Azure Storage Keys** blade, click **Add a storage key**, and then select an existing storage account or create a new account.
    

			![Additional storage blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


4. Fare clic su **Create**. Se si seleziona **Aggiungere a schermata iniziale**, verrà aggiunto un riquadro per il cluster alla schermata iniziale del portale. L'icona indica che il cluster è in fase di creazione e verrà visualizzata l'icona di HDInsight al completamento dell'operazione.
	
    La creazione del cluster richiederà del tempo, in genere circa 15 minuti. Usare il riquadro sulla schermata iniziale, o la voce **Notifiche** a sinistra della pagina per controllare il processo di provisioning.
	

5. Al termine della creazione, fare clic sul riquadro per il cluster dalla Schermata iniziale per avviare il pannello del cluster. Il pannello del cluster fornisce informazioni essenziali sui cluster, ad esempio nome, gruppo di risorse che a cui appartiene, percorso, sistema operativo, URL per il dashboard del cluster e così via.


	![Pannello del cluster](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "Proprietà del cluster")


	Usare le informazioni seguenti per comprendere le icone nella parte superiore di questo pannello e nella sezione **Informazioni di base**:


	* **Impostazioni** e **Tutte le impostazioni**: consentono di visualizzare il pannello**Impostazioni** per il cluster, che consente di accedere a informazioni dettagliate sulla configurazione del cluster.
	* **Dashboard**, **Dashboard del cluster** e **URL**: sono tutti modi per accedere al dashboard del cluster, ovvero un portale Web per eseguire processi nel cluster.
	* **Desktop remoto**: consente di attivare o disattivare il desktop remoto nei nodi del cluster.
	* **Scala Cluster**: consente di modificare il numero di nodi del ruolo di lavoro per questo cluster.
	* **Elimina**: consente di eliminare il cluster HDInsight.
	* **Guide rapide** (![icona cloud e thunderbolt = guida rapida](./media/hdinsight-provision-clusters/quickstart.png)): Visualizza le informazioni che consentiranno di iniziare a usare HDInsight.
	* **Utenti** (![icona utenti](./media/hdinsight-provision-clusters/users.png)): consente di impostare le autorizzazioni per _Gestione portale_ di questo cluster per altri utenti nella sottoscrizione Azure.
	

		> [AZURE.IMPORTANT] Questa impostazione influisce _solo_ sull'accesso e sulle autorizzazioni per questo cluster nel portale, e non ha alcun effetto su chi può connettersi o inviare processi al cluster HDInsight.
		
	* **Tag** (![icona tag](./media/hdinsight-provision-clusters/tags.png)): consente di impostare coppie chiave/valore per definire una tassonomia dei servizi cloud personalizzata. Ad esempio, è possibile creare una chiave denominata __progetto__ e usare un valore comune per tutti i servizi associati a un progetto specifico.

##Personalizzare i cluster

- Vedere [Personalizzare cluster HDInsight tramite Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
- Vedere l'articolo su come [personalizzare cluster HDInsight basati su Windows tramite un'azione script](hdinsight-hadoop-customize-cluster.md).

##Passaggi successivi
Questo articolo ha spiegato vari modi per creare un cluster HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Introduzione all'uso di Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - Informazioni su come iniziare a usare il cluster HDInsight
* [Inviare processi Hadoop a livello di codice](hdinsight-submit-hadoop-jobs-programmatically.md) - Informazioni su come inviare processi a HDInsight a livello di codice
* [Gestire cluster Hadoop in HDInsight tramite il portale di Azure](hdinsight-administer-use-management-portal.md)

<!---HONumber=AcomDC_0907_2016-->