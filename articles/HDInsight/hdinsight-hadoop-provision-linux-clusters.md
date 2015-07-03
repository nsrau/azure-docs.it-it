<properties 
   pageTitle="Effettuare il provisioning di cluster Hadoop in Linux in HDInsight | Microsoft Azure" 
   description="Informazioni su come effettuare il provisioning di cluster Hadoop in Linux per HDInsight tramite il portale di gestione, la riga di comando e .NET SDK." 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/17/2015"
   ms.author="nitinme"/>


#Provisioning di cluster Hadoop Linux in HDInsight con opzioni personalizzate (anteprima)

Questo articolo descrive i diversi modi disponibili per effettuare il provisioning personalizzato di un cluster Hadoop Linux in Azure HDInsight usando il portale di Azure, PowerShell, gli strumenti da riga di comando o HDInsight .NET SDK.

## Informazioni sui cluster HDInsight

Il motivo per cui vengono menzionati i cluster ogni volta che si discute di Hadoop o Big Data è che Hadoop consente l'elaborazione distribuita di dati di grandi dimensioni tra diversi nodi di un cluster. Il cluster presenta un'architettura master/worker con un nodo master (detto anche nodo head o Name Node) e un numero indeterminato di nodi worker (detti anche nodi dati). Per altre informazioni, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>.

![Cluster HDInsight][img-hdi-cluster]


Un cluster HDInsight astrae i dettagli dell'implementazione di Hadoop in modo che non sia necessario preoccuparsi di come comunicare con diversi nodi di un cluster. Quando si effettua il provisioning di un cluster HDInsight, si effettua il provisioning delle risorse di calcolo di Azure che includono Hadoop e le applicazioni correlate. Per altre informazioni, vedere [Introduzione a Hadoop in HDInsight](hdinsight-hadoop-introduction.md). I dati per la varianza sono archiviati nell'archivio BLOB di Azure. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight](../hdinsight-use-blob-storage.md).


In questo articolo vengono fornite istruzioni sui diversi modi di effettuare il provisioning di un cluster. Per informazioni su come iniziare rapidamente a effettuare il provisioning di un cluster, vedere l'articolo di [introduzione all'uso di Azure HDInsight in Linux](../hdinsight-hadoop-linux-get-started.md).

**Prerequisiti**

Per eseguire le procedure descritte nell'articolo è necessario:

- Una sottoscrizione di Azure. Azure è una piattaforma basata su sottoscrizione. I cmdlet di Azure PowerShell per HDInsight eseguono le attività tramite la sottoscrizione. Per altre informazioni su come ottenere una sottoscrizione, vedere <a href="http://azure.microsoft.com/pricing/purchase-options/" target="_blank">Opzioni di acquisto</a>, <a href="http://azure.microsoft.com/pricing/member-offers/" target="_blank">Offerte per i membri</a> oppure <a href="http://azure.microsoft.com/pricing/free-trial/" target="_blank">Versione di valutazione gratuita</a>.
- Chiavi Secure Shell (SSH). Sono necessarie se si vuole accedere in modalità remota a un cluster Linux tramite SSH con una chiave invece che con una password. L'uso della chiave è il metodo consigliato, perché risulta più sicuro. Per istruzioni su come generare chiavi SSH, vedere gli articoli seguenti:
	-  Da un computer Linux: [Usare SSH con HDInsight basato su Linux (Hadoop) da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
	-  Da un computer Windows: [Usare SSH con HDInsight basato su Linux (Hadoop) da Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

## <a id="configuration"></a>Opzioni di configurazione

### Cluster in Linux

HDInsight consente di effettuare il provisioning di cluster Linux in Azure. Effettuare il provisioning di un cluster Linux se si ha familiarità con Linux o Unix, si esegue la migrazione da una soluzione Hadoop basata su Linux esistente o si desidera un metodo facile per l'integrazione con i componenti dell'ecosistema Hadoop sviluppati per Linux. Per altre informazioni su Azure HDInsight in Linux, vedere [Introduzione a Hadoop in HDInsight](hdinsight-hadoop-introduction.md).


### Risorse di archiviazione aggiuntive

Durante la configurazione, è necessario specificare un account di archiviazione BLOB di Azure e un contenitore predefinito. Questo viene usato come posizione di archiviazione predefinita dal cluster. In alternativa è possibile specificare BLOB aggiuntivi da associare al cluster.


Per altre informazioni sull'uso degli archivi BLOB secondari, vedere [Uso dell'archivio BLOB di Azure con HDInsight](../hdinsight-use-blob-storage.md).


### Metastore

Il metastore contiene informazioni su tabelle Hive, partizioni, schemi, colonne e così via. Queste informazioni vengono usate da Hive per individuare la posizione di archiviazione dei dati in HDFS (Hadoop Distributed File System) o nell'archivio BLOB di Azure per HDInsight. Per impostazione predefinita, Hive usa un database incorporato per l'archiviazione di queste informazioni.

Quando si effettua il provisioning di un cluster HDInsight, è possibile specificare un database SQL che contiene il metastore per Hive. Ciò consente di conservare le informazioni dei metadati quando si elimina un cluster, perché vengono archiviate esternamente nel database SQL.


> [AZURE.NOTE]Attualmente l'opzione per l'uso di metastore è disponibile solo durante il provisioning di HDInsight per Linux tramite .NET SDK. Per istruzioni, vedere la sezione relativa al [provisioning di cluster HDInsight in Linux con .NET SDK](#sdk).



## <a id="options"></a>Opzioni per il provisioning di un cluster HDInsight Linux

È possibile effettuare il provisioning di un cluster HDInsight Hadoop Linux da un computer Linux, ma anche da un computer Windows. La seguente tabella fornisce informazioni sulle opzioni di provisioning disponibili dai diversi sistemi operativi e collegamenti per accedere a istruzioni relative a ognuna di queste opzioni.

Provisioning di cluster Linux da un computer che esegue questo sistema operativo| Uso del portale di Azure | Uso dell'interfaccia della riga di comando di Azure | Uso di .NET SDK | Uso di Azure PowerShell
-----------------| ------------------------| -------------------| ---------- | ---------
Linux| Fare clic [qui](#portal) | Fare clic [qui](#cli)| Non applicabile | Non applicabile
Windows | Fare clic [qui](#portal) | Fare clic [qui](#cli) | Fare clic [qui](#sdk) | Fare clic [qui](#powershell)

### <a id="portal"></a>Uso del portale di Azure

I cluster HDInsight usano un contenitore dell'archivio BLOB di Azure come file system predefinito. Per creare un cluster HDInsight, è necessario un account di archiviazione di Azure nello stesso data center. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight](../hdinsight-use-blob-storage.md). Per dettagli sulla creazione di un account di archiviazione di Azure, vedere [Creare un account di archiviazione](../storage-create-storage-account.md).


> [AZURE.NOTE]Attualmente i cluster HDInsight Linux possono essere ospitati solo nelle aree seguenti: **Asia sudorientale**, **Europa settentrionale**, **Stati Uniti orientali** e **Stati Uniti centro-meridionali**.

**Per creare un cluster HDInsight con l'opzione di creazione personalizzata**

1. Accedere al [portale di Azure][azure-management-portal].
2. Fare clic su **+ NUOVO** nella parte inferiore della pagina, quindi su **SERVIZI DATI**, **HDINSIGHT** e infine su **CREAZIONE PERSONALIZZATA**.
3. Nella pagina **Dettagli cluster** digitare o selezionare i valori seguenti:

	![Fornire dettagli del cluster Hadoop HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page1.png)

    <table border='1'>
		<tr><th>Proprietà</th><th>Valore</th></tr>
		<tr><td>Nome del cluster</td>
			<td><p>Assegnare un nome al cluster. </p>
				<ul>
				<li>Il nome DNS (Domain Name System) deve iniziare e finire con un carattere alfanumerico e può contenere trattini.</li>
				<li>Il campo deve essere una stringa contenente da 3 a 63 caratteri.</li>
				</ul></td></tr>
		<tr><td>Tipo di cluster</td>
			<td>Selezionare <strong>Hadoop</strong>.</td></tr>
		<tr><td>Sistema operativo</td>
			<td>Selezionare <b>Ubuntu 12.04 LTS Preview</b> per effettuare il provisioning del cluster HDInsight in Linux. Per effettuare il provisioning di un cluster Windows, vedere <a href="http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/" target="_blank">Effettuare il provisioning di cluster Hadoop in Windows in HDInsight</a>.</td></tr>
		<tr><td>Versione HDInsight</td>
			<td>Scegliere la versione. Per HDInsight in Linux l'impostazione predefinita è HDInsight versione 3.2, che usa Hadoop 2.6.</td></tr>
		</table>

	Immettere o selezionare i valori come illustrato nella tabella, quindi fare clic sulla freccia destra.

4. Nella pagina **Configura cluster** immettere o selezionare i valori seguenti:

	![Fornire dettagli del cluster Hadoop HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page2.png)

	<table border="1">
	<tr><th>Nome</th><th>Valore</th></tr>
	<tr><td>Nodi di dati</td><td>Numero di nodi di dati che si vuole distribuire. Ai fini di test, creare un cluster a singolo nodo. <br />Il limite relativo alle dimensioni del cluster dipende dalla sottoscrizione di Azure. Per aumentare il limite, contattare il team del supporto fatturazione di Azure.</td></tr>
	<tr><td>Area/Rete virtuale</td><td><p>Selezionare la stessa area geografica specificata per l'account di archiviazione creato in precedenza. HDInsight richiede che l'account di archiviazione si trovi nella stessa area geografica. Più avanti nella configurazione sarà possibile selezionare un account di archiviazione situato nella stessa area geografica specificata qui.</p></td></tr>
	<tr><td>Dimensioni nodo head</td><td><p>Selezionare le dimensioni di una macchina virtuale (VM) per il nodo head.</p></td></tr>
	<tr><td>Dimensioni nodo dati</td><td><p>Selezionare le dimensioni di una VM per il nodo dati.</p></td></tr>
	</table>

	>[AZURE.NOTE]Il costo potrebbe variare in base alla scelta delle VM. HDInsight usa tutte VM di livello standard per i nodi del cluster. Per informazioni sul modo in cui le dimensioni delle VM influiscono sui prezzi, vedere <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Prezzi di HDInsight</a>.


5. Nella pagina **Configura utente cluster** digitare o scegliere il valore seguente:

    ![Fornire l'utente del cluster Hadoop HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page3.png)

    <table border='1'>
	<tr><th>Proprietà</th><th>Valore</th></tr>
	<tr><td>Password HTTP</td>
		<td>Specificare la password per l'utente HTTP predefinito, ovvero <strong>admin</strong>.</td></tr>
	<tr><td>Nome utente SSH</td>
		<td>Specificare il nome per l'utente SSH. Si userà questo nome utente per avviare una sessione SSH remota sui nodi del cluster HDInsight.</td></tr>
	<tr><td>Tipo autenticazione SSH</td>
		<td>Specificare se si vuole usare una password o una chiave SSH per autenticare un utente SSH.</td></tr>
	<tr><td>Password SSH</td>
		<td>Se si sceglie una password come tipo di autenticazione, specificare la password SSH per autenticare un utente SSH. Verrà richiesto di immettere questa password quando si tenta di avviare una sessione SSH nel computer Linux remoto.</td></tr>
	<tr><td>Chiave pubblica SSH</td>
		<td>Se si sceglie una chiave come tipo di autenticazione, specificare la chiave pubblica SSH che deve essere già stata generata. Quando si avvia una sessione SSH con un nodo nel cluster Linux, si userà la chiave privata associata a questa chiave pubblica.<br>
		Per istruzioni su come generare una chiave SSH in un computer Linux, fare clic <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-linux-use-ssh-unix/" target="_blank">qui</a>. Per istruzioni su come generare una chiave SSH in un computer Windows, fare clic <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/" target="_blank">qui</a>.
	</td></tr>
	<tr><td>Immettere metastore Hive/Oozie</td>
		<td>Selezionare questa casella di controllo per specificare un database SQL nello stesso data center del cluster, da usare come metastore Hive/Oozie. Se si seleziona questa casella di controllo, è necessario specificare i dettagli relativi al database SQL di Azure nelle pagine successive della procedura guidata. Ciò risulta utile se si vogliono conservare i metadati sui progetti Hive/Oozie anche dopo aver eliminato un cluster.</td></tr>
	</td></tr>
	</table>
> [AZURE.NOTE]È consigliabile usare l'autenticazione tramite chiave pubblica SSH con SSH, perché è più sicura rispetto all'autenticazione tramite password.

	Fare clic sulla freccia destra.

6. Nella pagina **Configura utente cluster** digitare o scegliere il valore seguente:

    ![Fornire l'utente del cluster Hadoop HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page4.png)

	Specificare un database SQL di Azure che verrà usato come metastore Hive/Oozie. È possibile specificare lo stesso database per entrambi i metastore Hive e Oozie. Questo database SQL deve trovarsi nello stesso data center del cluster HDInsight. Nella casella di riepilogo vengono elencati solo i database SQL nello stesso data center specificato nella pagina <strong>Dettagli cluster</strong>. Specificare anche il nome utente e la password per la connessione al database SQL di Azure selezionato.

    >[AZURE.NOTE]Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Windows Azure** fare clic su **Sì** e infine su **Salva**.

    Fare clic sulla freccia destra.


6. Nella pagina **Account di archiviazione** indicare i valori seguenti:


    ![Fornire l'account di archiviazione per il cluster Hadoop HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page5.png)

	<table border='1'>
		<tr><th>Proprietà</th><th>Valore</th></tr>
		<tr><td>Storage Account</td>
			<td>Specificare l'account di archiviazione di Azure che verrà usato come file system predefinito per il cluster HDInsight. È possibile scegliere una delle tre opzioni seguenti:
			<ul>
				<li><strong>Utilizzare l'archiviazione esistente</strong></li>
				<li><strong>Crea nuova archiviazione</strong></li>
				<li><strong>Utilizzare l'archiviazione da un'altra sottoscrizione</strong></li>
			</ul>
			</td></tr>
		<tr><td>Nome account</td>
			<td><ul>
				<li>Se si sceglie di usare l'archiviazione esistente, per il campo <strong>Nome account</strong> selezionare un account di archiviazione esistente. Nella casella di riepilogo vengono elencati solo gli account di archiviazione che si trovano nello stesso data center in cui si è scelto di effettuare il provisioning del cluster.</li>
				<li>Se si sceglie l'opzione <strong>Crea nuova archiviazione</strong> o <strong>Utilizzare l'archiviazione da un'altra sottoscrizione</strong>, è necessario indicare il nome dell'account di archiviazione.</li>
			</ul></td></tr>
		<tr><td>Chiave account</td>
			<td>Se si sceglie l'opzione <strong>Utilizzare l'archiviazione da un'altra sottoscrizione</strong>, specificare la chiave dell'account relativa a tale account di archiviazione.</td></tr>
		<tr><td>Contenitore predefinito</td>
			<td><p>Specificare il contenitore predefinito nell'account di archiviazione usato come file system predefinito per il cluster HDInsight. Se si sceglie <strong>Utilizzare l'archiviazione esistente</strong> per il campo <strong>Account di archiviazione</strong> e non ci sono contenitori in tale account, il contenitore verrà creato per impostazione predefinita con lo stesso nome del cluster. Se esiste già un contenitore con il nome del cluster, al nome del contenitore verrà aggiunto un numero di sequenza. Ad esempio, mycontainer1, mycontainer2 e così via. Tuttavia, se un account di archiviazione esistente ha un contenitore con un nome diverso da quello del cluster specificato, si potrà usare anche tale contenitore.</p>
            <p>Se si è scelto di creare una nuova risorsa di archiviazione da un'altra sottoscrizione di Azure è necessario specificare il nome del contenitore predefinito.</p>
        </td></tr>
		<tr><td>Account di archiviazione aggiuntivi</td>
			<td>HDInsight supporta più account di archiviazione. Un cluster può usare un numero illimitato di account di archiviazione aggiuntivi. Se tuttavia si crea un cluster tramite il portale di Azure, esiste un limite di sette dovuto ai vincoli dell'interfaccia utente. Per ogni account di archiviazione aggiuntivo specificato viene aggiunta un'altra pagina <strong>Account di archiviazione</strong> alla procedura guidata, in cui è possibile specificare le informazioni sull'account. Nella schermata precedente, ad esempio, è selezionato un account di archiviazione aggiuntivo, per cui viene aggiunta la pagina 5 alla finestra di dialogo.</td></tr>
	</table>

	Fare clic sulla freccia destra.

7. Se si è scelto di configurare un account di archiviazione aggiuntivo per il cluster, nella pagina **Account di archiviazione** immettere le informazioni relative all'account aggiuntivo:

	![Fornire dettagli di archiviazione aggiuntivi per il cluster HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page6.png)

    Anche in questo caso, è possibile scegliere risorse di archiviazione esistenti, creare nuove risorse di archiviazione o usare le risorse di archiviazione di un'altra sottoscrizione di Azure. La procedura che consente di ottenere i valori è simile al passaggio precedente.


    > [AZURE.NOTE]Dopo aver scelto un account di archiviazione di Azure per il cluster HDInsight, non è possibile eliminare l'account né usarlo per un altro cluster.


 	Dopo aver specificato l'account di archiviazione aggiuntivo, fare clic sul segno di spunta per avviare il provisioning del cluster.

###<a id="cli"></a> Uso della riga di comando multipiattaforma

Un'altra opzione per effettuare il provisioning di un cluster HDInsight è l'interfaccia della riga di comando di Azure per Mac, Linux e Windows. L'interfaccia della riga di comando di Azure viene implementata in Node.js. Può essere usata in tutte le piattaforme che supportano Node.js, incluse Windows, Mac e Linux. È possibile installare l'interfaccia della riga di comando dai percorsi seguenti:

- **Node.js SDK** - <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>
- **Interfaccia della riga di comando multipiattaforma per Mac, Linux e Windows** - <a href="https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>  

Per una guida generale sull'uso dell'interfaccia della riga di comando, vedere [Installare e configurare l'interfaccia della riga di comando multipiattaforma di Azure](../xplat-cli.md).

Le istruzioni seguenti illustrano come installare la riga di comando multipiattaforma in Linux e Windows e quindi come usare la riga di comando per effettuare il provisioning di un cluster.

- [Configurare la riga di comando multipiattaforma di Azure per Linux](#clilin)
- [Configurare la riga di comando multipiattaforma di Azure per Windows](#cliwin)
- [Effettuare il provisioning di cluster HDInsight tramite la riga di comando multipiattaforma](#cliprovision)

#### <a id="clilin"></a>Configurare la riga di comando multipiattaforma di Azure per Linux

Seguire queste procedure per configurare il computer Linux per l'uso degli strumenti da riga di comando di Azure:

- Installare l'interfaccia della riga di comando tramite Node.js Package Manager (NPM)
- Connettersi alla sottoscrizione di Azure

**Per installare l'interfaccia della riga di comando tramite NPM**

1.	Aprire una finestra del terminale nel computer Linux ed eseguire il seguente comando:

		sudo npm install -g https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.	Eseguire il comando seguente per verificare l'installazione:

		azure hdinsight -h

	È possibile usare l'opzione **-h** a livelli diversi per visualizzare le informazioni della Guida. ad esempio:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Per connettersi alla sottoscrizione di Azure**

Prima di usare l'interfaccia della riga di comando, è necessario configurare la connettività tra la workstation e Azure. Le informazioni relative alla sottoscrizione di Azure vengono usate dall'interfaccia della riga di comando per connettersi all'account dell'utente. Tali informazioni possono essere ottenute da Azure in un file di impostazioni di pubblicazione. Il file di impostazioni di pubblicazione può essere quindi importato come impostazione di configurazione locale persistente che verrà usata per le operazioni successive. Sarà necessario importare le impostazioni di pubblicazione una sola volta.


> [AZURE.NOTE]Il file di impostazioni di pubblicazione contiene informazioni riservate. Microsoft consiglia di eliminare il file o di eseguire altri passaggi per crittografare la cartella utente contenente il file. In Windows modificare le proprietà della cartella o usare Crittografia unità BitLocker.



1.	Aprire una finestra del terminale.
2.	Eseguire il comando seguente per accedere alla sottoscrizione di Azure:

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	Il comando avvia la pagina Web da cui scaricare il file delle impostazioni di pubblicazione. Se la pagina Web non si apre, fare clic sul collegamento nella finestra del terminale per aprire la pagina del browser e accedere al portale.

3.	Scaricare il file di impostazioni di pubblicazione nel computer.
4.	Nella finestra del prompt dei comandi eseguire il comando seguente per importare il file di impostazioni di pubblicazione:

		azure account import <path/to/the/file>

	
#### <a id="cliwin"></a>Configurare la riga di comando multipiattaforma per Windows

Eseguire le procedure seguenti per configurare il computer Windows per l'uso degli strumenti da riga di comando di Azure:

- Installare l'interfaccia della riga di comando tramite NPM o Windows Installer
- Scaricare e importare impostazioni di pubblicazione dell'account Azure


L'interfaccia della riga di comando può essere installata tramite NPM o Windows Installer. È consigliabile eseguire l'installazione usando solo una delle due opzioni.

**Per installare l'interfaccia della riga di comando tramite NPM**

1.	Passare a **www.nodejs.org**.
2.	Fare clic su **INSTALL** e seguire le istruzioni usando le impostazioni predefinite.
3.	Aprire **Prompt dei comandi** (o il **prompt dei comandi di Azure** o **Prompt dei comandi per gli sviluppatori per VS2012**) dalla workstation.
4.	Nella finestra del prompt dei comandi eseguire il comando seguente:

		npm install -g https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [AZURE.NOTE]Se viene visualizzato un errore che informa che il comando NPM non è stato trovato, verificare che i percorsi seguenti siano presenti nella variabile di ambiente **PATH**: <i>C:\\Programmi (x86)\\nodejs;C:\\Utenti[nomeutente]\\AppData\\Roaming\\npm</i> or <i>C:\\Programmi\\nodejs;C:\\Utenti[nomeutente]\\AppData\\Roaming\\npm</i>


5.	Eseguire il comando seguente per verificare l'installazione:

		azure hdinsight -h

	È possibile usare l'opzione **-h** a livelli diversi per visualizzare le informazioni della Guida. ad esempio:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Per installare l'interfaccia della riga di comando tramite Windows Installer**

1.	Passare a **http://azure.microsoft.com/downloads/**.
2.	Scorrere verso il basso fino alla sezione **Strumenti da riga di comando** e quindi fare clic sul collegamento relativo all'**interfaccia della riga di comando multipiattaforma** e seguire l'Installazione guidata piattaforma Web.

**Per scaricare e importare impostazioni di pubblicazione**

Prima di usare l'interfaccia della riga di comando, è necessario configurare la connettività tra la workstation e Azure. Le informazioni relative alla sottoscrizione di Azure vengono usate dall'interfaccia della riga di comando per connettersi all'account dell'utente. Tali informazioni possono essere ottenute da Azure in un file di impostazioni di pubblicazione. Il file di impostazioni di pubblicazione può essere quindi importato come impostazione di configurazione locale persistente che verrà usata per le operazioni successive. Sarà necessario importare le impostazioni di pubblicazione una sola volta.


> [AZURE.NOTE]Il file di impostazioni di pubblicazione contiene informazioni riservate. Microsoft consiglia di eliminare il file o di eseguire altri passaggi per crittografare la cartella utente contenente il file. In Windows modificare le proprietà della cartella o usare BitLocker.



1.	Aprire un prompt dei comandi.
2.	Per scaricare il file di impostazioni di pubblicazione, eseguire il comando seguente:

		azure account download


	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	Il comando avvia la pagina Web da cui scaricare il file delle impostazioni di pubblicazione.


3.	Al prompt fare clic su **Salva** e indicare un percorso in cui salvare il file.
5.	Nella finestra del prompt dei comandi eseguire il comando seguente per importare il file di impostazioni di pubblicazione:

		azure account import <path/to/the/file>

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	
#### <a id="cliprovision"></a>Effettuare il provisioning di cluster HDInsight tramite la riga di comando multipiattaforma

Le procedure seguenti sono necessarie per effettuare il provisioning di un cluster HDInsight usando la riga di comando multipiattaforma:

- Creare un account di archiviazione di Azure
- Provisioning di un cluster


**Per creare un account di archiviazione di Azure**

HDInsight usa un contenitore dell'archivio BLOB di Azure come file system predefinito. Per creare un cluster HDInsight, è necessario un account di archiviazione di Azure. L'account di archiviazione deve trovarsi nello stesso data center.

- Al prompt dei comandi eseguire il comando seguente per creare un account di archiviazione di Azure:

		azure storage account create [options] <StorageAccountName>


	Quando viene richiesto un percorso, selezionarne uno in cui sia possibile effettuare il provisioning di un cluster HDInsight Linux. La risorsa di archiviazione deve trovarsi nello stesso percorso del cluster HDInsight.


Per informazioni sulla creazione di un account di archiviazione di Azure tramite il portale di Azure, vedere [Creare, gestire o eliminare un account di archiviazione](../storage-create-storage-account.md).

Se si ha già un account di archiviazione, ma non si conoscono il nome dell'account e la chiave dell'account, è possibile usare i comandi seguenti per recuperare le informazioni:

	-- Lists Storage accounts
	azure storage account list

	-- Shows information for a Storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a Storage account
	azure storage account keys list <StorageAccountName>

Per i dettagli sull'acquisizione delle informazioni mediante il portale di Azure, vedere la sezione "Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione" di [Creare, gestire o eliminare un account di archiviazione](../storage-create-storage-account.md).

Un cluster HDInsight richiede anche un contenitore in un account di archiviazione. Se l'account di archiviazione fornito non ha già un contenitore, **azure hdinsight cluster create** richiede il nome di un contenitore e lo crea. Se tuttavia si vuole creare il contenitore prima, è possibile usare il comando seguente:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Dopo aver preparato l'account di archiviazione e il contenitore BLOB, è possibile creare un cluster.

**Per effettuare il provisioning di un cluster HDInsight**

- Nella finestra del prompt dei comandi eseguire il comando seguente:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <StorageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType linux --sshUserName <SSH username> --sshPassword <SSH user password>

	>[AZURE.NOTE]I valori specificati per **--userName** e **--password** riguardano l'utente di Hadoop. Per l'utente di Hadoop è necessario specificare sempre --userName come "admin".

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**Per effettuare il provisioning di un cluster HDInsight tramite un file di configurazione**

In genere, si effettua il provisioning di un cluster HDInsight, si eseguono i processi e quindi si elimina il cluster per ridurre il costo. L'interfaccia della riga di comando consente di salvare le configurazioni in un file, per poterle riutilizzare ogni volta che si effettua il provisioning di un cluster.

- Al prompt dei comandi eseguire i comandi seguenti:


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster. Make sure you specify --userName as "admin"
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType linux --sshUserName <SSH username> --sshPassword <SSH user password>

		#If required, include commands to use additional Blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#Run this command to create a cluster by using the config file
		azure hdinsight cluster create --config <file>


	>[AZURE.NOTE]Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Windows Azure** fare clic su **Sì** e infine su **Salva**.



	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Per elencare e mostrare i dettagli dei cluster**

- Utilizzare i comandi seguenti per elencare e mostrare i dettagli dei cluster:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Per eliminare un cluster**

- Utilizzare il comando seguente per eliminare un cluster:

		azure hdinsight cluster delete <ClusterName>

###<a id="powershell"></a> Uso di Azure PowerShell
Azure PowerShell è un ambiente di scripting potente che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. Questa sezione include le istruzioni su come effettuare il provisioning di un cluster HDInsight con Azure PowerShell. Per informazioni sulla configurazione di una workstation per l'esecuzione dei cmdlet PowerShell per HDInsight, vedere [Come installare e configurare Azure PowerShell](../install-configure-powershell.md). Per altre informazioni sull'uso di PowerShell con HDInsight, vedere [Amministrare HDInsight tramite PowerShell](hdinsight-administer-use-powershell.md). Per l'elenco dei cmdlet Windows PowerShell per HDInsight, vedere [Documentazione di riferimento dei cmdlet di HDInsight](http://msdn.microsoft.com/library/windowsazure/dn479228.aspx).

Le procedure seguenti sono necessarie per effettuare il provisioning di un cluster HDInsight con PowerShell:

- Creare un account di archiviazione di Azure
- Creare un contenitore BLOB di Azure
- Creare un cluster HDInsight

Per eseguire gli script, è possibile usare la console di Windows PowerShell o Windows PowerShell Integrated Scripting Environment (ISE).
 
HDInsight usa un contenitore dell'archivio BLOB di Azure come file system predefinito. Per creare un cluster HDInsight, sono necessari un account di archiviazione di Azure e un contenitore di archiviazione. L'account di archiviazione deve trovarsi nello stesso data center che include il cluster HDInsight. Attualmente i cluster HDInsight Linux possono essere ospitati solo nelle aree seguenti: **Asia sudorientale**, **Europa settentrionale**, **Stati Uniti orientali** e **Stati Uniti centro-meridionali**.

**Per connettersi all'account di Azure**

		Add-AzureAccount 

Verrà richiesto di immettere le credenziali dell'account Azure.

**Per creare un account di archiviazione di Azure**

		$storageAccountName = "<StorageAcccountName>"	# Provide a Storage account name
		$location = "<MicrosoftDataCenter>"				# For example, "West US"

		# Create an Azure Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

Se si ha già di un account di archiviazione, ma non si conosce il nome dell'account e la chiave dell'account, è possibile usare i comandi di Windows PowerShell seguenti per recuperare le informazioni:

		# List Storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a Storage account
		Get-AzureStorageKey "<StorageAccountName>"

**Per creare il contenitore di archiviazione BLOB di Azure**

		$storageAccountName = "<StorageAccountName>"	# Provide the Storage account name
		$containerName="<ContainerName>"				# Provide a container name

		# Create a storage context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
		                                       -StorageAccountKey $storageAccountKey  

		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

Dopo aver preparato l'account di archiviazione e il contenitore BLOB, è possibile creare un cluster.

**Per effettuare il provisioning di un cluster HDInsight**

I due parametri più importanti da impostare per il provisioning dei cluster Linux sono quelli in cui vengono specificati il tipo di sistema operativo e i dettagli dell'utente SSH:

- Assicurarsi di specificare il parametro **- OSType** come **Linux**.
- Per usare SSH per le sessioni remote nei cluster, è possibile specificare la password utente SSH oppure la chiave pubblica SSH. Se si specificano sia la password utente SSH e la chiave pubblica SSH, la chiave verrà ignorata. Per usare la chiave SSH per le sessioni remote, specificare una password SSH vuota quando viene richiesta.


		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created

		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system

		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        $containerName = $clusterName					  # Azure Blob container that is used as the default file system for the HDInsight cluster
		
		
		# Get the credentials for HTTP and SSH users for the cluster
		$clusterCredentials = Get-Credential            	  # Make sure you specify the username as "admin". This is the Hadoop user name and password for the cluster. You will use this account to connect to the cluster. 
        $sshCredentials = Get-Credential				      # SSH user name for the cluster. You will use this account to start an SSH session on the remote cluster.
		$sshPublicKey = "<SSH public key>"           

		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		
        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location} 

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $clusterCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes -Version "3.2" -OSType Linux -SshCredential $sshCredentials -SshPublicKey $sshPublicKey


>[AZURE.NOTE]I valori specificati per **$clusterCredentials** vengono usati per creare l'account utente Hadoop per il cluster. Questo account verrà usato per connettersi al cluster. I valori specificati per **$sshCredentials** vengono usati per creare l'utente SSH per il cluster. Questo account viene usato per avviare una sessione SSH remota nel cluster ed eseguire i processi. Se si usa l'opzione Creazione rapida del portale di Azure per effettuare il provisioning di un cluster, il nome utente Hadoop predefinito è "admin" e il nome utente SSH predefinito è "hdiuser".

Il completamento del provisioning del cluster può richiedere alcuni minuti.

![HDI.CLI.Provision][image-hdi-ps-provision]

**Per effettuare il provisioning di un cluster HDInsight con le opzioni di configurazione personalizzate**

Durante il provisioning di un cluster, è possibile usare altre opzioni di configurazione, come la connessione a più contenitori di archiviazione BLOB di Azure oppure l'uso di un database SQL di Azure per i metastore Hive e Oozie. Ciò consente di separare la durata dei dati e dei metadati da quella del cluster.

I due parametri più importanti da impostare per il provisioning dei cluster Linux sono quelli in cui vengono specificati il tipo di sistema operativo e i dettagli dell'utente SSH:

- Assicurarsi di specificare il parametro **- OSType** come **Linux**.
- Per usare SSH per le sessioni remote nei cluster, è possibile specificare la password utente SSH oppure la chiave pubblica SSH. Se si specificano sia la password utente SSH e la chiave pubblica SSH, la chiave verrà ignorata. Per usare la chiave SSH per le sessioni remote, specificare una password SSH vuota quando viene richiesta.


		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created
		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        		
		# Get the credentials for HTTP user, SSH user, and Hive/Oozie metastore databases for the cluster
		$clusterCredentials = Get-Credential            	  # Make sure you specify the username as "admin". This is the Hadoop user name and password for the cluster. You will use this account to connect to the cluster. 
        $sshCredentials = Get-Credential				      # SSH user name for the cluster. You will use this account to start an SSH session on the remote cluster.
		$sshPublicKey = "<SSH public key>"
		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"

		$storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
		$containerName_Default = $clusterName
		$storageAccountName_Add1 = "<AdditionalStorageAccountName>"

		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
		$oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

		# Get the Storage account keys
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

		# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName_Default | %{$_.Location}

		# Create a cluster configuration file
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes | Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default | Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 | Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore | Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore
		        
		# Create the cluster
		New-AzureHDInsightCluster -Name $clusterName -Config $config -Credential $clusterCredentials -Location $clusterLocation -Version "3.2" -OSType Linux -SshCredential $sshCredentials -SshPublicKey $sshPublicKey

>[AZURE.NOTE]Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Windows Azure** fare clic su **Sì** e infine su **Salva**.

Il completamento del provisioning del cluster può richiedere alcuni minuti.

![HDI.CLI.Provision][image-hdi-ps-config-provision]

###<a id="sdk"></a>Uso di HDInsight .NET SDK
HDInsight .NET SDK fornisce librerie client .NET che semplificano l'uso di HDInsight da un'applicazione .NET Framework.

Per effettuare il provisioning di un cluster HDInsight in Linux con l'SDK, è necessario eseguire le procedure seguenti:

- Installare HDInsight .NET SDK
- Creare un certificato autofirmato
- Creare un'applicazione console
- Eseguire l'applicazione


**Per installare HDInsight .NET SDK**

È possibile installare l'ultima build pubblicata dell'SDK da [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Le istruzioni verranno illustrate nella procedura successiva.

**Per creare un certificato autofirmato**

Creare un certificato autofirmato, installarlo nella workstation e caricarlo nella sottoscrizione di Azure. Per ottenere istruzioni, vedere [Creare un certificato autofirmato](http://go.microsoft.com/fwlink/?LinkId=511138).


**Per creare l'applicazione console di Visual Studio**

1. Aprire Visual Studio 2013.

2. Scegliere **Nuovo** dal menu **File**, quindi fare clic su **Progetto**.

3. In **Nuovo progetto** digitare o selezionare i valori seguenti:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Proprietà</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valore</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Categoria</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Modello</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Applicazione console</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
</table>

4. Fare clic su **OK** per creare il progetto.

5. Dal menu **Strumenti** fare clic su **Gestione pacchetti NuGet**, quindi su **Console di Gestione pacchetti**.

6. Eseguire il comando seguente nella console per installare i pacchetti:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Questo comando aggiunge librerie .NET e riferimenti ad esse nel progetto corrente di Visual Studio.

7. In Esplora soluzioni fare doppio clic su **Program.cs** per aprirlo.

8. Aggiungere le istruzioni using seguenti all'inizio del file:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. Nella funzione **Main()** copiare e incollare il codice seguente:

	> [AZURE.NOTE]Assicurarsi che l'area specificata sia una delle seguenti: **Asia sudorientale**, **Europa settentrionale**, **Stati Uniti orientali** e **Stati Uniti centro-meridionali**, ovvero le aree che possono ospitare cluster HDInsight Linux. Anche l'account di archiviazione specificato deve trovarsi nella stessa area.

        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";		# Make sure you specify this username as "admin"
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;
		string version = "<version>";
        string sshusername = "<ssh user name>";
        string sshpublickey = "<ssh public key>;
		

		// If required, provide details of the Hive and Oozie metastore that you want to configure. ServerName is the name of the server on which the SQL databases are provisioned. HiveStoreSqlDatabaseName and OozieStoreSqlDatabaseName are the names of databases created on the server. You can also use the same database for both Hive and Oozie metastores

		Metastore hiveMetastore = new Metastore("<ServerName>.database.windows.net", "<HiveStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");
        Metastore oozieMetastore = new Metastore("<ServerName>.database.windows.net", "<OozieStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");

        // Get the certificate object from certificate store by using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
        ClusterCreateParametersV2 clusterInfo = new ClusterCreateParametersV2()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
			Version = version,
            OSType = OSType.Linux,
            SshUserName = sshusername,
            SshPublicKey = sshpublickey,
			HiveMetastore = hiveMetastore,		//Only if you created a hivemetastore object earlier
            OozieMetastore = oozieMetastore		//Only if you created an ooziemetastore object earlier
        };

		// Configure Hive and Oozie if you opted for the metastores earlier
		clusterInfo.HiveConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("datanucleus.connectionPoolingType", "none"));
		clusterInfo.OozieConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("oozie.service.AuthorizationService.security.enabled", "false"));
        

		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. Sostituire le variabili all'inizio della funzione **Main()**,

**Per eseguire l'applicazione**

Mentre l'applicazione è aperta in Visual Studio, premere **F5** per eseguirla. Verrà aperta una finestra della console in cui è visualizzato lo stato dell'applicazione. Possono essere necessari alcuni minuti per creare un cluster HDInsight.



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
[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx

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




[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "Elenco e visualizzazione di cluster"

[image-hdi-ps-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.provision.png
[image-hdi-ps-config-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.config.provision.png

[img-hdi-cluster]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.png

[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Utilizzo di Sqoop con HDInsight."

<!--HONumber=54--> 