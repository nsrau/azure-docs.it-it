<properties
   	pageTitle="Creare cluster Hadoop, HBase, Storm o Spark in Linux in HDInsight tramite il portale | Microsoft Azure"
   	description="Informazioni su come creare cluster Hadoop, HBase, Storm o Spark in Linux per HDInsight usando un browser Web e il portale di anteprima di Azure."
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="03/21/2016"
   	ms.author="nitinme"/>


#Creare cluster basati su Linux in HDInsight tramite il portale di Azure

[AZURE.INCLUDE [selettore](../../includes/hdinsight-selector-create-clusters.md)]

Il portale di Azure è uno strumento di gestione basato sul Web per servizi e risorse ospitati nel cloud di Microsoft Azure. In questo articolo si apprenderà come creare cluster HDInsight basati su Linux tramite il portale.

## Prerequisiti

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- __Un Web browser moderno__. Il portale di Azure usa HTML5 e Javascript e potrebbe non funzionare correttamente nei Web browser meno recenti.

##Creare i cluster

Il portale di Azure espone la maggior parte delle proprietà del cluster. Con il modello di Azure Resource Manager di Azure è possibile nascondere molti dettagli. Per altre informazioni, vedere [Creare cluster Hadoop basati su Linux in HDInsight tramite modelli di Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Fare clic su **NUOVO**, fare clic su **Analisi di dati** e quindi fare clic su **HDInsight**.

    ![Creazione di un nuovo cluster nel portale di Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.1.png "Creazione di un nuovo cluster nel portale di Azure")

3. Digitare:

    - **Nome cluster**: il nome deve essere globalmente univoco.
    - **Tipo di cluster**: se non si sa quale scegliere, selezionare **Hadoop**.
    - **Sistema operativo cluster**: selezionare **Linux**.
    - **Versione**: usare la versione predefinita, se non si sa quale scegliere. Per altre informazioni, vedere [Versioni del cluster HDInsight](hdinsight-component-versioning.md).

4. Fare clic su **Sottoscrizione** per selezionare la sottoscrizione di Azure che verrà usata per il cluster.

5. Fare clic su **Gruppo di risorse** per selezionare un gruppo di risorse esistente o fare clic su **Nuovo** crearne uno nuovo.

	> [AZURE.NOTE] Questa voce sarà impostata su uno dei gruppi di risorse esistenti, se disponibili.

6. Fare clic su **Credenziali** e quindi immettere una password per l'utente amministratore. È anche necessario immettere un valore nel campo **Nome utente SSH** e un valore in **PASSWORD** o **CHIAVE PUBBLICA**, che verranno usati per autenticare l'utente SSH. Si consiglia di utilizzare una chiave pubblica. Fare clic su **Seleziona** nella parte inferiore per salvare la configurazione delle credenziali.

	![Fornire le credenziali del cluster](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.3.png "Fornire le credenziali del cluster")

	Per altre informazioni sull'uso di SSH con HDInsight, vedere gli articoli seguenti:

	* [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Fare clic su **Origine dati** per scegliere un'origine dati esistente per il cluster o crearne una nuova.

	![Pannello di origine dati](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.4.png "Fornire la configurazione origine dati")

	Attualmente è possibile selezionare un account di archiviazione di Azure come origine dati per un cluster HDInsight. Usare le informazioni seguenti per comprendere le voci nel pannello **Origine dati**.

	- **Metodo di selezione**: impostare questa voce su **Da tutte le sottoscrizioni** per consentire l'esplorazione degli account di archiviazione da tutte le sottoscrizioni. Impostare questa voce su **Chiave di accesso** se si vuole immettere un valore nei campi **Nome archiviazione** e **Chiave di accesso** per un account di archiviazione esistente.

	- **Selezionare l'account di archiviazione/Nuovo**: fare clic su **Selezionare l'account di archiviazione** per cercare e selezionare un account di archiviazione esistente da associare al cluster. In alternativa, fare clic su **Nuovo** per creare un nuovo account di archiviazione. Usare il campo che viene visualizzato per immettere il nome dell'account di archiviazione. Se il nome è disponibile, verrà visualizzato un segno di spunta verde.

	- **Scegli contenitore predefinito**: utilizzare questa opzione per immettere il nome del contenitore predefinito da utilizzare per il cluster. È possibile immettere qualsiasi nome, è consigliabile usare lo stesso nome del cluster in modo che sia facilmente intuibile che il contenitore viene usato per tale cluster specifico.

	- **Percorso**: l'area geografica dove si trova o dove verrà creato l'account di archiviazione.

		> [AZURE.IMPORTANT] La selezione del percorso per l'origine dati predefinito imposterà anche il percorso del cluster HDInsight. L'origine dati del cluster e l'origine dati predefinita devono trovarsi nella stessa area.
        
    - **Identità AAD del cluster**: configurandolo si rende accessibile il cluster per gli archivi Azure Data Lake in base alla configurazione di AAD.

	Fare clic su **Seleziona** per salvare la configurazione dell'origine dati.

8. Fare clic su **Piani tariffari per il nodo** per visualizzare informazioni sui nodi che verranno creati per questo cluster. Impostare il numero di nodi del ruolo di lavoro necessari per il cluster. Verrà visualizzato il costo stimato del cluster all'interno del pannello.

	![Pannello livelli dei prezzi di nodo](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.5.png "Specificare il numero di nodi del cluster")
    
    > [AZURE.IMPORTANT] Se si prevedono più di 32 nodi di lavoro, al momento della creazione del cluster o con il ridimensionamento del cluster dopo la creazione, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM.
    >
    > Per altre informazioni sulle dimensioni di nodo e i costi associati, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

	Fare clic su **Seleziona** per salvare la configurazione del prezzo del nodo.

9. Fare clic su **Configurazione facoltativa** per selezionare la versione del cluster, nonché configurare altre impostazioni facoltative, ad esempio l'aggiunta a una **Rete virtuale**, l’impostazione di un **Metastore esterno** per contenere i dati per Hive e Oozie, utilizzare azioni di Script per personalizzare un cluster per installare i componenti personalizzati o utilizzare ulteriori account di archiviazione con il cluster.

	* **Rete virtuale**: selezionare una rete virtuale di Azure e la subnet, se si desidera posizionare il cluster in una rete virtuale.  

		![Pannello della rete virtuale](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.6.png "Specificare i dettagli della rete virtuale")

    	Per informazioni sull'uso di HDInsight con una rete virtuale, inclusi i requisiti di configurazione specifici per la rete virtuale, vedere [Estendere le funzionalità di HDInsight usando Rete virtuale di Azure](hdinsight-extend-hadoop-virtual-network.md).

	* Fare clic su **Metastore esterni** per specificare il database SQL che si vuole usare per salvare i metadati Hive e Oozie associati al cluster.
    
        > [AZURE.NOTE] La configurazione Metastore non è disponibile per i tipi di cluster HBase.

		![Pannello metastore personalizzati](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.7.png "Specificare metastore esterni")

		Per **Usare un database SQL esistente per i metadati Hive**, fare clic su **Sì**, selezionare un database SQL e quindi specificare il nome utente e la password per il database. Ripetere questa procedura se si desidera **Utilizzare un database SQL esistente per i metadati Oozie**. Fare clic su **Seleziona** fino a quando non si visualizza di nuovo il pannello **Configurazione facoltativa**.

		>[AZURE.NOTE] Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Microsoft Azure** fare clic su **Sì** e infine su **Salva**.

	* **Azioni script**: se si desidera usare uno script personalizzato per personalizzare un cluster durante la creazione. Per altre informazioni sulle azioni di script, vedere [Personalizzare i cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster-linux.md). Nel pannello Azioni di Script fornire i dettagli, come illustrato nella schermata.

		![Pannello azione di script](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.8.png "Specificare l'azione di script")

	* Fare clic su **Account di archiviazione collegati** per specificare gli account di archiviazione aggiuntivi da associare al cluster. Nel pannello **Chiavi di archiviazione di Azure**, fare clic su **Aggiungi una chiave di archiviazione**, quindi selezionare un account di archiviazione esistente o creare un nuovo account.

		![Pannello risorse di archiviazione aggiuntive](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.9.png "Specificare gli account di archiviazione aggiuntivi")

		Fare clic su **Seleziona** fino a quando non verrà visualizzato il pannello **Nuovo cluster HDInsight**.
        
        Oltre a un account di archiviazione BLOB, è anche possibile collegare archivi Azure Data Lake. La configurazione può essere eseguita configurando AAD dall'origine dati in cui è stato configurato l'account di archiviazione predefinito e il contenitore predefinito.

10. Nel pannello **Nuovo cluster HDInsight** assicurarsi che l'opzione **Aggiungi a Schermata iniziale** sia selezionata e quindi fare clic su **Crea**. Questo creerà il cluster e aggiungerà una sezione apposita nella schermata iniziale del portale di Azure. L'icona indica che il cluster sta eseguendo il provisioning e verrà visualizzata l'icona di HDInsight, una volta completato il provisioning.

	| Durante il provisioning | Provisioning completato |
	| ------------------ | --------------------- |
	| ![Indicatore del provisioning nella Schermata iniziale](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioning.png) | ![Sezione del cluster su cui è stato effettuato il provisioning](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioned.png) |

	> [AZURE.NOTE] La creazione del cluster richiederà del tempo, in genere circa 15 minuti. Utilizzare il riquadro sulla schermata iniziale, o la voce **Notifiche** a sinistra della pagina per controllare il processo di provisioning.

11. Al termine del provisioning, fare clic sul riquadro per il cluster dalla schermata iniziale per avviare il pannello del cluster. Il pannello del cluster fornisce informazioni essenziali sui cluster, ad esempio nome, gruppo di risorse che a cui appartiene, percorso, sistema operativo, URL per il dashboard del cluster e così via.

	![Pannello del cluster](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.Cluster.Blade.png "Proprietà del cluster")

	Usare le informazioni seguenti per comprendere le icone nella parte superiore di questo pannello e nella sezione **Informazioni di base**:

	* **Impostazioni** e **Tutte le impostazioni**: consentono di visualizzare il pannello**Impostazioni** per il cluster, che consente di accedere a informazioni dettagliate sulla configurazione del cluster.

	* **Dashboard**, **Dashboard del cluster** e **URL**: sono tutti modi per accedere al dashboard del cluster, ovvero un portale Web per eseguire processi nel cluster.

	* **Secure Shell**: informazioni necessarie per accedere al cluster tramite SSH.

	* **Elimina**: consente di eliminare il cluster HDInsight.

	* **Guide rapide** (![icona cloud e thunderbolt = guida rapida](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)): Visualizza le informazioni che consentiranno di iniziare a usare HDInsight.

	* **Utenti** (![icona utenti](./media/hdinsight-hadoop-create-linux-cluster-portal/users.png)): consente di impostare le autorizzazioni per _Gestione portale_ di questo cluster per altri utenti nella sottoscrizione Azure.

		> [AZURE.IMPORTANT] Questo influisce _solo_ sull'accesso e sulle autorizzazioni per tale cluster nel portale di Azure e non ha alcun effetto su chi può connettersi o inviare processi al cluster HDInsight.

	* **Tag** (![icona tag](./media/hdinsight-hadoop-create-linux-cluster-portal/tags.png)): consente di impostare coppie chiave/valore per definire una tassonomia dei servizi cloud personalizzata. Ad esempio, è possibile creare una chiave denominata __progetto__ e usare un valore comune per tutti i servizi associati a un progetto specifico.

##Personalizzare i cluster

- Vedere [Personalizzare cluster HDInsight tramite Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
- Vedere [Personalizzare cluster HDInsight basati su Windows tramite Azione script](hdinsight-hadoop-customize-cluster.md).

##Eliminazione del cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##Passaggi successivi

Dopo aver creato un cluster HDInsight, usare le informazioni seguenti per acquisire familiarità con il cluster:

###Cluster Hadoop

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md)

###Cluster HBase

* [Introduzione a HBase in HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Sviluppare applicazioni Java per HBase in HDInsight](hdinsight-hbase-build-java-maven-linux)

###Cluster Storm

* [Sviluppare topologie Java per Storm in HDInsight](hdinsight-storm-develop-java-topology.md)
* [Usare i componenti di Python in Storm in HDInsight](hdinsight-storm-develop-python-topology.md)
* [Distribuire e monitorare le topologie con Storm in HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

###Cluster Spark

* [Creare un'applicazione autonoma con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Eseguire i processi in modalità remota in un cluster Spark utilizzando Livy](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per stimare i risultati dell'ispezione cibo](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)

<!---HONumber=AcomDC_0323_2016-->