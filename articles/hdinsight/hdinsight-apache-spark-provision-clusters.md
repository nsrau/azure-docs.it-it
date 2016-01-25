<properties
   pageTitle="Provisioning Apache Spark cluster in HDInsight | Microsoft Azure"
   description="Informazioni su come effettuare il provisioning di cluster Spark per Azure HDInsight tramite il portale di Azure, Azure PowerShell, una riga di comando o HDInsight .NET SDK"
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
    ms.date="12/08/2015"
    ms.author="nitinme"/>

# Creare cluster Apache Spark in HDInsight usando le opzioni personalizzate (Windows)

> [AZURE.NOTE]HDInsight offre ora cluster Spark su Linux. Per informazioni su come creare un cluster Spark personalizzato in Linux per HDInsight, vedere [Creare cluster basati su Linux in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Per la maggior parte degli scenari, è possibile creare un cluster Spark usando il metodo veloce come descritto in [Introduzione ad Apache Spark in HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md). In alcuni scenari, è possibile creare un cluster personalizzato. Ad esempio, è possibile collegare un archivio di metadati esterno per conservare i metadati Hive in modo persistente oltre la durata di un cluster o è possibile utilizzare ulteriore spazio di archiviazione con il cluster.

Per questi e altri scenari, questo articolo fornisce istruzioni su come usare il portale di Azure, Azure PowerShell o .NET SDK per HDInsight per creare un cluster Spark personalizzato in HDInsight.


**Prerequisiti:**

Per poter eseguire le istruzioni descritte nell'articolo è necessario disporre di: Vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a id="configuration"></a>Quali sono le diverse opzioni di configurazione?

### Risorse di archiviazione aggiuntive

Durante la configurazione, è necessario specificare un account di archiviazione BLOB di Azure e un contenitore predefinito, che viene usato come posizione di archiviazione predefinita dal cluster. È possibile specificare anche un account di archiviazione di Azure aggiuntivo da associare al cluster.

>[AZURE.NOTE]Non condividere un contenitore di archiviazione BLOB tra più cluster. Questa operazione non è supportata.

Per altre informazioni sull'uso degli archivi BLOB secondari, vedere [Uso dell'archiviazione BLOB di Azure con HDInsight](hdinsight-use-blob-storage.md).

### Metastore

Spark consente di definire schema e Hive tabelle su dati non elaborati. È possibile salvare questi schemi e i metadati della tabella in Metastore esterni. Il metastore consente di conservare i propri metadati Hive, in modo da non dover creare nuovamente tabelle Hive alla creazione di un nuovo cluster. Per impostazione predefinita, Hive usa un database incorporato per l'archiviazione di queste informazioni. Se il cluster viene eliminato, tuttavia, i metadati non vengono mantenuti nel database incorporato.

Per istruzioni su come creare un database SQL in Azure, vedere [Creare il primo database SQL di Azure](sql-database-get-started.md).

### Personalizzazione cluster

L'uso di script durante la creazione consente di installare componenti aggiuntivi o personalizzare la configurazione di un cluster. Gli script vengono chiamati tramite un'**azione script**, ovvero un'opzione di configurazione che può essere usata da portale di Azure, dai cmdlet di Windows PowerShell per HDInsight o da .NET SDK per HDInsight. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante le azioni script][hdinsight-customize-cluster].


### Reti virtuali

[Rete virtuale di Azure](http://azure.microsoft.com/documentation/services/virtual-network/) consente di creare una rete sicura e persistente che contiene le risorse necessarie per la propria soluzione. Una rete virtuale consente di:

* Connettere le risorse cloud tra loro in una rete privata (solo cloud).

	![Diagramma di una configurazione solo cloud](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-cloud-only.png)

* Connettere le risorse cloud alla rete del data center locale (da sito a sito o da punto a sito) usando una rete privata virtuale (VPN).

	La configurazione da sito a sito consente di connettere più risorse dal data center alla rete virtuale di Azure usando una rete VPN hardware o il servizio Routing e accesso remoto.

	![Diagramma di una configurazione da sito a sito](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-site-to-site.png)

	La configurazione da punto a sito consente di connettere una risorsa specifica alla rete virtuale di Azure usando una rete VPN software.

	![Diagramma di una configurazione da punto a sito](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-point-to-site.png)

Per informazioni sull'uso di HDInsight con una rete virtuale, inclusi i requisiti di configurazione specifici per la rete virtuale, vedere l'articolo su come [estendere le funzionalità di HDInsight mediante una rete virtuale di Azure](hdinsight-extend-hadoop-virtual-network.md).

##<a id="portal"></a> Uso del portale di anteprima di Azure

I cluster Spark in HDInsight usano un contenitore dell'archivio BLOB di Azure come file system predefinito. Per creare un cluster HDInsight, è necessario un account di archiviazione di Azure nello stesso data center. Per altre informazioni, vedere [Usare l'archiviazione BLOB di Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md). Per dettagli sulla creazione di un account di archiviazione di Azure, vedere [Come creare un account di archiviazione][azure-create-storageaccount].

**Per creare un cluster HDInsight con l'opzione di creazione personalizzata**

1. Accedere al [portale di anteprima di Azure](https://portal.azure.com).
2. Fare clic su **NUOVO**, selezionare **Analisi dei dati** e quindi fare clic su **HDInsight**.

    ![Creazione di un nuovo cluster nel portale di anteprima di Azure](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.1.png "Creazione di un nuovo cluster nel portale di anteprima di Azure")

3. Immettere un **Nome cluster**, selezionare **Spark** per il **tipo di Cluster**, e dall’elenco a discesa **Sistema operativo Cluster**, selezionare **Windows Server 2012 R2 Datacenter**. Un segno di spunta verde verrà visualizzato accanto al nome del cluster, se disponibile.

	![Immettere il nome del cluster e il tipo](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.2.png "Immettere il nome del cluster e il tipo")

4. Se sono disponibili più sottoscrizioni, selezionare la voce **Sottoscrizione** per specificare la sottoscrizione di Azure da usare per il cluster.

5. Fare clic su **Gruppo di risorse** per visualizzare un elenco di gruppi di risorse esistenti e quindi selezionare quello in cui creare il cluster. In alternativa, è possibile fare clic su **Crea nuovo** e quindi immettere il nome del nuovo gruppo di risorse. Verrà visualizzato un segno di spunta verde per indicare se il nome del nuovo gruppo è disponibile.

	> [AZURE.NOTE]Questa voce sarà impostata su uno dei gruppi di risorse esistenti, se disponibili.

6. Fare clic su **Credenziali**, quindi immettere i valori di **Nome utente dell'account di accesso del cluster** e **Password dell'account di accesso del cluster**. Se si vuole abilitare il Desktop remoto nel nodo del cluster, su **Abilita Desktop remoto**, fare clic su **Sì**. Selezionare una data di scadenza per l’accesso desktop remoto al cluster e fornire il nome utente e la password per l'utente di desktop remoto. Fare clic su **Seleziona** nella parte inferiore per salvare la configurazione delle credenziali.

	![Fornire le credenziali del cluster](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.3.png "Fornire le credenziali del cluster")

7. Fare clic su **Origine dati** per scegliere un'origine dati esistente per il cluster o crearne una nuova.

	![Pannello di origine dati](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.4.png "Fornire la configurazione origine dati")

	Attualmente è possibile selezionare un account di archiviazione di Azure come origine dati per un cluster HDInsight. Usare le informazioni seguenti per comprendere le voci nel pannello **Origine dati**.

	- **Metodo di selezione**: impostare questa voce su **Da tutte le sottoscrizioni** per consentire l'esplorazione degli account di archiviazione da tutte le sottoscrizioni. Impostare questa voce su **Chiave di accesso** se si vuole immettere un valore nei campi **Nome archiviazione** e **Chiave di accesso** per un account di archiviazione esistente.

	- **Seleziona account di archiviazione / Crea nuovo**: fare clic su **Seleziona account di archiviazione** per cercare e selezionare un account di archiviazione esistente da associare al cluster. In alternativa, fare clic su **Crea nuovo** per creare un nuovo account di archiviazione. Utilizzare il campo che viene visualizzato per immettere il nome dell'account di archiviazione. Se il nome è disponibile, verrà visualizzato un segno di spunta verde.

	- **Scegli contenitore predefinito**: utilizzare questa opzione per immettere il nome del contenitore predefinito da utilizzare per il cluster. È possibile immettere qualsiasi nome, è consigliabile utilizzare lo stesso nome del cluster in modo che sia facilmente intuibile che il contenitore viene utilizzato per tale cluster specifico.

	- **Percorso**: l'area geografica dove si trova o dove verrà creato l'account di archiviazione.

		> [AZURE.IMPORTANT]La selezione del percorso per l'origine dati predefinito imposterà anche il percorso del cluster HDInsight. L'origine dati del cluster e l’origine dati predefinita devono trovarsi nella stessa area.

	Fare clic su **Seleziona** per salvare la configurazione dell'origine dati.

8. Fare clic su **Piani tariffari per il nodo** per visualizzare informazioni sui nodi che verranno creati per questo cluster. Impostare il numero di nodi del ruolo di lavoro necessari per il cluster. Verrà visualizzato il costo stimato del cluster all'interno del pannello.

	![Pannello livelli dei prezzi di nodo](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.5.png "Specificare il numero di nodi del cluster")

	Fare clic su **Seleziona** per salvare la configurazione di prezzi del nodo.

9. Fare clic su **Configurazione facoltativa** per selezionare la versione del cluster, nonché configurare altre impostazioni facoltative, ad esempio l'aggiunta a una **Rete virtuale**, l’impostazione di un **Metastore esterno** per contenere i dati per Hive e Oozie, utilizzare azioni di Script per personalizzare un cluster per installare i componenti personalizzati o utilizzare ulteriori account di archiviazione con il cluster.

	* Fare clic sull’elenco a discesa **Versione HDInsight** e selezionare la versione che si desidera utilizzare per il cluster. Per altre informazioni, vedere [Versioni del cluster HDInsight](hdinsight-component-versioning.md).

	* Fare clic su **Rete virtuale** per fornire i dettagli di configurazione per configurare il cluster come parte di una rete virtuale. Nel pannello **Rete virtuale**, fare clic su **Rete virtuale** e quindi fare clic sulla rete che si desidera utilizzare. Analogamente, selezionare una **Subnet** per la rete e quindi fare clic su **Seleziona** per salvare la configurazione della rete virtuale.

		![Pannello della rete virtuale](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.6.png "Specificare i dettagli della rete virtuale")

	* Fare clic su **Metastore esterni** per specificare il database SQL che si vuole usare per salvare i metadati Hive e Oozie associati al cluster.

		![Pannello metastore personalizzati](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.7.png "Specificare metastore esterni")

		Per **Usare un database SQL esistente per i metadati Hive**, fare clic su **Sì**, selezionare un database SQL e quindi specificare il nome utente e la password per il database. Ripetere questa procedura se si desidera **Utilizzare un database SQL esistente per i metadati Oozie**. Fare clic su **Seleziona** fino a quando non si visualizza di nuovo il pannello **Configurazione facoltativa**.

		>[AZURE.NOTE]Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Microsoft Azure** fare clic su **Sì** e infine su **Salva**.

	* Fare clic su **Azioni script** se si vuole usare uno script personalizzato per personalizzare un cluster, come il cluster che si sta creando. Per altre informazioni sulle azioni script, vedere [Personalizzare i cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster.md). Nel pannello Azioni di Script fornire i dettagli, come illustrato nella schermata.

		![Pannello azione di script](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.8.png "Specificare l'azione di script")

		Fare clic su **Seleziona** per salvare le modifiche alla configurazione dell'azione script.

	* Fare clic su **Chiavi di archiviazione di Azure** per specificare gli account di archiviazione aggiuntivi da associare al cluster. Nel pannello **Chiavi di archiviazione di Azure**, fare clic su **Aggiungi una chiave di archiviazione**, quindi selezionare un account di archiviazione esistente o creare un nuovo account.

		![Pannello risorse di archiviazione aggiuntive](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.9.png "Specificare gli account di archiviazione aggiuntivi")

		Fare clic su **Seleziona** fino a quando non verrà visualizzato il pannello **Nuovo cluster HDInsight**.

10. Nel pannello**Nuovo cluster HDInsight** assicurarsi che **Aggiungi a Schermata iniziale** sia selezionato, quindi fare clic su **Crea**. Questo creerà il cluster e aggiungerà una sezione apposita nella schermata iniziale del portale di Azure. L'icona indica che il cluster è in fase di creazione e verrà visualizzata l'icona di HDInsight al termine dell'operazione.

	| Durante la creazione | Creazione completata |
	| ------------------ | --------------------- |
	| ![Indicatore della creazione sulla schermata iniziale](./media/hdinsight-apache-spark-provision-clusters/provisioning.png) | ![Riquadro del cluster creato](./media/hdinsight-apache-spark-provision-clusters/provisioned.png) |

	> [AZURE.NOTE]La creazione del cluster richiederà del tempo, in genere circa 15 minuti. Usare il riquadro nella Schermata iniziale o la voce **Notifiche** nella parte sinistra della pagina per controllare il processo di creazione.

11. Al termine della creazione, fare clic sul riquadro per il cluster dalla Schermata iniziale per avviare il pannello del cluster. Il pannello del cluster fornisce informazioni essenziali sui cluster, ad esempio nome, gruppo di risorse che a cui appartiene, percorso, sistema operativo, URL per il dashboard del cluster e così via.

	![Pannello del cluster](./media/hdinsight-apache-spark-provision-clusters/hdispark.cluster.blade.png "Proprietà del cluster")

	Usare le informazioni seguenti per comprendere le icone nella parte superiore di questo pannello e nelle sezioni **Informazioni di base** e **Collegamenti**:

	* **Impostazioni** e **Tutte le impostazioni**: consentono di visualizzare il pannello**Impostazioni** per il cluster, che consente di accedere a informazioni dettagliate sulla configurazione del cluster.

	* **Dashboard** e **URL**: sono modi per accedere al dashboard del cluster, che è un portale Web per eseguire processi nel cluster.

	* **Desktop remoto**: consente di abilitare o disabilitare il desktop remoto nei nodi del cluster.

	* **Scala Cluster**: consente di modificare il numero di nodi del ruolo di lavoro per questo cluster.

	* **Elimina**: consente di eliminare il cluster HDInsight.

	* **Guide rapide** (![icona cloud e thunderbolt = guida rapida](./media/hdinsight-apache-spark-provision-clusters/quickstart.png)): Visualizza le informazioni che consentiranno di iniziare a usare HDInsight.

	* **Utenti** (![icona utenti](./media/hdinsight-apache-spark-provision-clusters/users.png)): consente di impostare le autorizzazioni per _Gestione portale_ di questo cluster per altri utenti nella sottoscrizione Azure.

		> [AZURE.IMPORTANT]Questo influisce _solo_ sull'accesso e sulle autorizzazioni per tale cluster nel portale di anteprima di Azure e non ha alcun effetto su chi può connettersi o inviare processi al cluster HDInsight.

	* **Tag** (![icona tag](./media/hdinsight-apache-spark-provision-clusters/tags.png)): consente di impostare coppie chiave/valore per definire una tassonomia dei servizi cloud personalizzata. Ad esempio, è possibile creare una chiave denominata __progetto__ e usare un valore comune per tutti i servizi associati a un progetto specifico.

	* **Dashboard del cluster**: avvia il pannello Dashboard cluster da cui è possibile avviare lo stesso dashboard del cluster o i notebook Zeppelin e Jupyter.


##<a id="powershell"></a>Uso di Azure PowerShell

Vedere [Creare cluster HDInsight](hdinsight-provision-clusters.md#create-using-azure-powershell).

Specificare l'opzione di tipo cluster Spark nel cmdlet New-AzureRmHDInsightCluster:

	-ClusterType Spark


## Uso di .NET SDK basato su ARM per HDInsight

Vedere [Creare cluster HDInsight](hdinsight-provision-clusters.md#create-using-the-hdinsight-net-sdk).

Specificare il tipo di cluster Spark:

	private const HDInsightClusterType NewClusterType = HDInsightClusterType.Spark;


##<a id="nextsteps"></a> Passaggi successivi

* Vedere [Eseguire l’analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools-v1.md) per imparare a utilizzare Apache Spark in HDInsight con strumenti di Business Intelligence quali Power BI e Tableau.
* Vedere [Usare Spark in HDInsight per la creazione di applicazioni di apprendimento automatico](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md) per imparare a creare applicazioni di apprendimento automatico usando Apache Spark in HDInsight.
* Vedere [Usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md) per imparare a creare applicazioni di streaming usando Apache Spark in HDInsight.
* Vedere [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager-v1.md) per imparare a usare Gestione risorse per gestire le risorse allocate al cluster Spark.


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-storm-get-started]: ../hdinsight-storm-getting-started/

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/

[azure-create-storageaccount]: ../storage-create-storage-account/

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[powershell-install-configure]: ../install-configure-powershell/


[azure-preview-portal]: https://portal.azure.com

[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Usare Sqoop con HDInsight"

<!---HONumber=AcomDC_0114_2016-->