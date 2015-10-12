<properties
	pageTitle="Gestire cluster Hadoop in HDInsight tramite il portale di Azure | Microsoft Azure"
	description="Informazioni su come amministrare il servizio HDInsight. Creare un cluster HDInsight, aprire la console interattiva JavaScript e aprire la console dei comandi di Hadoop."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/06/2015"
	ms.author="larryfr"/>

# Gestire cluster Hadoop in HDInsight tramite il portale di anteprima di Azure

[AZURE.INCLUDE [selettore](../../includes/hdinsight-portal-management-selector.md)]


Tramite il [Portale di anteprima di Azure][preview-portal], è possibile eseguire il provisioning e gestire i cluster Hadoop basati su Linux in Azure HDInsight.

> [AZURE.NOTE]I passaggi descritti in questo documento sono specifici per i cluster basati su Linux. Per informazioni sull'utilizzo dei cluster basati su Windows, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di anteprima di Azure](hdinsight-administer-use-management-portal.md)


[AZURE.INCLUDE [preview-portal](../../includes/hdinsight-azure-preview-portal-nolink.md)]


## Altri strumenti per l'amministrazione di HDInsight
Oltre al portale di Azure sono disponibili altri strumenti per amministrare HDInsight.

- [Amministrare HDInsight utilizzando l’interfaccia della riga di comando di Azure](hdinsight-administer-use-command-line.md): l’interfaccia della riga di comando di Azure è uno strumento della riga di comando multipiattaforma che consente di gestire i servizi di Azure

- [Amministrare HDInsight tramite Azure PowerShell](hdinsight-administer-use-powershell.md): Azure PowerShell fornisce cmdlet di PowerShell per la gestione dei servizi di Azure

## Prerequisiti

Per eseguire le procedure descritte nell'articolo è necessario:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

## Provisioning di cluster HDInsight

È possibile effettuare il provisioning di cluster HDInsight dal portale di Azure tramite i seguenti passaggi:

1. Accedere al [portale di anteprima di Azure][preview-portal].

2. Selezionare **NUOVO**, selezionare __Analisi di dati__, quindi selezionare __HDInsight__

	![Creazione di un nuovo cluster nel portale di Anteprima di Azure](./media/hdinsight-administer-use-portal-linux/new-cluster.png)

3. Immettere un __Nome cluster__, quindi selezionare il __Tipo cluster__ che si vuole creare. Un segno di spunta verde verrà visualizzato accanto al __Nome del cluster__, se disponibile.

	![Nome del cluster, tipo di cluster e tipo di sistema operativo](./media/hdinsight-administer-use-portal-linux/clustername.png)

4. Se si dispone di più di una sottoscrizione, selezionare la voce __Sottoscrizione__ per selezionare la sottoscrizione di Azure che verrà usata per il cluster.

5. Selezionare __Gruppo di risorse__ per visualizzare un elenco di gruppi di risorse esistenti e quindi selezionare quello in cui creare il cluster. In alternativa, è possibile selezionare __Crea nuovo__ e quindi immettere il nome del nuovo gruppo di risorse. Verrà visualizzato un segno di spunta verde per indicare se il nome del nuovo gruppo è disponibile.

	> [AZURE.NOTE]Questa voce sarà impostata su uno dei gruppi di risorse esistenti, se disponibili.

6. Selezionare __Credenziali__ e quindi immettere una __Password dell'account di accesso del cluster__ per il __Nome utente dell'account di accesso del cluster__. È anche necessario specificare un __Nome utente SSH__ e una __PASSWORD__ o una __CHIAVE PUBBLICA__, che verranno usati per autenticare l'utente SSH. Infine, usare il pulsante __Seleziona__ per impostare le credenziali. Il desktop remoto non verrà utilizzato in questo documento, pertanto è possibile lasciarlo disabilitato.

	![Pannello credenziali del cluster](./media/hdinsight-administer-use-portal-linux/clustercredentials.png)
    
    > [AZURE.NOTE]Il protocollo SSH viene usato per accedere in modalità remota al cluster HDInsight tramite una riga di comando. Il nome utente e la password o la chiave pubblica specificati qui verranno usati per la connessione al cluster tramite SSH. Il nome utente SSH deve essere univoco, in quanto crea un account utente in tutti i nodi del cluster HDInsight. Di seguito sono riportati alcuni nomi di account riservati per l'uso da parte dei servizi nel cluster che __non devono__ essere usati come nome utente SSH:
    >
    > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.

    Per altre informazioni sull'uso di SSH con HDInsight, vedere gli articoli seguenti:

	* [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows)

7. Selezionare la voce __Origine dati__ per scegliere un'origine dati esistente o crearne una nuova.

	![Pannello di origine dati](./media/hdinsight-administer-use-portal-linux/datasource.png)

	Attualmente è possibile selezionare un account di archiviazione di Azure come origine dati per un cluster HDInsight. Per comprendere le voci nel pannello __Origine dati__, usare le informazioni seguenti.

	- __Metodo di selezione__: impostare questa proprietà su __Da tutte le sottoscrizioni__ per consentire l'esplorazione di account di archiviazione da tutte le sottoscrizioni. Impostare questa proprietà su __Chiave di accesso__ se si vuole specificare il __Nome archiviazione__ e la __Chiave di accesso__ di un account di archiviazione esistente.

	- __Crea nuovo__: usare questa opzione per creare un nuovo account di archiviazione. Utilizzare il campo che viene visualizzato per immettere il nome dell'account di archiviazione. Se il nome è disponibile, verrà visualizzato un segno di spunta verde.

	- __Scegliere il contenitore predefinito__: usare questa opzione per specificare il nome del contenitore predefinito da usare per il cluster. È possibile immettere qualsiasi nome, è consigliabile utilizzare lo stesso nome del cluster in modo che sia facilmente intuibile che il contenitore viene utilizzato per tale cluster specifico.

	- __Località__: l'area geografica in cui si trova o in cui verrà creato l'account di archiviazione.

		> [AZURE.IMPORTANT]La selezione del percorso per l'origine dati predefinito imposterà anche il percorso del cluster HDInsight. L'origine dati del cluster e l’origine dati predefinita devono trovarsi nella stessa area.

	- __Seleziona__: usare questa opzione per salvare la configurazione dell'origine dati.

	
8. Selezionare __Piani tariffari per il nodo__ per visualizzare informazioni sui nodi che verranno creati per questo cluster. Per impostazione predefinita, il numero di nodi di lavoro verrà impostato su __4__.


	Verrà visualizzato il costo stimato del cluster in fondo al pannello.

	![Pannello livelli dei prezzi di nodo](./media/hdinsight-administer-use-portal-linux/nodepricingtiers.png)

	Usare il pulsante __Seleziona__ per salvare le informazioni sui __Piani tariffari per il nodo__.

9. Selezionare __Configurazione facoltativa__. Questo pannello consente di configurare i seguenti elementi:

	* __Versione HDInsight__: la versione di HDInsight usata per il cluster. Per altre informazioni sulle versioni di HDInsight, vedere [Controllo delle versioni del componente HDInsight](hdinsight-component-versioning.md).

	* __Metastore esterni__: consente di selezionare un database SQL, che verrà usato per archiviare informazioni di configurazione per Oozie e Hive. In questo modo è possibile riutilizzare la configurazione quando si elimina e si ricrea un cluster, anziché dover ricreare la configurazione Hive e Oozie ogni volta.

	* __Rete virtuale__: consente di posizionare il cluster HDInsight nella stessa rete virtuale delle altre risorse, ad esempio il database SQL o una macchina virtuale di Azure. Se le risorse si trovano in una rete virtuale possono comunicare direttamente l'una con l'altra, ignorando i gateway pubblici che gestiscono il traffico in ingresso da Internet. Per altre informazioni sui vantaggi delle reti virtuali di Azure su HDInsight, vedere [Estendere le funzionalità di HDInsight usando Rete virtuale di Azure](hdinsight-extend-hadoop-virtual-network.md).

		> [AZURE.IMPORTANT]È necessario creare la rete virtuale di Azure prima di creare il cluster HDInsight, perché non è possibile creare una nuova rete dalla configurazione di HDInsight.
		>
		> Al momento (25/08/2015) è prevista una limitazione in base alla quale può essere presente un solo cluster HDInsight basato su Linux in una rete virtuale di Azure.
        >
        > Non è possibile utilizzare una rete virtuale di Azure v1 (classica) con HDInsight basato su Linux. La rete virtuale deve essere v2 (Gestione risorse di Azure) per poter essere elencata come opzione durante il processo di creazione di cluster HDInsight nel portale di anteprima di Azure o possa essere usata durante la creazione di un cluster mediante l'interfaccia della riga di comando di Azure o Azure PowerShell.
        >
        > Se si dispone di risorse in una rete v1 e si desidera rendere HDInsight direttamente accessibile a tali risorse attraverso una rete virtuale, vedere [Connessione di reti virtuali classiche a nuove reti virtuali](../virtual-network/virtual-networks-arm-asm-s2s.md) per informazioni su come connettersi una rete virtuale v2 a una rete virtuale v1. Una volta stabilita la connessione, è possibile creare il cluster HDInsight nella rete virtuale v2.

	* __Azioni script__: consente di specificare script Bash per personalizzare il cluster HDInsight durante il provisioning. Ad esempio, è disponibile uno [script che installa Hue](hdinsight-hadoop-hue-linux.md), un client grafico per l'utilizzo di Hadoop. Per altre informazioni sulle azioni script, vedere [Personalizzare cluster HDInsight mediante l'azione script](hdinsight-hadoop-customize-cluster-linux.md).

	* __Chiavi di archiviazione di Azure__: consentono di associare account di archiviazione aggiuntivi al server HDInsight.

		> [AZURE.NOTE]HDInsight può accedere solo agli account di archiviazione di Azure utilizzati come archivio di dati predefinito, aggiunti in questa sezione di configurazione, o accessibili pubblicamente.

	![Pannello configurazione facoltativa](./media/hdinsight-administer-use-portal-linux/optionalconfiguration.png)

10. Assicurarsi che __Aggiungi alla Schermata iniziale__ sia selezionato e quindi selezionare __Crea__. Questo creerà il cluster e aggiungerà una sezione apposita nella schermata iniziale del portale di Azure. L'icona indica che il cluster sta eseguendo il provisioning e verrà visualizzata l'icona di HDInsight, una volta completato il provisioning.

	| Durante il provisioning | Provisioning completato |
	| ------------------ | --------------------- |
	| ![Indicatore del provisioning sulla schermata iniziale](./media/hdinsight-administer-use-portal-linux/provisioning.png) | ![Sezione del cluster su cui è stato effettuato il provisioning](./media/hdinsight-administer-use-portal-linux/provisioned.png) |

	> [AZURE.NOTE]La creazione del cluster richiederà del tempo, in genere circa 15 minuti. Utilizzare il riquadro sulla schermata iniziale, o la voce __Notifiche__ a sinistra della pagina per controllare il processo di provisioning.

## Gestire un cluster

Selezionando un cluster dal portale di anteprima di Azure verranno visualizzate le informazioni essenziali sul cluster, ad esempio il nome, il gruppo di risorse, il sistema operativo e l'URL per il dashboard del cluster (utilizzato per accedere ad Ambari Web per i cluster Linux).

![Dettagli cluster](./media/hdinsight-administer-use-portal-linux/clusterdetails.png)

Utilizzare quanto segue per comprendere le icone nella parte superiore di questo pannello e nelle sezioni __Essentials__ e __Collegamenti rapidi__:

* __Impostazioni__ e __Tutte le impostazioni__: consentono di visualizzare il pannello__Impostazioni__ per il cluster, che consente di accedere a informazioni dettagliate sulla configurazione del cluster.

* __Dashboard__, __Dashboard del cluster__ e __URL__: sono modi per accedere al dashboard del cluster, che per i cluster basati su Linux è Ambari Web.

* __Secure Shell__: informazioni necessarie per accedere al cluster tramite SSH.

* __Scala Cluster__: consente di modificare il numero di nodi del ruolo di lavoro per questo cluster.

* __Elimina__: consente di eliminare il cluster HDInsight.

* __Guide rapide__ (![icona cloud e thunderbolt = guida rapida](./media/hdinsight-administer-use-portal-linux/quickstart.png)): Visualizza le informazioni che consentiranno di iniziare a utilizzare HDInsight.

* __Utenti__ (![icona utenti](./media/hdinsight-administer-use-portal-linux/users.png)): consente di impostare le autorizzazioni per _Gestione portale_ di questo cluster per altri utenti nella sottoscrizione Azure.

	> [AZURE.IMPORTANT]Questo influisce _solo_ sull'accesso e sulle autorizzazioni per tale cluster nel portale di anteprima di Azure e non ha alcun effetto su chi può connettersi o inviare processi al cluster HDInsight.

* __Tag__ (![icona tag](./media/hdinsight-administer-use-portal-linux/tags.png)): consente di impostare coppie chiave/valore per definire una tassonomia dei servizi cloud personalizzata. Ad esempio, è possibile creare una chiave denominata __progetto__, e utilizzare un valore comune per tutti i servizi associati a un progetto specifico.

* __Documentazione__: collegamenti alla documentazione per Azure HDInsight.

> [AZURE.IMPORTANT]Per gestire i servizi forniti dal cluster HDInsight, è necessario utilizzare Ambari Web o l'API REST di Ambari. Per altre informazioni sull'uso di Ambari, vedere [Gestire i cluster HDInsight tramite Ambari](hdinsight-hadoop-manage-ambari.md).

### <a name="scaling"></a>Ridimensionamento

Per ridimensionare un cluster tramite il portale, selezionare il cluster HDInsight e quindi __Ridimensiona cluster__. Specificare il __Numero di nodi del ruolo di lavoro__ da impostare per il cluster e quindi fare clic su __Salva__.

![immagine dell'interfaccia utente di ridimensionamento](./media/hdinsight-administer-use-portal-linux/scaling.png)

Per altre informazioni sulle operazioni di ridimensionamento, vedere [Informazioni sull'uso di HDInsight in Linux](hdinsight-hadoop-linux-information.md#scaling).

## Monitorare un cluster

La sezione __Utilizzo__ del pannello del cluster HDInsight visualizza informazioni relative al numero di core disponibili per la sottoscrizione da usare con HDInsight, il numero di core allocati al cluster e il modo in cui vengono allocati per i nodi nel cluster.

![Informazioni sull'utilizzo](./media/hdinsight-administer-use-portal-linux/usage.png)

> [AZURE.IMPORTANT]Per monitorare i servizi forniti dal cluster HDInsight, è necessario utilizzare Ambari Web o l'API REST di Ambari. Per altre informazioni sull'uso di Ambari, vedere [Gestire i cluster HDInsight tramite Ambari](hdinsight-hadoop-manage-ambari.md)

## Passaggi successivi
In questo articolo è stato illustrato come creare un cluster HDInsight tramite il portale di Azure e come aprire lo strumento da riga di comando di Hadoop. Per altre informazioni, vedere gli articoli seguenti:

* [Amministrare HDInsight tramite Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Amministrare HDInsight tramite l’interfaccia della riga di comando di Azure](hdinsight-administer-use-command-line.md)
* [Effettuare il provisioning di cluster HDInsight](hdinsight-provision-clusters.md)
* [Inviare processi Hadoop a livello di codice](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Introduzione ad Azure HDInsight](../hdinsight-get-started.md)
* [Versione di Hadoop inclusa in Azure HDInsight](hdinsight-component-versioning.md)

[preview-portal]: https://portal.azure.com

<!---HONumber=Oct15_HO1-->