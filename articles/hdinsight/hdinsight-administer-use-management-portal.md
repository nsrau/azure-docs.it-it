<properties
	pageTitle="Gestire cluster Hadoop in HDInsight tramite il portale di anteprima di Azure | Microsoft Azure"
	description="Informazioni su come amministrare il servizio HDInsight. Creare un cluster HDInsight, aprire la console interattiva JavaScript e aprire la console dei comandi di Hadoop."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="jgao"/>

# Gestire cluster Hadoop in HDInsight tramite il portale di anteprima di Azure

[AZURE.INCLUDE [selettore](../../includes/hdinsight-portal-management-selector.md)]

Tramite il [portale di anteprima di Azure][azure-portal] è possibile effettuare il provisioning di cluster Hadoop in Azure HDInsight, modificare la password utente di Hadoop e abilitare RDP (Remote Desktop Protocol) per accedere alla console dei comandi di Hadoop nel cluster.

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Gestire cluster Hadoop in HDInsight tramite il portale di Azure](hdinsight-administer-use-management-portal-v1.md)

> [AZURE.NOTE]I passaggi descritti in questo documento sono specifici per i cluster basati su Windows. Per informazioni sull'utilizzo dei cluster basati su Linux, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di anteprima di Azure](hdinsight-administer-use-portal-linux.md)



> [AZURE.NOTE]Oltre al portale di anteprima di Azure sono disponibili altri strumenti per amministrare HDInsight.
>
> - [Amministrare HDInsight tramite Azure PowerShell](hdinsight-administer-use-powershell.md)
> - [Amministrare HDInsight tramite l’interfaccia della riga di comando di Azure](hdinsight-administer-use-command-line.md)

**Prerequisiti**

Per eseguire le procedure descritte nell'articolo è necessario:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Account di archiviazione di Azure**: cluster HDInsight usano un contenitore di archiviazione BLOB di Azure come file system predefinito. Per altre informazioni sull'esperienza lineare offerta dall'archivio BLOB con cluster HDInsight, vedere [Usare l'archivio BLOB di Azure con HDInsight](../hdinsight-use-blob-storage.md). Per dettagli sulla creazione di un account di archiviazione di Azure, vedere [Come creare un account di archiviazione](../storage-create-storage-account.md).


##Provisioning di cluster HDInsight

Per le istruzioni di provisioning tramite il portale di anteprima, vedere [Provisioning dei cluster HDInsight](hdinsight-provision-clusters.md#portal).

##Personalizzare il provisioning di cluster HDInsight

HDInsight è compatibile con una vasta gamma di componenti Hadoop. Per l'elenco dei componenti verificati e supportati, vedere [Versione di Hadoop inclusa in Azure HDInsight](hdinsight-component-versioning.md). Per personalizzare HDInsight, è possibile usare una delle opzioni seguenti:

- Usare azioni script per eseguire script personalizzati in grado di personalizzare un cluster per modificare la configurazione del cluster o installare componenti personalizzati, ad esempio Giraph o Solr. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster.md).
- Usare i parametri di personalizzazione del cluster in HDInsight .NET SDK o di Azure PowerShell durante il provisioning del cluster. Le modifiche alla configurazione vengono quindi mantenute per tutta la durata del cluster e non vengono interessate dalla ricreazione delle immagini dei nodi del cluster che la piattaforma Azure esegue periodicamente per la manutenzione. Per altri informazioni sull'uso dei parametri di personalizzazione dei cluster, vedere [Provisioning di cluster HDInsight](hdinsight-provision-clusters.md).
- Nel cluster è possibile eseguire alcuni componenti Java nativi, come Mahout e Cascading, sotto forma di file JAR. Tali file JAR possono essere distribuiti nell'archivio BLOB di Azure e inviati ai cluster HDInsight usando i meccanismi di invio dei processi Hadoop. Per altre informazioni, vedere [Inviare processi Hadoop a livello di codice](hdinsight-submit-hadoop-jobs-programmatically.md).


	>[AZURE.NOTE]In caso di problemi durante la distribuzione di file JAR in cluster HDInsight o la chiamata di file JAR in cluster HDInsight, contattare il [Supporto tecnico Microsoft](http://azure.microsoft.com/support/options/).

	> Cascading non è supportato da HDInsight, pertanto in caso di problemi non è possibile rivolgersi al Supporto Microsoft. Per gli elenchi dei componenti supportati, vedere [Novità delle versioni cluster incluse con HDInsight](hdinsight-component-versioning.md).


L'installazione di software personalizzato nel cluster tramite Connessione Desktop remoto non è supportata. È consigliabile evitare di archiviare file nelle unità del nodo head in quanto andranno perse qualora fosse necessario ricreare i cluster. È consigliabile archiviare i file nell'archivio BLOB di Azure. L'archivio BLOB è persistente.

## Acquisire familiarità con l'interfaccia del portale cluster

**Per accedere al cluster**

1. Accedere al [portale di anteprima][azure-portal].
2. Fare clic su **Esplora tutto** dal menu a sinistra per aprire il pannello **Sfoglia**.
3. Fare clic su **Cluster HDInsight** per aprire il pannello **Cluster HDInsight**.
4. Fare clic su uno dei cluster dall'elenco per aprire il pannello cluster:

	![Portale di HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.blade.png)

5. Fare clic su **Impostazioni** per visualizzare i dettagli di configurazione e per configurare il cluster:

	![Impostazioni del cluster del portale HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

	|Impostazione|Descrizione|
	|-----|-----|
	|Proprietà| Visualizzare le proprietà del cluster.|
	|Chiavi di archiviazione di Azure| Visualizzare le informazioni sull'account di archiviazione Azure predefinito. |
	|Account di accesso del cluster| Concedere/richiamare l’accesso ai servizi web HTTP e configurare le informazioni di accesso del cluster. |
	|Metastore esterni| Visualizzare le informazioni del metastore Hive/Oozie.|
	|Cluster di scala| Aumentare/diminuire il numero di nodi dei ruoli di lavoro del cluster.|
	|Desktop remoto| Attivare/disattivare la connettività di Desktop remoto, connettersi al cluster tramite Desktop remoto.|


##Concedere/revocare l'accesso ai servizi HTTP

Per i cluster HDInsight sono disponibili i servizi Web HTTP seguenti (tutti con endpoint RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

Per impostazione predefinita, a questi servizi è concesso l'accesso. È possibile revocare/concedere l'accesso dal portale di Azure.

>[AZURE.NOTE]La concessione/revoca dell'accesso implica la reimpostazione del nome utente e della password del cluster.

**Per concedere/revocare l'accesso ai servizi Web HTTP**

1. Accedere al [portale di anteprima][azure-portal].
2. Fare clic su **Esplora tutto** dal menu a sinistra, fare clic su **Cluster HDInsight**, e poi sul nome del cluster.
3. Fare clic su **Impostazioni** dal menu principale, quindi fare clic su **Account di accesso del cluster**.
5. Fare clic su **ON** o **OFF** su **Account di accesso del cluster accesso**.  
6. Per **Nome utente di accesso al cluster** e **Password di accesso al cluster** immettere rispettivamente il nuovo nome utente e la nuova password per il cluster.
7. Fare clic su **SAVE**.

	![hdinsight rimozione complessiva dell’accesso al servizio web http](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)


È possibile effettuare questa operazione anche con i cmdlet di Azure PowerShell:

- Grant-AzureHDInsightHttpServicesAccess
- Revoke-AzureHDInsightHttpServicesAccess

Vedere [Amministrare cluster Hadoop in HDInsight tramite Azure PowerShell](hdinsight-administer-use-powershell.md)

##Modifica di nome utente e password di cluster HDInsight
Per un cluster HDInsight possono esistere due account utente. L'account utente del cluster HDInsight viene creato durante il processo di provisioning. È anche possibile creare un account utente RDP per accedere al cluster tramite RDP. Vedere la sezione relativa all'[abilitazione di Desktop remoto](#connect-to-hdinsight-clusters-by-using-rdp).

**Per modificare il nome utente e la password di un cluster HDInsight**

1. Accedere al [portale di anteprima][azure-portal].
2. Fare clic su **Esplora tutto** dal menu a sinistra, fare clic su **Cluster HDInsight**, e poi sul nome del cluster.
3. Fare clic su **Impostazioni** dal menu principale, quindi fare clic su **Account di accesso del cluster**.
4. Modificare il **nome account di accesso del Cluster** e/o la**Password di accesso del Cluster**, quindi fare clic su **Salva**.

	![hdinsight modificare il nome utente e la password dell’utente http del cluster](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

##Ridimensionare i cluster
La funzionalità di scalabilità del cluster consente di modificare il numero di nodi del ruolo di lavoro usati da un cluster in esecuzione in Azure HDInsight senza dover ricreare il cluster.

>[AZURE.NOTE]Sono supportati solo i cluster con HDInsight versione 3.1.3 o successive. Se non si è certi della versione del cluster, è possibile controllare la pagina delle proprietà. Vedere [Acquisire familiarità con l'interfaccia del portale cluster](hdinsight-adminster-use-management-portal/#Get-familiar-with-the-cluster-portal-interface).

Questa sezione descrive l'impatto della modifica del numero di nodi dati per ogni tipo di cluster supportato da HDInsight:

- Hadoop

	È possibile aumentare facilmente il numero di nodi del ruolo di lavoro in un cluster Hadoop in esecuzione senza conseguenze per eventuali processi in sospeso o in esecuzione. È inoltre possibile inviare nuovi processi mentre è in corso l'operazione. Gli errori in un'operazione di scalabilità vengono gestiti in modo che il cluster rimanga sempre in uno stato funzionale.

	Quando un cluster Hadoop viene ridimensionato riducendo il numero di nodi dati, alcuni dei servizi del cluster vengono riavviati. In questo modo, tutti i processi in esecuzione e in attesa daranno esito negativo dopo il completamento dell'operazione di ridimensionamento. È tuttavia possibile inviare nuovamente i processi una volta completata l'operazione.

- HBase

	È possibile aggiungere o rimuovere facilmente nodi nel cluster HBase mentre è in esecuzione. I server a livello di area vengono bilanciati automaticamente entro pochi minuti dal completamento dell'operazione di ridimensionamento. È tuttavia possibile anche bilanciare manualmente i server a livello di area accedendo al nodo head del cluster ed eseguendo i comandi seguenti da una finestra del prompt dei comandi:

		>pushd %HBASE_HOME%\bin
		>hbase shell
		>balancer

	Per altre informazioni sull'uso della shell HBase, vedere
- Storm

	È possibile aggiungere o rimuovere facilmente nodi dati dal cluster Storm mentre è in esecuzione. Tuttavia, dopo il completamento dell'operazione di ridimensionamento, è necessario bilanciare nuovamente la topologia.

	A tale scopo, è possibile scegliere tra due opzioni:

	* Interfaccia utente Web di Storm
	* Interfaccia della riga di comando (CLI)

	Per altre informazioni, fare riferimento alla [documentazione su Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

	L'interfaccia utente Web di Storm è disponibile nel cluster HDInsight:

	![hdinsight scala ribilanciamento di storm](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

	Di seguito viene fornito un esempio d'uso del comando CLI per ribilanciare la topologia di Storm:

		## Reconfigure the topology "mytopology" to use 5 worker processes,
		## the spout "blue-spout" to use 3 executors, and
		## the bolt "yellow-bolt" to use 10 executors

		$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10


**Per ridimensionare i cluster**

1. Accedere al [portale di anteprima][azure-portal].
2. Fare clic su **Esplora tutto** dal menu a sinistra, fare clic su **Cluster HDInsight**, e poi sul nome del cluster.
3. Fare clic su **Impostazioni** dal menu principale, quindi fare clic su **Ridimensiona cluster**.
4. Immettere il **numero di nodi del ruolo di lavoro**. Il limite al numero dei nodi del cluster varia tra le diverse sottoscrizioni di Azure. Per aumentare il limite, contattare il team del supporto fatturazione. Le informazioni sui costi rifletteranno le modifiche apportate al numero di nodi.


	![hdinsight scalabilità hadoop hbase storm spark](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)



##Connettersi ai cluster HDInsight tramite RDP

Le credenziali del cluster fornite durante la creazione consentono di accedere ai servizi nel cluster, ma non al cluster stesso tramite Desktop remoto. È possibile attivare l'accesso Desktop remoto durante o dopo il provisioning di un cluster. Per istruzioni sull'abilitazione di Desktop remoto al momento del provisioning, vedere [Provisioning del cluster HDInsight](hdinsight-provision-clusters.md).

**Per abilitare Desktop remoto**

1. Accedere al [portale di anteprima][azure-portal].
2. Fare clic su **Esplora tutto** dal menu a sinistra, fare clic su **Cluster HDInsight**, e poi sul nome del cluster.
3. Fare clic su **Impostazioni** dal menu principale, quindi fare clic su **Desktop remoto**.
4. Immettere **Scadenza**, **Nome utente di Desktop remoto** e **Password di Desktop remoto**, quindi fare clic su **Abilita**.

	![hdinsight attivare disattivare configurare desktop remoto](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

	Il valore predefinito per Scadenza è una settimana.
> [AZURE.NOTE]Per abilitare Desktop remoto in un cluster è anche possibile usare HDInsight .NET SDK. Usare il metodo **EnableRdp** sull'oggetto client HDInsight nel modo seguente: **client.EnableRdp(clustername, location, "rdpuser", "rdppassword", DateTime.Now.AddDays(6))**. Analogamente, per disabilitare Desktop remoto nel cluster è possibile usare **client.DisableRdp(clustername, location)**. Per altre informazioni su questi metodi, vedere [Documentazione di riferimento relativa a HDInsight .NET SDK](http://go.microsoft.com/fwlink/?LinkId=529017). È applicabile solo per i cluster HDInsight in esecuzione in Windows.

**Per connettersi a un cluster tramite RDP**

1. Accedere al [portale di anteprima][azure-portal].
2. Fare clic su **Esplora tutto** dal menu a sinistra, fare clic su **Cluster HDInsight**, e poi sul nome del cluster.
3. Fare clic su **Impostazioni** dal menu principale, quindi fare clic su **Desktop remoto**.
4. Fare clic su **Connetti** e seguire le istruzioni. Se Connetti è disabilitato, è necessario prima abilitarlo. Assicurarsi di utilizzare il nome utente e la password del Desktop remoto. Non è possibile utilizzare le credenziali dell'utente del Cluster.

##Aprire la riga di comando di Hadoop.

Per connettersi al cluster tramite Desktop remoto e usare la riga di comando di Hadoop, è prima necessario aver abilitato l'accesso Desktop remoto al cluster come descritto nella sezione precedente.

**Per aprire la riga di comando di Hadoop**

1. Connettersi al cluster tramite Desktop remoto.
8. Dal desktop fare doppio clic su **Hadoop Command Line**.

	![HDI.HadoopCommandLine][image-hadoopcommandline]


	Per altre informazioni sui comandi Hadoop, vedere la [documentazione di riferimento sui comandi Hadoop](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

Nella schermata precedente il numero di versione di Hadoop è incorporato nel nome della cartella. Il numero di versione cambia in base alla versione dei componenti Hadoop installati nel cluster. È possibile usare le variabili d'ambiente di Hadoop per fare riferimento a tali cartelle. Ad esempio:

	cd %hadoop_home%
	cd %hive_home%
	cd %hbase_home%
	cd %pig_home%
	cd %sqoop_home%
	cd %hcatalog_home%


##Passaggi successivi
In questo articolo è stato illustrato come creare un cluster HDInsight tramite il portale di anteprima di Azure e come aprire lo strumento da riga di comando di Hadoop. Per altre informazioni, vedere gli articoli seguenti:

* [Amministrare HDInsight tramite Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Amministrare HDInsight tramite l’interfaccia della riga di comando di Azure](hdinsight-administer-use-command-line.md)
* [Effettuare il provisioning di cluster HDInsight](hdinsight-provision-clusters.md)
* [Inviare processi Hadoop a livello di codice](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Introduzione ad Azure HDInsight](../hdinsight-get-started.md)
* [Versione di Hadoop inclusa in Azure HDInsight](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-cluster-quickcreate]: ./media/hdinsight-administer-use-management-portal/HDI.QuickCreateCluster.png
[image-cluster-landing]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterLanding.PNG "Pagina di destinazione del cluster"
[image-hdi-create-rpd-user]: ./media/hdinsight-administer-use-management-portal/HDI.CreateRDPUser.png
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/HDI.HadoopCommandLine.PNG "Riga di comando di Hadoop"
[image-hdiclustercreate-uploadcert]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterCreate.UploadCert.png

<!---HONumber=August15_HO8-->