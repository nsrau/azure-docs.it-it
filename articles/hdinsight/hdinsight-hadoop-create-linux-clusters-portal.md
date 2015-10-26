<properties
   	pageTitle="Creare cluster Hadoop, HBase o Storm in Linux in HDInsight tramite il portale | Microsoft Azure"
   	description="Informazioni su come creare cluster Hadoop, HBase o Storm in Linux per HDInsight usando un Web browser e il portale di anteprima di Azure."
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
   	ms.date="10/14/2015"
   	ms.author="nitinme"/>


#Creare cluster basati su Linux in HDInsight tramite il portale di anteprima di Azure

[AZURE.INCLUDE [selettore](../../includes/hdinsight-create-linux-cluster-selector.md)]

Il portale di anteprima di Azure è uno strumento di gestione basato sul Web per servizi e risorse ospitati nel cloud di Microsoft Azure. Usare le informazioni in questo documento per creare un nuovo cluster HDInsight basato su Linux tramite il portale di anteprima e il Web browser.

##Prerequisiti

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Un Web browser moderno__. Il portale di anteprima di Azure usa HTML5 e Javascript e potrebbe non funzionare correttamente nei browser meno recenti.

##Creare un cluster

1. Accedere al [portale di anteprima di Azure](https://portal.azure.com).

2. Fare clic su **NUOVO**, fare clic su **Analisi di dati** e quindi fare clic su **HDInsight**.

    ![Creazione di un nuovo cluster nel portale di anteprima di Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.1.png "Creazione di un nuovo cluster nel portale di anteprima di Azure")

3. Immettere un valore in **Nome cluster**, selezionare **Hadoop** per **Tipo di cluster** e dall'elenco a discesa **Sistema operativo cluster** selezionare **Ubuntu**. Un segno di spunta verde verrà visualizzato accanto al nome del cluster, se disponibile.

    > [AZURE.NOTE]Per eseguire il provisioning di cluster HBase o Storm, selezionare il valore appropriato nell'elenco a discesa **Tipo di cluster**.

    ![Immettere il nome del cluster e il tipo](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.2.png "Immettere il nome del cluster e il tipo")

4. Se si dispone di più di una sottoscrizione, fare clic sulla voce **Sottoscrizione** per selezionare la sottoscrizione di Azure che verrà usata per il cluster.

5. Fare clic su **Gruppo di risorse** per visualizzare un elenco di gruppi di risorse esistenti e quindi selezionare quello in cui creare il cluster. In alternativa, è possibile fare clic su **Crea nuovo** e quindi immettere il nome del nuovo gruppo di risorse. Verrà visualizzato un segno di spunta verde per indicare se il nome del nuovo gruppo è disponibile.

	> [AZURE.NOTE]Questa voce sarà impostata su uno dei gruppi di risorse esistenti, se disponibili.

6. Fare clic su **Credenziali** e quindi immettere una password per l'utente amministratore. È anche necessario immettere un valore nel campo **Nome utente SSH** e un valore in **PASSWORD** o **CHIAVE PUBBLICA**, che verranno usati per autenticare l'utente SSH. Si consiglia di utilizzare una chiave pubblica. Fare clic su **Seleziona** nella parte inferiore per salvare la configurazione delle credenziali.

	![Fornire le credenziali del cluster](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.3.png "Fornire le credenziali del cluster")

	Per altre informazioni sull'uso di SSH con HDInsight, vedere gli articoli seguenti:

	* [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Fare clic su **Origine dati** per scegliere un'origine dati esistente per il cluster o crearne una nuova.

	![Pannello di origine dati](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.4.png "Fornire la configurazione origine dati")

	Attualmente è possibile selezionare un account di archiviazione di Azure come origine dati per un cluster HDInsight. Usare le informazioni seguenti per comprendere le voci nel pannello **Origine dati**.

	- **Metodo di selezione**: impostare questa proprietà su **Da tutte le sottoscrizioni** per consentire l'esplorazione di account di archiviazione da tutte le sottoscrizioni. Impostare questa proprietà su **Chiave di accesso** se si desidera immettere un valore nei campi **Nome di archiviazione** e **Chiave di accesso** per un account di archiviazione esistente.

	- **Seleziona account di archiviazione / Crea nuovo**: fare clic su **Seleziona account di archiviazione** per cercare e selezionare un account di archiviazione esistente da associare al cluster. Fare clic su **Crea nuovo** per creare un nuovo account di archiviazione. Utilizzare il campo che viene visualizzato per immettere il nome dell'account di archiviazione. Se il nome è disponibile, verrà visualizzato un segno di spunta verde.

	- **Scegli contenitore predefinito**: usare questa opzione per immettere il nome del contenitore predefinito da usare per il cluster. È possibile immettere qualsiasi nome, è consigliabile utilizzare lo stesso nome del cluster in modo che sia facilmente intuibile che il contenitore viene utilizzato per tale cluster specifico.

	- **Percorso**: l'area geografica dove si trova o dove verrà creato l'account di archiviazione.

		> [AZURE.IMPORTANT]La selezione del percorso per l'origine dati predefinito imposterà anche il percorso del cluster HDInsight. L'origine dati del cluster e l’origine dati predefinita devono trovarsi nella stessa area.

	Fare clic su **Seleziona** per salvare la configurazione dell'origine dati.

8. Fare clic su **Piani tariffari per il nodo** per visualizzare informazioni sui nodi che verranno creati per questo cluster. Impostare il numero di nodi del ruolo di lavoro necessari per il cluster. Verrà visualizzato il costo stimato del cluster all'interno del pannello.

	![Pannello livelli dei prezzi di nodo](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.5.png "Specificare il numero di nodi del cluster")

	Fare clic su **Seleziona** per salvare la configurazione del piano tariffario per il nodo.

9. Fare clic su **Configurazione facoltativa** per selezionare la versione del cluster, nonché configurare altre impostazioni facoltative, ad esempio l'aggiunta a una **rete virtuale**, l’impostazione di un **metastore esterno** per contenere i dati per Hive e Oozie, usare azioni di Script per personalizzare un cluster per installare i componenti personalizzati o usare ulteriori account di archiviazione con il cluster.

	* Fare clic sull’elenco a discesa **Versione HDInsight** e selezionare la versione che si desidera usare per il cluster. Per altre informazioni, vedere [Versioni del cluster HDInsight](hdinsight-component-versioning.md).

	* **Rete virtuale**: selezionare una rete virtuale di Azure e la subnet se si desidera posizionare il cluster in una rete virtuale.

		![Pannello della rete virtuale](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.6.png "Specificare i dettagli della rete virtuale")

    	>[AZURE.NOTE]Il cluster HDInsight basato su Windows può essere inserito solo in una rete virtuale classica.

	* Fare clic su **Metastore esterni** per specificare il database SQL che si desidera usare per salvare i metadati Hive e Oozie associati al cluster.

		![Pannello metastore personalizzati](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.7.png "Specificare metastore esterni")

		Per i metadati **Usare un database SQL esistente per Hive** fare clic su **Sì**, selezionare un database SQL e quindi specificare il nome utente e la password per il database. Ripetere questi passaggi se si desidera **usare un database SQL esistente per i metadati Oozie**. Fare clic su **Seleziona** fino a quando non si visualizza di nuovo il pannello **Configurazione facoltativa**.

		>[AZURE.NOTE]Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Windows Azure** fare clic su **Sì** e infine su **Salva**.

	* **Azioni script**: se si desidera usare uno script personalizzato per personalizzare un cluster durante la creazione. Per altre informazioni sulle azioni di script, vedere [Personalizzare i cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster-linux.md). Nel pannello Azioni di Script fornire i dettagli, come illustrato nella schermata.

		![Pannello azione di script](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.8.png "Specificare l'azione di script")

	* Fare clic su **Chiavi di archiviazione di Azure** per specificare gli account di archiviazione aggiuntivi da associare al cluster. Nel pannello **Chiavi di archiviazione di Azure** fare clic su **Aggiungi una chiave di archiviazione** e quindi selezionare un account di archiviazione esistente o creare un nuovo account.

		![Pannello risorse di archiviazione aggiuntive](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.9.png "Specificare gli account di archiviazione aggiuntivi")

		Fare clic su **Seleziona** fino a quando non verrà visualizzato il pannello **Nuovo cluster HDInsight**.

10. Nel pannello **Nuovo cluster HDInsight** assicurarsi che **Aggiungi alla schermata iniziale** sia selezionato e quindi fare clic su **Crea**. Questo creerà il cluster e aggiungerà una sezione apposita nella schermata iniziale del portale di Azure. L'icona indica che il cluster sta eseguendo il provisioning e verrà visualizzata l'icona di HDInsight, una volta completato il provisioning.

	| Durante il provisioning | Provisioning completato |
	| ------------------ | --------------------- |
	| ![Indicatore del provisioning nella Schermata iniziale](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioning.png) | ![Sezione del cluster su cui è stato effettuato il provisioning](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioned.png) |

	> [AZURE.NOTE]La creazione del cluster richiederà del tempo, in genere circa 15 minuti. Usare il riquadro sulla schermata iniziale o la voce **Notifiche** a sinistra della pagina per controllare il processo di provisioning.

11. Al termine del provisioning, fare clic sul riquadro per il cluster dalla schermata iniziale per avviare il pannello del cluster. Il pannello del cluster fornisce informazioni essenziali sui cluster, ad esempio nome, gruppo di risorse che a cui appartiene, percorso, sistema operativo, URL per il dashboard del cluster e così via.

	![Pannello del cluster](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.Cluster.Blade.png "Proprietà del cluster")

	Usare le informazioni seguenti per comprendere le icone nella parte superiore di questo pannello e nella sezione **Essentials**:

	* **Impostazioni** e **Tutte le impostazioni**: consentono di visualizzare il pannello **Impostazioni** per il cluster, che consente di accedere a informazioni dettagliate sulla configurazione del cluster.

	* **Dashboard**, **Dashboard del cluster** e **URL**: sono tutti modi per accedere al dashboard del cluster, ovvero un portale Web per eseguire processi nel cluster.

	* **Secure Shell**: informazioni necessarie per accedere al cluster tramite SSH.

	* **Elimina**: consente di eliminare il cluster HDInsight.

	* **Guide rapide** (![icona cloud e thunderbolt = guida rapida](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)): visualizza le informazioni che consentiranno di iniziare a usare HDInsight.

	* **Utenti** (![icona utenti](./media/hdinsight-hadoop-create-linux-cluster-portal/users.png)): consente di impostare le autorizzazioni per _Gestione portale_ di questo cluster per altri utenti nella sottoscrizione Azure.

		> [AZURE.IMPORTANT]Ciò influisce _solo_ sull'accesso e sulle autorizzazioni per tale cluster nel portale di anteprima di Azure e non ha alcun effetto su chi può connettersi o inviare processi al cluster HDInsight.

	* **Tag** (![icona tag](./media/hdinsight-hadoop-create-linux-cluster-portal/tags.png)): consente di impostare coppie chiave/valore per definire una tassonomia dei servizi cloud personalizzata. Ad esempio, è possibile creare una chiave denominata __progetto__ e quindi usare un valore comune per tutti i servizi associati a un progetto specifico.

##Passaggi successivi

Dopo aver creato un cluster HDInsight, usare le informazioni seguenti per acquisire familiarità con il cluster:

###Cluster Hadoop

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md)

###Cluster HBase

* [Introduzione a HBase in HDInsight](hdinsight-hbase-tutorial-get-stared-linux.md)
* [Sviluppare applicazioni Java per HBase in HDInsight](hdinsight-hbase-build-java-maven-linux)

###Cluster Storm

* [Sviluppare topologie Java per Storm in HDInsight](hdinsight-storm-develop-java-topology.md)
* [Usare i componenti di Python in Storm in HDInsight](hdinsight-storm-develop-python.md)
* [Distribuire e monitorare le topologie con Storm in HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=Oct15_HO3-->