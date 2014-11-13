<properties urlDisplayName="Administration" pageTitle="Gestire i cluster Hadoop in HDInsight con il portale di Azure | Azure" metaKeywords="" description="Informazioni su come amministrare il servizio HDInsight. Creare un cluster HDInsight, aprire la console interattiva JavaScript e aprire la console dei comandi di Hadoop." metaCanonical="" services="hdinsight" documentationCenter="" title="Gestire cluster Hadoop in HDInsight tramite il portale di gestione di Azure" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Gestire cluster Hadoop in HDInsight tramite il portale di gestione di Azure

Il portale di gestione di Azure permette di eseguire il provisioning di cluster Hadoop in HDInsight, modificare la password dell'utente di Hadoop e abilitare RDP in modo da accedere alla console dei comandi di Hadoop nel cluster. Oltre al portale di gestione sono disponibili altri strumenti per amministrare HDInsight.

-   Per altre informazioni sull'amministrazione di HDInsight tramite Azure PowerShell, vedere [Amministrazione di HDInsight tramite PowerShell][Amministrazione di HDInsight tramite PowerShell].

-   Per altre informazioni sull'amministrazione di HDInsight tramite gli strumenti da riga di comando multipiattaforma, vedere [Amministrazione di HDInsight tramite l'interfaccia della riga di comando multipiattaforma di Azure][Amministrazione di HDInsight tramite l'interfaccia della riga di comando multipiattaforma di Azure].

**Prerequisiti:**

Per eseguire le procedure descritte nell'articolo è necessario:

-   **Sottoscrizione di Azure**. Azure è una piattaforma basata su sottoscrizione. Per informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto][Opzioni di acquisto], [Offerte per i membri][Offerte per i membri] oppure [Versione di valutazione gratuita][Versione di valutazione gratuita].

## Contenuto dell'articolo

-   [Provisioning di cluster HDInsight][Provisioning di cluster HDInsight]
-   [Personalizzazione di cluster HDInsight][Personalizzazione di cluster HDInsight]
-   [Modifica di nome utente e password di cluster HDInsight][Modifica di nome utente e password di cluster HDInsight]
-   [Connessione a cluster HDInsight tramite RDP][Connessione a cluster HDInsight tramite RDP]
-   [Creare un certificato autofirmato][Creare un certificato autofirmato]
-   [Concessione/revoca dell'accesso ai servizi HTTP][Concessione/revoca dell'accesso ai servizi HTTP]
-   [Apertura della console dei comandi di Hadoop][Apertura della console dei comandi di Hadoop]
-   [Passaggi successivi][Passaggi successivi]

## <span id="create"></span></a> Provisioning di cluster HDInsight

È possibile creare cluster HDInsight in diversi modi. In questo articolo viene spiegato come effettuare questa operazione usando l'opzione Quick Create del portale di gestione di Azure. Per le altre opzioni, vedere [Provisioning di cluster HDInsight][1].

I cluster HDInsight usano un contenitore dell'archivio BLOB di Azure come file system predefinito. Per altre informazioni sull'esperienza lineare offerta dall'archiviazione BLOB di con cluster HDInsight, vedere [Usare l'archivio BLOB di Azure con HDInsight][Usare l'archivio BLOB di Azure con HDInsight].

È necessario creare un account di archiviazione di Azure nello stesso data center in cui eseguire il provisioning del cluster HDInsight. È attualmente possibile eseguire il provisioning di cluster HDInsight in cinque data center:

-   Asia sudorientale
-   Europa settentrionale
-   Europa occidentale
-   Stati Uniti orientali
-   Stati Uniti occidentali

Per dettagli sulla creazione di un account di archiviazione di Azure, vedere [Come creare un account di archiviazione][Come creare un account di archiviazione].

**Per eseguire il provisioning di un cluster HDInsight**

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic su **NEW** nella parte inferiore della pagina, quindi su **DATA SERVICES**, **HDINSIGHT** e infine su **QUICK CREATE**.

3.  Specificare un valore in **Cluster Name**, **Cluster Size**, **Cluster Admin Password** e un **account di archiviazione** di Azure, quindi fare clic su **Create HDInsight Cluster**. Lo stato del cluster creato e in esecuzione è *Running*.

    ![HDI.QuickCreate][HDI.QuickCreate]

    Quando si usa l'opzione Creazione rapida per creare un cluster, il nome utente predefinito dell'account amministratore è *admin*. Per assegnare all'account un nome utente diverso, è possibile usare l'opzione Custom Create anziché Quick Create. È anche possibile cambiare il nome dell'account dopo il provisioning.

    Quando si usa l'opzione Quick Create per creare un cluster, nell'account di archiviazione specificato viene creato automaticamente un nuovo contenitore con il nome del cluster HDInsight. Se si desidera personalizzare il nome del contenitore che verrà usato dal cluster per impostazione predefinita, usare l'opzione Custom Create.

    > [WACOM.NOTE] Dopo avere scelto un account di archiviazione di Azure per il cluster HDInsight, l'unico modo per modificare l'account consiste nell'eliminare il cluster e crearne uno nuovo con l'account di archiviazione desiderato.

4.  Fare clic sul nuovo cluster creato. Verrà visualizzata la pagina di destinazione:

    ![HDI.ClusterLanding][HDI.ClusterLanding]

## <span id="customize"></span></a> Personalizzazione di cluster HDInsight

HDInsight è compatibile con una vasta gamma di componenti Hadoop. Per l'elenco dei componenti verificati e supportati, vedere [Versione di Hadoop inclusa in Azure HDInsight][Versione di Hadoop inclusa in Azure HDInsight]. Per personalizzare HDInsight, è possibile scegliere una delle opzioni seguenti:

-   Usare i parametri di personalizzazione del cluster in HDInsight .NET SDK o in Azure PowerShell durante il provisioning del cluster. In questo modo le modifiche alla configurazione vengono mantenute per tutta la durata del cluster e non vengono interessate dalla ricreazione delle immagini dei nodi del cluster che la piattaforma Azure esegue periodicamente per la manutenzione. Per altre informazioni sull'utilizzo dei parametri di personalizzazione dei cluster, vedere [Provisioning di cluster HDInsight][1].
-   Nel cluster è possibile eseguire alcuni componenti Java nativi, come Mahout o Cascading, sotto forma di file JAR. Tali file JAR possono essere distribuiti nell'archivio BLOB di Azure e inviati ai cluster HDInsight usando i meccanismi di invio dei processi Hadoop. Per altre informazioni, vedere [Inviare processi Hadoop a livello di codice][Inviare processi Hadoop a livello di codice].

    > [WACOM.NOTE] In caso di problemi durante la distribuzione di file JAR in cluster HDInsight o la chiamata di file JAR in cluster HDInsight, contattare il [Supporto Microsoft][Supporto Microsoft].

    > Cascading non è supportato da HDInsight, pertanto in caso di problemi non è possibile rivolgersi al Supporto Microsoft. Per gli elenchi dei componenti supportati, vedere [Novità delle versioni cluster incluse con HDInsight][Versione di Hadoop inclusa in Azure HDInsight].

L'installazione di software personalizzato nel cluster tramite Connessione Desktop remoto non è supportata. È consigliabile evitare di archiviare file nelle unità del nodo head in quanto andranno perse nel caso in cui fosse necessario ricreare i cluster. È consigliabile archiviare i file nell'archivio BLOB di Azure. L'archivio BLOB è persistente.

## <span id="password"></span></a> Modifica di nome utente e password di cluster HDInsight

Per un cluster HDInsight possono esistere due account utente. L'account utente del cluster HDInsight viene creato durante il processo di provisioning. È inoltre possibile creare un account utente RDP per accedere al cluster tramite RDP. Vedere la sezione relativa all'[abilitazione di Desktop remoto][abilitazione di Desktop remoto].

**Per modificare nome utente e password di cluster HDInsight**

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic su **HDINSIGHT** nel riquadro sinistro. Verrà visualizzato un elenco dei cluster HDInsight distribuiti.
3.  Fare clic sul cluster HDInsight di cui reimpostare nome utente e password.
4.  Fare clic su **CONFIGURATION** nella parte superiore della pagina.
5.  Fare clic su **OFF** accanto a **HADOOP SERVICES**.
6.  Fare clic su **SAVE** nella parte inferiore della pagina e attendere il completamento dell'operazione di disabilitazione.
7.  Dopo la disabilitazione del servizio, fare clic su **ON** accanto a **HADOOP SERVICES**.
8.  Immettere un valore in **USER NAME** e **NEW PASSWORD**. Questi saranno il nuovo nome utente e la nuova password del cluster.
9.  Fare clic su **SAVE**.

## <span id="rdp"></span></a> Connessione a cluster HDInsight tramite RDP

Le credenziali del cluster fornite durante la creazione consentono di accedere ai servizi nel cluster, ma non al cluster stesso tramite Desktop remoto. L'accesso a Desktop remoto è disattivato per impostazione predefinita, pertanto per l'accesso diretto al cluster sono necessarie alcune operazioni di configurazione aggiuntive successive alla creazione.

**Per abilitare Desktop remoto**

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic su **HDINSIGHT** nel riquadro sinistro. Verrà visualizzato un elenco dei cluster HDInsight distribuiti.
3.  Fare clic sul cluster HDInsight a cui connettersi.
4.  Fare clic su **CONFIGURATION** nella parte superiore della pagina.
5.  Nella parte inferiore della pagina fare clic su **ENABLE REMOTE**.
6.  Nella procedura guidata **Configure Remote Desktop** immettere nome utente e password di Destkop remoto. Si noti che il nome utente deve essere diverso da quello usato per creare il cluster (*admin* per impostazione predefinita con l'opzione Quick Create). Immettere una data di scadenza nella casella **EXPIRES ON**. Si noti che la data di scadenza deve ricorrere nel futuro e non deve essere successiva a una settimana rispetto alla data odierna. Per impostazione predefinita, si presuppone che l'ora di scadenza del giorno sia la mezzanotte della data specificata. Fare clic sull'icona del segno di spunta.

    ![HDI.CreateRDPUser][HDI.CreateRDPUser]

    La data di scadenza deve ricorrere nel futuro e non essere successiva a sette giorni rispetto alla data odierna. L'ora corrisponde alla mezzanotte della data selezionata.

> [WACOM.NOTE] Dopo aver abilitato RDP per un cluster, è necessario aggiornare la pagina per potersi connettere al cluster.

**Per connettersi a un cluster tramite RDP**

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic su **HDINSIGHT** nel riquadro sinistro. Verrà visualizzato un elenco dei cluster HDInsight distribuiti.
3.  Fare clic sul cluster HDInsight a cui connettersi.
4.  Fare clic su **CONFIGURATION** nella parte superiore della pagina.
5.  Fare clic su **CONNECT** e seguire le istruzioni.

## <span id="cert"></span></a>Creare un certificato autofirmato

Per eseguire operazioni nel cluster con .NET SDK è necessario creare un certificato autofirmato nella workstation e aggiornarlo nella sottoscrizione di Azure. Si tratta di un'attività una-tantum. È possibile installare lo stesso certificato in altre macchine, purché sia valido.

**Per creare un certificato autofirmato**

1.  Creare un certificato autofirmato che venga usato per autenticare le richieste. È possibile usare IIS o [makecert][makecert] per creare il certificato.

2.  Passare al percorso del certificato, fare clic con il pulsante destro del mouse su di esso, fare clic su **Installa certificato** quindi installare il certificato nell'archivio personale sul computer. Modificare le proprietà del certificato per assegnare ad esso un nome descrittivo.

3.  Impostare il certificato nel portale di gestione di Azure. Dal portale, fare clic su **Impostazioni** in basso a sinistra, quindi fare clic su **Certificati di gestione**. Nella parte inferiore della pagina, fare clic su **Carica** e seguire le istruzioni per caricare il file con estensione cer creato al passaggio precedente.

    ![HDI.ClusterCreate.UploadCert][HDI.ClusterCreate.UploadCert]

## <span id="httpservice"></span></a> Concessione/revoca dell'accesso ai servizi HTTP

Per i cluster HDInsight sono disponibili i servizi Web HTTP seguenti (tutti con endpoint RESTful):

-   ODBC
-   JDBC
-   Ambari
-   Oozie
-   Templeton

Per impostazione predefinita, a questi servizi è concesso l'accesso. È possibile revocare/concedere l'accesso dal portale di gestione.

> [WACOM.NOTE] La concessione/revoca dell'accesso implica la reimpostazione del nome utente e della password del cluster.

**Per concedere/revocare l'accesso ai servizi Web HTTP**

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic su **HDINSIGHT** nel riquadro sinistro. Verrà visualizzato un elenco dei cluster HDInsight distribuiti.
3.  Fare clic sul cluster HDInsight da configurare.
4.  Fare clic su **CONFIGURATION** nella parte superiore della pagina.
5.  Fare clic su **ON** o **OFF** accanto a **SERVIZI HADOOP**.
6.  Immettere un valore in **USER NAME** e **NEW PASSWORD**. Questi saranno il nuovo nome utente e la nuova password del cluster.
7.  Fare clic su **SAVE**.

È possibile effettuare questa operazione anche con i cmdlet di Azure PowerShell:

-   Grant-AzureHDInsightHttpServicesAccess
-   Revoke-AzureHDInsightHttpServicesAccess

Vedere [Amministrazione di HDInsight tramite PowerShell][Amministrazione di HDInsight tramite PowerShell].

## <span id="hadoopcmd"></span></a> Aprire la riga di comando di Hadoop.

Per connettersi al cluster tramite Desktop remoto e usare la riga di comando di Hadoop, è prima necessario aver abilitato l'accesso Desktop remoto al cluster come descritto nella sezione precedente.

**Per aprire la riga di comando di Hadoop**

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic su **HDINSIGHT** nel riquadro sinistro. Verrà visualizzato un elenco dei cluster Hadoop distribuiti.
3.  Fare clic sul cluster HDInsight a cui connettersi.
4.  Fare clic su **CONFIGURATION** nella parte superiore della pagina.
5.  Fare clic su **Connect** nella parte inferiore della pagina.
6.  Fare clic su **Apri**.
7.  Immettere le credenziali e quindi fare clic su **OK**. Usare il nome utente e la password configurati durante la creazione del cluster.
8.  Fare clic su **Yes**.
9.  Dal desktop fare doppio clic su **Hadoop Command Line**.

    ![HDI.HadoopCommandLine][HDI.HadoopCommandLine]

    Per altre informazioni sui comandi Hadoop, vedere la [documentazione di riferimento sui comandi Hadoop][documentazione di riferimento sui comandi Hadoop].

Nella schermata precedente nel nome della cartella è incorporato il numero di versione di Hadoop. Il numero di versione cambia in base alla versione dei componenti Hadoop installati nel cluster. È possibile usare le variabili d'ambiente di Hadoop per fare riferimento a tali cartelle. Ad esempio:

    cd %hadoop_home%
    cd %hive_home%
    cd %pig_home%
    cd %sqoop_home%   
    cd %hcatalog_home%

## <span id="nextsteps"></span></a>Passaggi successivi

In questo articolo è stato illustrato come creare un cluster HDInsight tramite il portale di gestione di Azure e come aprire lo strumento da riga di comando di Hadoop. Per altre informazioni, vedere gli articoli seguenti:

-   [Amministrazione di HDInsight tramite PowerShell][Amministrazione di HDInsight tramite PowerShell]
-   [Amministrazione di HDInsight tramite l'interfaccia della riga di comando multipiattaforma][Amministrazione di HDInsight tramite l'interfaccia della riga di comando multipiattaforma di Azure]
-   [Provisioning di cluster HDInsight][1]
-   [Inviare processi Hadoop a livello di codice][Inviare processi Hadoop a livello di codice]
-   [Introduzione all'utilizzo di Azure HDInsight][Introduzione all'utilizzo di Azure HDInsight]
-   [Versione di Hadoop inclusa in Azure HDInsight][Versione di Hadoop inclusa in Azure HDInsight]

  [Amministrazione di HDInsight tramite PowerShell]: ../hdinsight-administer-use-powershell/
  [Amministrazione di HDInsight tramite l'interfaccia della riga di comando multipiattaforma di Azure]: ../hdinsight-administer-use-command-line/
  [Opzioni di acquisto]: http://azure.microsoft.com/it-it/pricing/purchase-options/
  [Offerte per i membri]: http://azure.microsoft.com/it-it/pricing/member-offers/
  [Versione di valutazione gratuita]: http://azure.microsoft.com/it-it/pricing/free-trial/
  [Provisioning di cluster HDInsight]: #create
  [Personalizzazione di cluster HDInsight]: #customize
  [Modifica di nome utente e password di cluster HDInsight]: #password
  [Connessione a cluster HDInsight tramite RDP]: #rdp
  [Creare un certificato autofirmato]: #cert
  [Concessione/revoca dell'accesso ai servizi HTTP]: #httpservice
  [Apertura della console dei comandi di Hadoop]: #hadoopcmd
  [Passaggi successivi]: #nextsteps
  [1]: ../hdinsight-provision-clusters/
  [Usare l'archivio BLOB di Azure con HDInsight]: ../hdinsight-use-blob-storage/
  [Come creare un account di archiviazione]: ../storage-create-storage-account/
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [HDI.QuickCreate]: ./media/hdinsight-administer-use-management-portal/HDI.QuickCreateCluster.png
  [HDI.ClusterLanding]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterLanding.PNG "Cluster landing page"
  [Versione di Hadoop inclusa in Azure HDInsight]: ../hdinsight-component-versioning/
  [Inviare processi Hadoop a livello di codice]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Supporto Microsoft]: http://azure.microsoft.com/it-it/support/options/
  [abilitazione di Desktop remoto]: #enablerdp
  [HDI.CreateRDPUser]: ./media/hdinsight-administer-use-management-portal/HDI.CreateRDPUser.png
  [makecert]: http://msdn.microsoft.com/it-it/library/bfsktky3(v=vs.110).aspx
  [HDI.ClusterCreate.UploadCert]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterCreate.UploadCert.png
  [HDI.HadoopCommandLine]: ./media/hdinsight-administer-use-management-portal/HDI.HadoopCommandLine.PNG "Hadoop command line"
  [documentazione di riferimento sui comandi Hadoop]: http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html
  [Introduzione all'utilizzo di Azure HDInsight]: ../hdinsight-get-started/
