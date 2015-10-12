<properties
   	pageTitle="Eseguire il provisioning di cluster Hadoop, HBase o Storm in Linux in HDInsight | Microsoft Azure"
   	description="Informazioni su come effettuare il provisioning di cluster Hadoop in Linux per HDInsight tramite il portale di gestione, la riga di comando e .NET SDK."
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
   	ms.date="09/21/2015"
   	ms.author="nitinme"/>


#provisioning di cluster Hadoop Linux in HDInsight con opzioni personalizzate.

Questo articolo descrive i diversi modi disponibili per effettuare il provisioning personalizzato di un cluster Hadoop Linux in Azure HDInsight usando il portale di Azure, Azure PowerShell, l’interfaccia della riga di comando di Azure o HDInsight .NET SDK.

## Informazioni sui cluster HDInsight

Il motivo per cui vengono chiamati in causa i cluster ogni volta che si discute di Hadoop o Big Data è che Hadoop consente l'elaborazione distribuita di dati di grandi dimensioni tra diversi nodi di un cluster. Il cluster presenta un'architettura master/worker con un nodo master (detto anche nodo head o Name Node) e un numero indeterminato di nodi worker (detti anche nodi dati). Per altre informazioni, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>.

![Cluster HDInsight][img-hdi-cluster]


Un cluster HDInsight astrae i dettagli dell'implementazione di Hadoop in modo che non sia necessario preoccuparsi di come comunicare con diversi nodi di un cluster. Quando si effettua il provisioning di un cluster HDInsight, si effettua il provisioning delle risorse di calcolo di Azure che includono Hadoop e le applicazioni correlate. Per altre informazioni, vedere [Introduzione a Hadoop in HDInsight](hdinsight-hadoop-introduction.md). I dati per la varianza sono archiviati nell'archivio BLOB di Azure. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight](../hdinsight-use-blob-storage.md).


In questo articolo vengono fornite istruzioni sui diversi modi di effettuare il provisioning di un cluster. Per informazioni su come iniziare rapidamente a effettuare il provisioning di un cluster, vedere l'articolo di [introduzione all'uso di Azure HDInsight in Linux](../hdinsight-hadoop-linux-get-started.md).

**Prerequisiti**

Per eseguire le procedure descritte nell'articolo è necessario:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Chiavi Secure Shell (SSH)**. Se si vuole accedere in modalità remota a un cluster Linux tramite SSH con una chiave invece che con una password. L'uso della chiave è il metodo consigliato, perché risulta più sicuro. Per istruzioni su come generare chiavi SSH, vedere gli articoli seguenti:
	-  Da un computer Linux: [Usare SSH con HDInsight basato su Linux (Hadoop) da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
	-  Da un computer Windows: [Usare SSH con HDInsight basato su Linux (Hadoop) da Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

## <a id="configuration"></a>Opzioni di configurazione

### Risorse di archiviazione aggiuntive

Durante la configurazione, è necessario specificare un account di archiviazione BLOB di Azure e un contenitore predefinito, Questo viene usato come posizione di archiviazione predefinita dal cluster. In alternativa è possibile specificare BLOB aggiuntivi da associare al cluster.


Per altre informazioni sull'uso degli archivi BLOB secondari, vedere [Uso dell'archivio BLOB di Azure con HDInsight](../hdinsight-use-blob-storage.md).


### Metastore

Il metastore contiene informazioni su tabelle Hive, partizioni, schemi, colonne e così via. Queste informazioni vengono usate da Hive per individuare la posizione di archiviazione dei dati in HDFS (Hadoop Distributed File System) o nell'archivio BLOB di Azure per HDInsight. Per impostazione predefinita, Hive usa un database incorporato per l'archiviazione di queste informazioni.

Quando si effettua il provisioning di un cluster HDInsight, è possibile specificare un database SQL che contiene il metastore per Hive. Ciò consente di conservare le informazioni dei metadati quando si elimina un cluster, perché vengono archiviate esternamente nel database SQL. Per istruzioni su come creare un database SQL in Azure, vedere [Creare il primo database SQL di Azure](sql-database-get-started.md).

### Personalizzare i cluster mediante l'azione script

L'uso di script durante il provisioning consente di installare componenti aggiuntivi o personalizzare la configurazione di un cluster. Questi script vengono richiamati mediante **Azione script**. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster-linux.md).

> [AZURE.IMPORTANT]Non è possibile aggiungere altri componenti dopo il provisioning di un cluster, poiché tali componenti non saranno disponibili dopo che è stata ricreata l'immagine di un nodo del cluster. I componenti installati tramite azioni script vengono reinstallati come parte del processo rimanente.

### Usare le reti virtuali di Azure

[Rete virtuale di Azure](http://azure.microsoft.com/documentation/services/virtual-network/) permette di creare una rete sicura e persistente che contiene le risorse necessarie per la propria soluzione. Una rete virtuale consente di:

* Connettere le risorse cloud tra loro in una rete privata (solo cloud).

	![Diagramma di una configurazione solo cloud](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* Connettere le risorse cloud alla rete del data center locale (da sito a sito o da punto a sito) usando una rete privata virtuale (VPN).

	La configurazione da sito a sito consente di connettere più risorse dal data center alla rete virtuale di Azure usando una rete VPN hardware o il servizio Routing e accesso remoto.

	![Diagramma di una configurazione da sito a sito](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png)

	La configurazione da punto a sito consente di connettere una risorsa specifica alla rete virtuale di Azure usando una rete VPN software.

	![Diagramma di una configurazione da punto a sito](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png)

Per altre informazioni sulle funzionalità, i vantaggi e le capacità della rete virtuale, vedere la [panoramica della rete virtuale di Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [AZURE.NOTE]È necessario creare la rete virtuale di Azure prima del provisioning di un cluster. Per altre informazioni, vedere l'articolo relativo alla [creazione di una rete virtuale](virtual-networks-create-vnet.md).
>
> Azure HDInsight supporta solo reti virtuali basate sulla posizione e attualmente non funziona con le reti virtuali basate su set di affinità. Usare il cmdlet Get-AzureVNetConfig di Azure PowerShell per verificare se una rete virtuale esistente di Azure è basata sulla posizione. Se la rete virtuale non è basata sulla posizione, saranno disponibili le opzioni seguenti:
>
> - Esportare la configurazione di rete virtuale esistente, quindi creare una nuova rete virtuale. Per impostazione predefinita, tutte le nuove reti virtuali sono basate sulla posizione.
> - Eseguire la migrazione a una rete virtuale basata sulla posizione. Vedere l'articolo relativo alla [migrazione di servizi esistenti a un ambito a livello di area](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/).
>
> È consigliabile designare una singola subnet per un cluster.
>
> Attualmente (25/8/2015) è possibile eseguire il provisioning solo di cluster basati su Linux in una rete virtuale di Azure.
>
> Non è possibile usare una rete virtuale di Azure v1 (classica) con HDInsight basato su Linux. La rete virtuale deve essere v2 (Gestione risorse di Azure) per poter essere elencata come opzione durante il processo di creazione di cluster HDInsight nel portale di anteprima di Azure o possa essere usata durante la creazione di un cluster mediante l'interfaccia della riga di comando di Azure o Azure PowerShell.
>
> Se si dispone di risorse in una rete v1 e si desidera rendere HDInsight direttamente accessibile a tali risorse attraverso una rete virtuale, vedere [Connessione di reti virtuali classiche a nuove reti virtuali](../virtual-network/virtual-networks-arm-asm-s2s.md) per informazioni su come connettersi una rete virtuale v2 a una rete virtuale v1. Una volta stabilita la connessione, è possibile creare il cluster HDInsight nella rete virtuale v2.

## <a id="options"></a>Opzioni per il provisioning di un cluster HDInsight Linux

È possibile effettuare il provisioning di un cluster HDInsight Hadoop Linux da un computer Linux, ma anche da un computer Windows. La seguente tabella fornisce informazioni sulle opzioni di provisioning disponibili dai diversi sistemi operativi e collegamenti per accedere a istruzioni relative a ognuna di queste opzioni.

Provisioning di cluster Linux da un computer che esegue questo sistema operativo | Uso del portale di Azure | Utilizzare l'interfaccia della riga di comando di Azure | Uso di Azure PowerShell | Uso di .NET SDK
-----------------| --------| ------------------------| -------------------| ---------- | ---------
Linux| Fare clic [qui](#portal) | Fare clic [qui](#cli) | Non applicabile | Non applicabile
Mac OS X | Fare clic [qui](#portal) | Fare clic [qui](#cli) | Non applicabile | Non applicabile
Windows | Fare clic [qui](#portal) | Fare clic [qui](#cli) | Fare clic [qui](#powershell) | Fare clic [qui](#sdk)

### <a id="portal"></a>Uso del portale di Azure

I cluster HDInsight usano un contenitore dell'archivio BLOB di Azure come file system predefinito. Per creare un cluster HDInsight, è necessario un account di archiviazione di Azure nello stesso data center. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight](../hdinsight-use-blob-storage.md). Per dettagli sulla creazione di un account di archiviazione di Azure, vedere [Creare un account di archiviazione](../storage-create-storage-account.md).


> [AZURE.NOTE]Attualmente i cluster HDInsight Linux possono essere ospitati solo nelle aree seguenti: **Asia sudorientale**, **Europa settentrionale**, **Stati Uniti orientali** e **Stati Uniti centro-meridionali**.

**Per creare un cluster HDInsight**

1. Accedere al [portale di anteprima di Azure](https://portal.azure.com).
2. Fare clic su **NUOVO**, su **Analisi dei dati** e quindi su **HDInsight**.

    ![Creazione di un nuovo cluster nel portale di anteprima di Azure](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.1.png "Creazione di un nuovo cluster nel portale di anteprima di Azure")

3. Immettere un valore in **Nome cluster**, selezionare **Hadoop** per **Tipo di cluster** e dall'elenco a discesa **Sistema operativo cluster** selezionare **Ubuntu**. Un segno di spunta verde verrà visualizzato accanto al nome del cluster, se disponibile.


	> [AZURE.NOTE]Per eseguire il provisioning di cluster HBase o Storm, selezionare il valore appropriato nell'elenco a discesa **Tipo di cluster**.


	![Immettere il nome del cluster e il tipo](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.2.png "Immettere il nome del cluster e il tipo")

4. Se si dispone di più di una sottoscrizione, fare clic sulla voce **Sottoscrizione** per selezionare la sottoscrizione di Azure che verrà usata per il cluster.

5. Fare clic su **Gruppo di risorse** per visualizzare un elenco di gruppi di risorse esistenti e quindi selezionare quello in cui creare il cluster. In alternativa, è possibile fare clic su **Crea nuovo** e quindi immettere il nome del nuovo gruppo di risorse. Verrà visualizzato un segno di spunta verde per indicare se il nome del nuovo gruppo è disponibile.

	> [AZURE.NOTE]Questa voce sarà impostata su uno dei gruppi di risorse esistenti, se disponibili.

6. Fare clic su **Credenziali** e quindi immettere una password per l'utente amministratore. È anche necessario immettere un valore nel campo **Nome utente SSH** e un valore nel campo **PASSWORD** o **CHIAVE PUBBLICA**, che verranno usati per autenticare l'utente SSH. Si consiglia di utilizzare una chiave pubblica. Fare clic su **Seleziona** nella parte inferiore per salvare la configurazione delle credenziali.

	![Fornire le credenziali del cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.3.png "Fornire le credenziali del cluster")

	Per altre informazioni sull'uso di SSH con HDInsight, vedere gli articoli seguenti:

	* [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Fare clic su **Origine dati** per scegliere un'origine dati esistente per il cluster o crearne una nuova.

	![Pannello di origine dati](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.4.png "Fornire la configurazione origine dati")

	Attualmente è possibile selezionare un account di archiviazione di Azure come origine dati per un cluster HDInsight. Usare le seguenti informazioni per comprendere le voci nel pannello **Origine dati**.

	- **Metodo di selezione**: impostare questa proprietà su **Da tutte le sottoscrizioni** per consentire l'esplorazione di account di archiviazione da tutte le sottoscrizioni. Impostare questa proprietà su **Chiave di accesso** se si desidera immettere un valore nei campi **Nome archiviazione** e **Chiave di accesso** per un account di archiviazione esistente.

	- **Selezionare l'account di archiviazione / Crea nuovo**: fare clic su **Selezionare l'account di archiviazione** per cercare e selezionare un account di archiviazione esistente da associare al cluster. In alternativa, fare clic su **Crea nuovo** per creare un nuovo account di archiviazione. Utilizzare il campo che viene visualizzato per immettere il nome dell'account di archiviazione. Se il nome è disponibile, verrà visualizzato un segno di spunta verde.

	- **Scegliere il contenitore predefinito**: usare questa opzione per immettere il nome del contenitore predefinito da usare per il cluster. È possibile immettere qualsiasi nome, è consigliabile utilizzare lo stesso nome del cluster in modo che sia facilmente intuibile che il contenitore viene utilizzato per tale cluster specifico.

	- **Percorso**: l'area geografica dove si trova o dove verrà creato l'account di archiviazione.

		> [AZURE.IMPORTANT]La selezione del percorso per l'origine dati predefinito imposterà anche il percorso del cluster HDInsight. L'origine dati del cluster e l’origine dati predefinita devono trovarsi nella stessa area.

	Fare clic su **Seleziona** per salvare la configurazione dell'origine dati.

8. Fare clic su **Piani tariffari per il nodo** per visualizzare informazioni sui nodi che verranno creati per questo cluster. Impostare il numero di nodi del ruolo di lavoro necessari per il cluster. Verrà visualizzato il costo stimato del cluster all'interno del pannello.

	![Pannello livelli dei prezzi di nodo](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.5.png "Specificare il numero di nodi del cluster")

	Fare clic su **Seleziona** per salvare la configurazione del prezzo del nodo.

9. Fare clic su **Configurazione facoltativa** per selezionare la versione del cluster, nonché configurare altre impostazioni facoltative, ad esempio l'aggiunta a una **rete virtuale**, l’impostazione di un **metastore esterno** per contenere i dati per Hive e Oozie, usare azioni di script per personalizzare un cluster per installare i componenti personalizzati o usare ulteriori account di archiviazione con il cluster.

	* Fare clic sull'elenco a discesa **Versione HDInsight** e selezionare la versione che si desidera usare per il cluster. Per altre informazioni, vedere [Versioni del cluster HDInsight](hdinsight-component-versioning.md).


	* **Rete virtuale**: selezionare una rete virtuale di Azure e la subnet se si desidera posizionare il cluster in una rete virtuale.

		![Pannello della rete virtuale](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.6.png "Specificare i dettagli della rete virtuale")

    	>[AZURE.NOTE]Il cluster HDInsight basato su Windows può essere inserito solo in una rete virtuale classica.


	* Fare clic su **Metastore esterno** per specificare il database SQL che si desidera usare per salvare i metadati Hive e Oozie associati al cluster.

		![Pannello metastore personalizzati](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.7.png "Specificare metastore esterni")

		Per i metadati **Usare un database SQL esistente per Hive**, fare clic su **Sì**, selezionare un database SQL e quindi specificare il nome utente e la password per il database. Ripetere questi passaggi se si desidera **usare un database SQL esistente per i metadati Oozie**. Fare clic su **Seleziona** finché non viene visualizzato di nuovo il pannello **Configurazione facoltativa**.

		>[AZURE.NOTE]Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Windows Azure** fare clic su **Sì** e infine su **Salva**.


	* **Azioni script**: se si desidera usare uno script personalizzato per personalizzare un cluster durante la creazione. Per altre informazioni sulle azioni script, vedere [Personalizzare cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster-linux.md). Nel pannello Azioni di Script fornire i dettagli, come illustrato nella schermata.

		![Pannello azione di script](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.8.png "Specificare l'azione di script")


	* Fare clic su **Chiavi di archiviazione di Azure** per specificare gli account di archiviazione aggiuntivi da associare al cluster. Nel pannello **Chiavi di archiviazione di Azure** fare clic su **Aggiungi una chiave di archiviazione** e quindi selezionare un account di archiviazione esistente o creare un nuovo account.

		![Pannello risorse di archiviazione aggiuntive](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.9.png "Specificare gli account di archiviazione aggiuntivi")

		Fare clic su **Seleziona** fino a quando non verrà visualizzato il pannello **Nuovo cluster HDInsight**.

10. Nel pannello **Nuovo cluster HDInsight** assicurarsi che **Aggiungi alla schermata iniziale** sia selezionato e quindi fare clic su **Crea**. Questo creerà il cluster e aggiungerà una sezione apposita nella schermata iniziale del portale di Azure. L'icona indica che il cluster sta eseguendo il provisioning e verrà visualizzata l'icona di HDInsight, una volta completato il provisioning.

	| Durante il provisioning | Provisioning completato |
	| ------------------ | --------------------- |
	| ![Indicatore del provisioning nella Schermata iniziale](./media/hdinsight-hadoop-provision-linux-clusters/provisioning.png) | ![Sezione del cluster su cui è stato effettuato il provisioning](./media/hdinsight-hadoop-provision-linux-clusters/provisioned.png) |

	> [AZURE.NOTE]La creazione del cluster richiederà del tempo, in genere circa 15 minuti. Usare il riquadro sulla schermata iniziale o la voce **Notifiche** a sinistra della pagina per controllare il processo di provisioning.

11. Al termine del provisioning, fare clic sul riquadro per il cluster dalla schermata iniziale per avviare il pannello del cluster. Il pannello del cluster fornisce informazioni essenziali sui cluster, ad esempio nome, gruppo di risorse che a cui appartiene, percorso, sistema operativo, URL per il dashboard del cluster e così via.

	![Pannello del cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.Blade.png "Proprietà del cluster")

	Usare le informazioni seguenti per comprendere le icone nella parte superiore di questo pannello e nella sezione **Essentials**:

	* **Impostazioni** e **Tutte le impostazioni**: consentono di visualizzare il pannello **Impostazioni** per il cluster, che consente di accedere a informazioni dettagliate sulla configurazione del cluster.

	* **Dashboard**, **Dashboard del cluster** e **URL**: sono tutti modi per accedere al dashboard del cluster, ovvero a un portale Web per eseguire processi nel cluster.

	* **Secure Shell**: informazioni necessarie per accedere al cluster tramite SSH.

	* **Elimina**: consente di eliminare il cluster HDInsight.

	* **Guida introduttiva** (![icona cloud e thunderbolt = guida rapida](./media/hdinsight-hadoop-provision-linux-clusters/quickstart.png)): visualizza le informazioni che consentiranno di iniziare a usare HDInsight.

	* **Utenti** (![icona utenti](./media/hdinsight-hadoop-provision-linux-clusters/users.png)): consente di impostare le autorizzazioni per la _gestione del portale_ di questo cluster per altri utenti nella sottoscrizione Azure.

		> [AZURE.IMPORTANT]Ciò influisce _solo_ sull'accesso e sulle autorizzazioni per tale cluster nel portale di anteprima di Azure e non ha alcun effetto su chi può connettersi o inviare processi al cluster HDInsight.

	* **Tag** (![icona tag](./media/hdinsight-hadoop-provision-linux-clusters/tags.png)): consente di impostare coppie chiave/valore per definire una tassonomia dei servizi cloud personalizzata. Ad esempio, è possibile creare una chiave denominata __progetto__ e quindi usare un valore comune per tutti i servizi associati a un progetto specifico.

### <a id="cli"></a> Uso dell'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure è un'utilità della riga di comando multipiattaforma che consente di gestire i servizi di Azure. Può essere usata con i modelli di Gestione risorse di Azure per eseguire il provisioning di un cluster HDInsight insieme agli account di archiviazione associati e ad altri servizi.

I modelli di Gestione risorse di Azure sono documenti JSON che descrivono un __gruppo di risorse__ e tutte le risorse in esso contenute, ad esempio HDInsight. Questo approccio basato sui modelli consente di definire tutte le risorse necessarie per HDInsight in un modello e di gestire le modifiche apportate all'intero gruppo mediante __distribuzioni__ che applicano modifiche al gruppo.

I passaggi seguenti descrivono il processo di creazione di un nuovo cluster HDInsight tramite l'interfaccia della riga di comando di Azure e un modello:

1. Se l'interfaccia non è ancora installata, seguire i passaggi descritti nel documento [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md).

2. Dalla riga di comando, da un terminale o da una shell usare il comando seguente per eseguire l'autenticazione per la sottoscrizione di Azure.

        azure login

    Verrà chiesto di specificare il nome e la password. Se si dispone di più sottoscrizioni di Azure, è possibile usare `azure account set <subscriptionname>` per impostare la sottoscrizione che verrà usata dai comandi dell'interfaccia della riga di comando di Azure.

3. Passare alla modalità Gestione risorse di Azure usando il comando seguente:

        azure config mode arm

4. Creare un modello per il cluster HDInsight. Di seguito sono indicati alcuni modelli di esempio di base:

    * [Cluster basato su Linux, con una chiave pubblica SSH](https://github.com/matt1883/azure-quickstart-templates/tree/master/hdinsight-linux-sshpublickey)
    * [Cluster basato su Linux, con una password per l'account SSH](https://github.com/matt1883/azure-quickstart-templates/tree/master/hdinsight-linux-sshpassword)

    Entrambi i modelli creano anche l'account di archiviazione di Azure predefinito usato da HDInsight.

    I file necessari sono __azuredeploy.json__ e __azuredeploy.parameters.json__.

5. Aprire il file __azuredeploy.parameters.json__ in un editor e specificare i valori per gli elementi nella sezione `parameters`:

    * __location__: data center in cui verranno create le risorse. Per un elenco delle località consentite, vedere la sezione `location` nel file __azuredeploy.json__.
    * __clusterName__: nome del cluster HDInsight. Questo nome deve essere univoco, altrimenti la distribuzione avrà esito negativo.
    * __clusterStorageAccountName__: nome dell'account di archiviazione di Azure che verrà creato per il cluster HDInsight. Questo nome deve essere univoco, altrimenti la distribuzione avrà esito negativo.
    * __clusterLoginPassword__: password dell'utente amministratore del cluster. Deve essere una password sicura, poiché viene usata per accedere ai siti Web e ai servizi REST nel cluster.
    * __sshUserName__: nome del primo utente SSH da creare per il cluster. Per accedere in remoto al cluster con questo account sarà necessario usare SSH. Il nome deve essere univoco e non può corrispondere al nome di un account già in uso nel cluster. I nomi non validi includono root, storm e hbase.
    * __sshPublicKey__: se si usa il modello che richiede una chiave pubblica SSH, è necessario aggiungere la chiave pubblica in questa riga. Per altre informazioni sulla generazione e sull'uso di chiavi pubbliche, vedere gli articoli seguenti:

        * [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
        * [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    * __sshPassword__: se si usa il modello che richiede una password SSH, è necessario aggiungere una password in questa riga.

    Al termine, salvare e chiudere il file.

5. Per creare un gruppo di risorse vuoto, seguire questa procedura. Sostituire __RESOURCEGROUPNAME__ con il nome che si desidera usare per questo gruppo. Sostituire __LOCATION__ con il data center in cui deve essere creato il gruppo:

        azure group create RESOURCEGROUPNAME LOCATION
    
    > [AZURE.NOTE]Se il nome della località contiene spazi, racchiuderlo tra virgolette doppie, ad esempio "Stati Uniti centrali del sud".

6. Usare il comando seguente per creare la distribuzione iniziale per questo gruppo di risorse. Sostituire __PATHTOTEMPLATE__ con il percorso del file modello __azuredeploy.json__. Sostituire__PATHTOPARAMETERSFILE__ con il percorso del file __azuredeploy.parameters.json__. Sostituire __RESOURCEGROUPNAME__ con il nome del gruppo creato nel passaggio precedente:

        azure group deployment create -f PATHTOTEMPLATE -e PATHTOPARAMETERSFILE -g RESOURCEGROUPNAME -n InitialDeployment

    Dopo che la distribuzione è stata accettata, verrà visualizzato un messaggio simile a `group deployment create command ok`.

7. Il completamento della distribuzione può richiedere tempo, circa 15 minuti. Usare il comando seguente per visualizzare informazioni sulla distribuzione. Sostituire __RESOURCEGROUPNAME__ con il nome del gruppo di risorse usato nel passaggio precedente:

        azure group log show -l RESOURCEGROUPNAME

    Se si verifica un errore durante la distribuzione, è possibile ottenere altre informazioni sull'errore usando il comando seguente:

        azure group log show -l -v RESOURCEGROUPNAME

### <a id="powershell"></a> Uso di Azure PowerShell

Azure PowerShell è un ambiente di scripting potente che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. Questa sezione include le istruzioni su come effettuare il provisioning di un cluster HDInsight con Azure PowerShell. Per informazioni sulla configurazione di una workstation per l'esecuzione dei cmdlet di Windows PowerShell per HDInsight, vedere [Installare e configurare Azure PowerShell](../install-configure-powershell.md). Per altre informazioni sull'uso di Azure PowerShell con HDInsight, vedere [Amministrare HDInsight tramite PowerShell](hdinsight-administer-use-powershell.md). Per l'elenco dei cmdlet Windows PowerShell per HDInsight, vedere [Documentazione di riferimento dei cmdlet di HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Le procedure seguenti sono necessarie per effettuare il provisioning di un cluster HDInsight con Azure PowerShell:

- Creare un gruppo di risorse di Azure
- Creare un account di archiviazione di Azure
- Creazione di un contenitore BLOB di Azure
- Creare un cluster HDInsight

I due parametri più importanti da impostare per il provisioning dei cluster Linux sono quelli in cui vengono specificati il tipo di sistema operativo e i dettagli dell'utente SSH:

- Assicurarsi di specificare il parametro **- OSType** come **Linux**.
- Per usare SSH per le sessioni remote nei cluster, è possibile specificare la password utente SSH oppure la chiave pubblica SSH. Se si specificano sia la password utente SSH e la chiave pubblica SSH, la chiave verrà ignorata. Per usare la chiave SSH per le sessioni remote, specificare una password SSH vuota quando viene richiesta. Per altre informazioni sull'uso di SSH con HDInsight, vedere gli articoli seguenti:

	* [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


			# Use the new Azure Resource Manager mode
			Switch-AzureMode AzureResourceManager

			###########################################
			# Create required items, if none exist
			###########################################

			# Sign in
			Add-AzureAccount

			# Select the subscription to use
			$subscriptionName = "<SubscriptionName>"        # Provide your Subscription Name
			Select-AzureSubscription -SubscriptionName $subscriptionName

			# Register your subscription to use HDInsight
			Register-AzureProvider -ProviderNamespace "Microsoft.HDInsight" -Force

			# Create an Azure Resource Group
			$resourceGroupName = "<ResourceGroupName>"      # Provide a Resource Group name
			$location = "<Location>"                        # For example, "West US"
			New-AzureResourceGroup -Name $resourceGroupName -Location $location

			# Create an Azure Storage account
			$storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
			New-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

			# Create an Azure Blob Storage container
			$containerName = "<ContainerName>"              # Provide a container name
			$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
			$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
			New-AzureStorageContainer -Name $containerName -Context $destContext

			###########################################
			# Create an HDInsight Cluster
			###########################################

			# Skip these variables if you just created them
			$resourceGroupName = "<ResourceGroupName>"      # Provide the Resource Group name
			$storageAccountName = "<StorageAcccountName>"   # Provide the Storage account name
			$containerName = "<ContainerName>"              # Provide the container name
			$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }

			# Set these variables
			$clusterName = $containerName           		# As a best practice, have the same name for the cluster and container
			$clusterNodes = <ClusterSizeInNodes>    		# The number of nodes in the HDInsight cluster
			$credentials = Get-Credential
			$sshCredentials = Get-Credential

			# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
			$location = Get-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

			# Create a new HDInsight cluster
			New-AzureHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -OSType Linux -Version "3.2" -SshCredential $sshCredentials


>[AZURE.NOTE]I valori specificati per **$clusterCredentials** vengono usati per creare l'account utente Hadoop per il cluster. Questo account verrà usato per connettersi al cluster. I valori specificati per **$sshCredentials** vengono usati per creare l'utente SSH per il cluster. Questo account viene usato per avviare una sessione SSH remota nel cluster ed eseguire i processi. Se si usa l'opzione Creazione rapida del portale di Azure per effettuare il provisioning di un cluster, il nome utente Hadoop predefinito è "admin" e il nome utente SSH predefinito è "hdiuser".

Il completamento del provisioning del cluster può richiedere alcuni minuti.

![HDI.CLI.Provision][image-hdi-ps-provision]



###<a id="sdk"></a>Uso di HDInsight .NET SDK
.NET SDK per HDInsight fornisce librerie client .NET che semplificano l'uso di HDInsight da un'applicazione .NET Framework. Seguire le istruzioni seguenti per creare un'applicazione console Visual Studio e incollare il codice per la creazione di un cluster.

**Per creare un'applicazione console di Visual Studio**

1. Aprire Visual Studio 2013 o 2015.
2. Creare un nuovo progetto di Visual Studio con le impostazioni seguenti

	|Proprietà|Valore|
	|--------|-----|
	|Modello|Templates/Visual C#/Windows/Console Application|
	|Nome|CreateHDICluster|

5. Dal menu **Strumenti** fare clic su **Gestione pacchetti NuGet**, quindi su **Console di Gestione pacchetti**.
6. Eseguire il comando seguente nella console per installare i pacchetti:

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre

	Questi comandi aggiungono librerie .NET e riferimenti ad esse nel progetto corrente di Visual Studio.

6. Da Esplora soluzioni fare doppio clic su **Program.cs** per aprirlo, incollare il codice seguente e fornire valori per le variabili:

		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;

		namespace CreateHDICluster
		{
		    internal class Program
		    {
		        private static HDInsightManagementClient _hdiManagementClient;
		
		        private static Guid SubscriptionId = new Guid("<AZURE SUBSCRIPTION ID>");
		        private const string ResourceGroupName = "<AZURE RESOURCEGROUP NAME>";

		        private const string NewClusterName = "<HDINSIGHT CLUSTER NAME>";
		        private const int NewClusterNumNodes = <NUMBER OF NODES>;
		        private const string NewClusterLocation = "<LOCATION>";  // Must match the Azure Storage account location
		        private const string NewClusterVersion = "3.2";
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
		        private const OSType NewClusterOSType = OSType.Windows;

		        private const string ExistingStorageName = "<STORAGE ACCOUNT NAME>.blob.core.windows.net";
		        private const string ExistingStorageKey = "<STORAGE ACCOUNT KEY>";
		        private const string ExistingContainer = "<DEFAULT CONTAINER NAME>"; 

		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<HTTP USER PASSWORD>";

		        private const string NewClusterSshUserName = "sshuser";
		        private const string NewClusterSshPublicKey = @"---- BEGIN SSH2 PUBLIC KEY ----
					Comment: ""rsa-key-20150731""
					AAAAB3NzaC1yc2EAAAABJQAAAQEA4QiCRLqT7fnmUA5OhYWZNlZo6lLaY1c+IRsp
					gmPCsJVGQLu6O1wqcxRqiKk7keYq8bP5s30v6bIljsLZYTnyReNUa5LtFw7eauGr
					yVt3Pve6ejfWELhbVpi0iq8uJNFA9VvRkz8IP1JmjC5jsdnJhzQZtgkIrdn3w0e6
					WVfu15kKyY8YAiynVbdV51EB0SZaSLdMZkZQ81xi4DDtCZD7qvdtWEFwLa+EHdkd
					pzO36Mtev5XvseLQqzXzZ6aVBdlXoppGHXkoGHAMNOtEWRXpAUtEccjpATsaZhQR
					zZdZlzHduhM10ofS4YOYBADt9JohporbQVHM5w6qUhIgyiPo7w==
					---- END SSH2 PUBLIC KEY ----"; //replace the public key with your own
		
		        private static void Main(string[] args)
		        {
		            var tokenCreds = GetTokenCloudCredentials();
		            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
		
		            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
		
		            CreateCluster();
		        }
		
		        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
		        {
		            var authFactory = new AuthenticationFactory();
		
		            var account = new AzureAccount { Type = AzureAccount.AccountType.User };
		
		            if (username != null && password != null)
		                account.Id = username;
		
		            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
		
		            var accessToken =
		                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
		                    .AccessToken;
		
		            return new TokenCloudCredentials(accessToken);
		        }
		
		        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
		        {
		            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
		        }
		
		
		        private static void CreateCluster()
		        {
		            var parameters = new ClusterCreateParameters
		            {
		                ClusterSizeInNodes = NewClusterNumNodes,
		                Location = NewClusterLocation,
		                ClusterType = NewClusterType,
		                OSType = NewClusterOSType,
		                Version = NewClusterVersion,

		                DefaultStorageAccountName = ExistingStorageName,
		                DefaultStorageAccountKey = ExistingStorageKey,
		                DefaultStorageContainer = ExistingContainer,

		                UserName = NewClusterUsername,
		                Password = NewClusterPassword,
		                SshUserName = NewClusterSshUserName,
                		SshPublicKey = NewClusterSshPublicKey
		            };
		
		            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
		        }
			}
		}
		
10. Sostituire i valori dei membri di classe.

7. Premere **F5** per eseguire l'applicazione. Verrà aperta una finestra della console in cui è visualizzato lo stato dell'applicazione. Verrà richiesto di immettere le credenziali dell'account Azure. Possono essere necessari alcuni minuti per creare un cluster HDInsight.



##<a id="nextsteps"></a> Passaggi successivi
In questo articolo si sono appresi diversi modi per effettuare il provisioning di un cluster HDInsight Hadoop in Linux. Per altre informazioni, vedere gli articoli seguenti:

- [Uso di HDInsight in Linux](hdinsight-hadoop-linux-information.md): dettagli sull'uso di un cluster HDInsight in Linux.
- [Gestire cluster HDInsight tramite Ambari](hdinsight-hadoop-manage-ambari.md): informazioni su come monitorare e gestire il cluster Hadoop basato su Linux in HDInsight tramite il sito Web Ambari o l'API REST Ambari.
- [Usare Hadoop MapReduce in HDInsight](hdinsight-use-mapreduce.md): informazioni sulle diverse modalità di esecuzione di processi MapReduce in un cluster.
- [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md): informazioni sulle diverse modalità di esecuzione di una query Hive in un cluster.
- [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md): informazioni sulle diverse modalità di esecuzione di un processo Pig in un cluster.
- [Usare l'archivio BLOB di Azure con HDInsight](../hdinsight-use-blob-storage.md): informazioni sulle modalità con cui HDInsight usa l'archivio BLOB di Azure per archiviare dati per i cluster HDInsight.
- [Caricare dati in HDInsight](hdinsight-upload-data.md): informazioni sull'uso dei dati contenuti in un archivio BLOB di Azure per un cluster HDInsight.


[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-create-storageaccount]: ../storage-create-storage-account/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[azure-preview-portal]: https://portal.azure.com


[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "Elenco e visualizzazione di cluster"

[image-hdi-ps-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.provision.png
[image-hdi-ps-config-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.config.provision.png

[img-hdi-cluster]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.png

  [89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Usare Sqoop con HDInsight"

<!---HONumber=Oct15_HO1-->