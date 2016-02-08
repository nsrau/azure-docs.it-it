<properties
	pageTitle="Gestire cluster Hadoop in HDInsight tramite il portale di Azure classico | Microsoft Azure"
	description="Informazioni su come amministrare il servizio HDInsight. Creare un cluster HDInsight, aprire la console interattiva JavaScript e aprire la console dei comandi di Hadoop."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/04/2015"
	ms.author="jgao"/>

# Gestire cluster Hadoop in HDInsight tramite il portale di Azure classico

Il [portale di Azure classico](https://manage.windowsazure.com) permette di effettuare il provisioning di cluster Hadoop in Azure HDInsight, modificare la password utente di Hadoop e abilitare RDP (Remote Desktop Protocol) per accedere alla console dei comandi di Hadoop nel cluster.

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Gestire cluster Hadoop in HDInsight tramite il portale di Azure](hdinsight-administer-use-management-portal.md)

## Altri strumenti per l'amministrazione di HDInsight
Oltre al portale di Azure sono disponibili altri strumenti per amministrare HDInsight.

- Per altre informazioni sull'amministrazione di HDInsight tramite Azure PowerShell, vedere [Amministrare HDInsight tramite Azure PowerShell](hdinsight-administer-use-powershell.md).

- Per altre informazioni sull'amministrazione di HDInsight tramite l’interfaccia della riga di comando di Azure, vedere [Amministrare HDInsight tramite l’interfaccia della riga di comando di Azure](hdinsight-administer-use-command-line.md).

##Prerequisiti

Per eseguire le procedure descritte nell'articolo è necessario:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Account di archiviazione di Azure**: cluster HDInsight usano un contenitore di archiviazione BLOB di Azure come file system predefinito. Per altre informazioni sull'esperienza lineare offerta dall'archivio BLOB con cluster HDInsight, vedere [Usare l'archivio BLOB di Azure con HDInsight](../hdinsight-use-blob-storage.md). Per dettagli sulla creazione di un account di archiviazione di Azure, vedere [Come creare un account di archiviazione](../storage-create-storage-account.md).


##Provisioning di cluster HDInsight

È possibile effettuare il provisioning di cluster HDInsight dal portale di Azure classico tramite l'opzione Creazione rapida o Creazione personalizzata. Per istruzioni, vedere i collegamenti seguenti:

- [Effettuare il provisioning di un cluster con Creazione rapida](../hdinsight-get-started.md#provision)
- [Effettuare il provisioning di un cluster con Creazione personalizzata](hdinsight-provision-clusters.md#portal)

[AZURE.INCLUDE [Elenco di data center](../../includes/hdinsight-pricing-data-centers-clusters.md)]


##Personalizzazione di cluster HDInsight

HDInsight è compatibile con una vasta gamma di componenti Hadoop. Per l'elenco dei componenti verificati e supportati, vedere [Versione di Hadoop inclusa in Azure HDInsight](hdinsight-component-versioning.md). Per personalizzare HDInsight, è possibile usare una delle opzioni seguenti:

- Usare azioni script per eseguire script personalizzati in grado di personalizzare un cluster per modificare la configurazione del cluster o installare componenti personalizzati, ad esempio Giraph o Solr. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster.md).
- Usare i parametri di personalizzazione del cluster in HDInsight .NET SDK o di Azure PowerShell durante il provisioning del cluster. Le modifiche alla configurazione vengono quindi mantenute per tutta la durata del cluster e non vengono interessate dalla ricreazione delle immagini dei nodi del cluster che la piattaforma Azure esegue periodicamente per la manutenzione. Per altri informazioni sull'uso dei parametri di personalizzazione dei cluster, vedere [Provisioning di cluster HDInsight](hdinsight-provision-clusters.md).
- Nel cluster è possibile eseguire alcuni componenti Java nativi, come Mahout e Cascading, sotto forma di file JAR. Tali file JAR possono essere distribuiti nell'archivio BLOB di Azure e inviati ai cluster HDInsight usando i meccanismi di invio dei processi Hadoop. Per altre informazioni, vedere [Inviare processi Hadoop a livello di codice](hdinsight-submit-hadoop-jobs-programmatically.md).


	>[AZURE.NOTE] In caso di problemi durante la distribuzione di file JAR in cluster HDInsight o la chiamata di file JAR in cluster HDInsight, contattare il [Supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).

	> Cascading non è supportato da HDInsight, pertanto in caso di problemi non è possibile rivolgersi al Supporto Microsoft. Per gli elenchi dei componenti supportati, vedere [Novità delle versioni cluster incluse con HDInsight](hdinsight-component-versioning.md).


L'installazione di software personalizzato nel cluster tramite Connessione Desktop remoto non è supportata. È consigliabile evitare di archiviare file nelle unità del nodo head in quanto andranno perse qualora fosse necessario ricreare i cluster. È consigliabile archiviare i file nell'archivio BLOB di Azure. L'archivio BLOB è persistente.

##Modificare il nome utente e la password di un cluster HDInsight
Per un cluster HDInsight possono esistere due account utente. L'account utente del cluster HDInsight viene creato durante il processo di provisioning. È anche possibile creare un account utente RDP per accedere al cluster tramite RDP. Vedere la sezione relativa all'[abilitazione di Desktop remoto](#connect-to-hdinsight-clusters-by-using-rdp).

**Per modificare il nome utente e la password di un cluster HDInsight**

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/).
2. Fare clic su **HDINSIGHT** nel riquadro sinistro. Verrà visualizzato un elenco dei cluster HDInsight distribuiti.
3. Fare clic sul cluster HDInsight per cui si vuole reimpostare il nome utente e la password.
4. Fare clic su **CONFIGURAZIONE** nella parte superiore della pagina.
5. Fare clic su **DISATTIVA** accanto a **SERVIZI HADOOP**.
6. Fare clic su **SALVA** nella parte inferiore della pagina e attendere il completamento dell'operazione di disabilitazione.
7. Dopo la disabilitazione del servizio, fare clic su **ATTIVA** accanto a **SERVIZI HADOOP**.
8. Per **NOME UTENTE** e **NUOVA PASSWORD** immettere rispettivamente il nuovo nome utente e la nuova password per il cluster.
8. Fare clic su **SALVA**.


##Connettersi ai cluster HDInsight tramite RDP

Le credenziali del cluster fornite durante la creazione consentono di accedere ai servizi nel cluster, ma non al cluster stesso tramite Desktop remoto. L'accesso a Desktop remoto è disattivato per impostazione predefinita, quindi per l'accesso diretto al cluster sono necessarie altre operazioni di configurazione successive alla creazione.

**Per abilitare Desktop remoto**

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/).
2. Fare clic su **HDINSIGHT** nel riquadro sinistro. Verrà visualizzato un elenco dei cluster HDInsight distribuiti.
3. Fare clic sul cluster HDInsight a cui connettersi.
4. Fare clic su **CONFIGURAZIONE** nella parte superiore della pagina.
5. Nella parte inferiore della pagina fare clic su **ABILITA MODALITÀ REMOTA**.
6. Nella procedura guidata **Configura desktop remoto** immettere un nome utente e una password per Desktop remoto. Si noti che il nome utente deve essere diverso da quello usato per creare il cluster (**admin** per impostazione predefinita con l'opzione Creazione rapida). Immettere una data di scadenza nella casella **EXPIRES ON**. La data di scadenza deve ricorrere nel futuro e non essere successiva a 90 giorni rispetto alla data odierna. Per impostazione predefinita, si presuppone che l'ora di scadenza del giorno sia la mezzanotte della data specificata. Fare clic sull'icona del segno di spunta.

	![HDI.CreateRDPUser][image-hdi-create-rpd-user]


> [AZURE.NOTE] Per abilitare Desktop remoto in un cluster è anche possibile usare HDInsight .NET SDK. Usare il metodo **EnableRdp** sull'oggetto client HDInsight nel modo seguente: **client.EnableRdp(clustername, location, "rdpuser", "rdppassword", DateTime.Now.AddDays(6))**. Analogamente, per disabilitare Desktop remoto nel cluster è possibile usare **client.DisableRdp(clustername, location)**. Per altre informazioni su questi metodi, vedere [Documentazione di riferimento relativa a HDInsight .NET SDK](http://go.microsoft.com/fwlink/?LinkId=529017). È applicabile solo per i cluster HDInsight in esecuzione in Windows.



> [AZURE.NOTE] Dopo aver abilitato RDP per un cluster, è necessario aggiornare la pagina per potersi connettere al cluster.

**Per connettersi a un cluster tramite RDP**

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/).
2. Fare clic su **HDINSIGHT** nel riquadro sinistro. Verrà visualizzato un elenco dei cluster HDInsight distribuiti.
3. Fare clic sul cluster HDInsight a cui connettersi.
4. Fare clic su **CONFIGURATION** nella parte superiore della pagina.
5. Fare clic su **CONNECT** e seguire le istruzioni.

##Creare un certificato autofirmato

Per eseguire operazioni nel cluster con .NET SDK è necessario creare un certificato autofirmato nella workstation e caricarlo nella sottoscrizione di Azure. Si tratta di un'attività una-tantum. È possibile installare lo stesso certificato in altre macchine, purché sia valido.

**Per creare un certificato autofirmato**

1. Creare un certificato autofirmato usato per autenticare le richieste. Per creare il certificato è possibile usare Internet Information Services (IIS) o [makecert](http://go.microsoft.com/fwlink/?LinkId=534000).

2. Passare al percorso del certificato, fare clic con il pulsante destro del mouse su di esso, fare clic su **Installa certificato** quindi installare il certificato nell'archivio personale sul computer. Modificare le proprietà del certificato per assegnare ad esso un nome descrittivo.

3. Importare il certificato nel portale di Azure classico. Nel portale fare clic su **IMPOSTAZIONI** in basso a sinistra della pagina, quindi fare clic su **CERTIFICATI DI GESTIONE**. Nella parte inferiore della pagina, fare clic su **CARICA** e seguire le istruzioni per caricare il file con estensione cer creato al passaggio precedente.

	![HDI.ClusterCreate.UploadCert][image-hdiclustercreate-uploadcert]


##Concedere/revocare l'accesso ai servizi HTTP

Per i cluster HDInsight sono disponibili i servizi Web HTTP seguenti (tutti con endpoint RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

Per impostazione predefinita, a questi servizi è concesso l'accesso. È possibile revocare/concedere l'accesso dal portale di Azure classico.

>[AZURE.NOTE] La concessione/revoca dell'accesso implica la reimpostazione del nome utente e della password del cluster.

**Per concedere/revocare l'accesso ai servizi Web HTTP**

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/).
2. Fare clic su **HDINSIGHT** nel riquadro sinistro. Verrà visualizzato un elenco dei cluster HDInsight distribuiti.
3. Fare clic sul cluster HDInsight da configurare.
4. Fare clic su **CONFIGURAZIONE** nella parte superiore della pagina.
5. Fare clic su **ATTIVA** o **DISATTIVA** accanto a **SERVIZI HADOOP**.
6. Per **NOME UTENTE** e **NUOVA PASSWORD** immettere rispettivamente il nuovo nome utente e la nuova password per il cluster.
7. Fare clic su **SAVE**.

Vedere [Amministrare cluster Hadoop in HDInsight tramite Azure PowerShell](hdinsight-administer-use-powershell.md)

##Aprire la riga di comando di Hadoop.

Per connettersi al cluster tramite Desktop remoto e usare la riga di comando di Hadoop, è prima necessario aver abilitato l'accesso Desktop remoto al cluster come descritto nella sezione precedente.

**Per aprire la riga di comando di Hadoop**

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/).
2. Fare clic su **HDINSIGHT** nel riquadro sinistro. Verrà visualizzato un elenco dei cluster Hadoop distribuiti.
3. Fare clic sul cluster HDInsight a cui connettersi.
3. Fare clic su **CONFIGURAZIONE** nella parte superiore della pagina.
4. Fare clic su **CONNETTI** nella parte inferiore della pagina.
5. Fare clic su **Apri**.
6. Immettere le credenziali e quindi fare clic su **OK**. Usare il nome utente e la password configurati durante la creazione del cluster.
7. Fare clic su **Sì**.
8. Dal desktop fare doppio clic su **Hadoop Command Line**.

	![HDI.HadoopCommandLine][image-hadoopcommandline]


	Per altre informazioni sui comandi Hadoop, vedere la [documentazione di riferimento sui comandi Hadoop](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

Nella schermata precedente il numero di versione di Hadoop è incorporato nel nome della cartella. Il numero di versione cambia in base alla versione dei componenti Hadoop installati nel cluster. È possibile usare le variabili d'ambiente di Hadoop per fare riferimento a tali cartelle. ad esempio:

	cd %hadoop_home%
	cd %hive_home%
	cd %pig_home%
	cd %sqoop_home%
	cd %hcatalog_home%

##Ridimensionare i cluster
Vedere [Scalabilità del cluster in HDInsight](hdinsight-hadoop-cluster-scaling.md)

##Passaggi successivi
In questo articolo è stato illustrato come creare un cluster HDInsight tramite il portale di Azure classico e come aprire lo strumento da riga di comando di Hadoop. Per altre informazioni, vedere gli articoli seguenti:

* [Amministrare HDInsight tramite Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Amministrare HDInsight tramite l’interfaccia della riga di comando di Azure](hdinsight-administer-use-command-line.md)
* [Effettuare il provisioning di cluster HDInsight](hdinsight-provision-clusters.md)
* [Inviare processi Hadoop a livello di codice](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Introduzione ad Azure HDInsight](../hdinsight-get-started.md)
* [Versione di Hadoop inclusa in Azure HDInsight](hdinsight-component-versioning.md)

[image-hdi-create-rpd-user]: ./media/hdinsight-administer-use-management-portal-v1/hdi.createrdpuser.png
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal-v1/hdinsight-hadoop-command-line.png "Riga di comando di Hadoop"
[image-hdiclustercreate-uploadcert]: ./media/hdinsight-administer-use-management-portal-v1/hdi.clustercreate.uploadcert.png

<!---HONumber=AcomDC_0128_2016-->