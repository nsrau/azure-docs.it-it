<properties linkid="manage-services-hdinsight-get-started-hdinsight" urlDisplayName="Get Started" pageTitle="Get started using Hadoop 2.2 clusters with HDInsight | Azure" metaKeywords="" description="Get started using Hadoop 2.2 clusters with HDInsight, a big data solution. Learn how to provision clusters, run MapReduce jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Azure HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

Introduzione all'utilizzo di cluster Hadoop 2.2 con HDInsight
=============================================================

HDInsight rende [Apache Hadoop](http://hadoop.apache.org/) disponibile come servizio nel cloud, rendendo disponibile il framework software MapReduce in un ambiente di Azure più semplice, scalabile ed efficiente a livello di costi. HDInsight offre inoltre un approccio economicamente vantaggioso alla gestione e all'archiviazione di dati tramite l'archivio BLOB di Azure.

In questa esercitazione verrà eseguito il provisioning di un cluster HDInsight tramite il portale di gestione di Azure, un processo MapReduce di Hadoop verrà inviato tramite PowerShell e quindi i dati di output del processo MapReduce verranno importati in Excel per essere esaminati.

> [WACOM.NOTE] In questa esercitazione viene illustrato l'utilizzo di cluster Hadoop 2.2 su HDInsight. Per l'esercitazione sull'utilizzo di cluster Hadoop 1.2 su HDInsight, vedere [Introduzione all'utilizzo di Azure HDInsight](/it-it/documentation/articles/hdinsight-get-started/).

> [WACOM.NOTE] La sintassi *asv://* non è supportata nei cluster HDInsight della versione 3.0 e non sarà supportata nelle versioni successive. Sarà pertanto necessario utilizzare la sintassi *wasb://*.

In concomitanza con la fase GA (General Availability) di Azure HDInsight, Microsoft ha rilasciato anche HDInsight Emulator per Azure, noto in precedenza come Microsoft HDInsight Developer Preview. Questo prodotto è stato progettato per scenari relativi allo sviluppo e supporta pertanto solo distribuzioni a nodo singolo. Per informazioni sull'utilizzo di HDInsight Emulator, vedere [Introduzione a HDInsight Emulator](/it-it/documentation/articles/hdinsight-get-started-emulator/).

**Prerequisiti:**

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

-   Una sottoscrizione di Azure. Per ulteriori informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto](https://www.windowsazure.com/en-us/pricing/purchase-options/), [Offerte per i membri](https://www.windowsazure.com/en-us/pricing/member-offers/) oppure [Versione di valutazione gratuita](https://www.windowsazure.com/en-us/pricing/free-trial/).
-   Un computer che esegue Windows 8, Windows 7, Windows Server 2012 o Windows Server 2008 R2. Tale computer verrà utilizzato per l'invio di processi MapReduce.
-   Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone oppure Office 2010 Professional Plus.

**Tempo previsto per il completamento:** 30 minuti

Contenuto dell'esercitazione:
-----------------------------

-   [Configurazione dell'ambiente locale per l'esecuzione di PowerShell](#setup)
-   [Provisioning di un cluster HDInsight](#provision)
-   [Esecuzione di un processo WordCount MapReduce](#sample)
-   [Connessione agli strumenti di Microsoft Business Intelligence](#powerquery)
-   [Passaggi successivi](#nextsteps)

Configurazione dell'ambiente locale per l'esecuzione di PowerShell
------------------------------------------------------------------

È possibile inviare processi MapReduce a HDInsight in molti modi. In questa esercitazione verrà utilizzato Azure PowerShell. Per installare Azure PowerShell, eseguire l'[Installazione guidata piattaforma Web Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Quando richiesto, fare clic su **Esegui**, quindi su **Installa** e infine seguire le istruzioni visualizzate. Per ulteriori informazioni, vedere [Come installare e configurare Azure PowerShell](/it-it/documentation/articles/install-configure-powershell/).

Per poter gestire i servizi tramite i cmdlet di PowerShell, è necessario specificare le informazioni della sottoscrizione.

**Per connettersi alla sottoscrizione mediante Azure AD**

1.  Aprire la console di Azure PowerShell come descritto in [Procedura: Installare Azure PowerShell](/it-it/documentation/articles/install-configure-powershell/#install).
2.  Eseguire il comando seguente:

         Add-AzureAccount

3.  Nella finestra digitare l'indirizzo di posta elettronica e la password associati all'account. Le informazioni delle credenziali vengono autenticate e salvate in Azure, quindi la finestra viene chiusa.

Per connettersi alla sottoscrizione è anche possibile utilizzare il metodo basato sul certificato. Per istruzioni, vedere [Come installare e configurare Azure PowerShell](/it-it/documentation/articles/install-configure-powershell/).

Provisioning di un cluster HDInsight
------------------------------------

Per eseguire il processo di provisioning di un cluster HDInsight, è necessario utilizzare un account di archiviazione di Azure come file system predefinito. L'account di archiviazione deve trovarsi nello stesso data center che include le risorse di calcolo di HDInsight. È attualmente possibile eseguire il provisioning di cluster HDInsight solo nei data center seguenti:

-   Asia sudorientale
-   Europa settentrionale
-   Europa occidentale
-   Stati Uniti orientali
-   Stati Uniti occidentali

È necessario scegliere uno dei cinque data center disponibili per l'account di archiviazione di Azure.

**Per creare un account di archiviazione di Azure**

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/).
2.  Fare clic su **NEW** nell'angolo inferiore sinistro, selezionare **DATA SERVICES**, quindi **STORAGE** e infine **QUICK CREATE**.

    ![HDI.StorageAccount.QuickCreate](./media/hdinsight-get-started-3.0/HDI.StorageAccount.QuickCreate.png)

3.  Immettere i valori desiderati per **URL**, **LOCATION** e **REPLICATION**, quindi fare clic su **CREATE STORAGE ACCOUNT**. I gruppi di affinità non sono supportati. Il nuovo account di archiviazione verrà incluso nell'elenco di archiviazione.
4.  Attendere che il valore di **STATUS** per il nuovo account di archiviazione venga modificato in **Online**.
5.  Fare clic sul nuovo account di archiviazione nell'elenco per selezionarlo.
6.  Fare clic su **MANAGE ACCESS KEYS** nella parte inferiore della pagina.
7.  Prendere nota dei valori di **STORAGE ACCOUNT NAME** e **PRIMARY ACCESS KEY**. Sarà necessario utilizzarli più avanti nell'esercitazione.

Per istruzioni dettagliate, vedere [Come creare un account di archiviazione](/it-it/documentation/articles/storage-create-storage-account/) e [Utilizzo dell'archivio BLOB di Azure con HDInsight](/it-it/documentation/articles/hdinsight-use-blob-storage/).

Il provisioning dei cluster HDInsight 3.0 è attualmente supportato solo mediante l'opzione Custom Create.

**Per eseguire il provisioning di un cluster HDInsight**

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/).

2.  Fare clic su **HDINSIGHT** a sinistra per elencare lo stato dei cluster disponibili nell'account. Nella schermata seguente non è disponibile alcun cluster HDInsight.

    ![HDI.ClusterStatus](./media/hdinsight-get-started-3.0/HDI.ClusterStatus.png)

3.  Custom Create **NEW** nell'angolo inferiore sinistro, quindi su **DATA SERVICES**, **HDINSIGHT** e infine su **CUSTOM CREATE**.

    ![HDI.CustomCreateCluster](./media/hdinsight-get-started-3.0/HDI.CustomCreateCluster.png)

4.  Nella scheda Cluster Details immettere o selezionare i valori seguenti:

	<table data-morhtml="true" border="1">
 <tr data-morhtml="true"><th data-morhtml="true">Nome</th><th data-morhtml="true">Valore</th></tr>
 <tr data-morhtml="true"><td data-morhtml="true"><strong data-morhtml="true">Cluster Name</strong></td><td data-morhtml="true">Nome del cluster.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true"><strong data-morhtml="true">Data Nodes</strong></td><td data-morhtml="true">Numero di nodi di dati che si desidera distribuire. Ai fini di test, creare un cluster a singolo nodo. <br data-morhtml="true" />Il limite relativo alle dimensioni del cluster dipende dalla sottoscrizione di Azure. Per aumentare il limite, contattare il team del supporto fatturazione di Azure.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true"><strong data-morhtml="true">HDInsight Version</strong></td><td data-morhtml="true">Scegliere <strong data-morhtml="true">3.0</strong> per creare un cluster Hadoop 2.2 su HDInsight.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true"><strong data-morhtml="true">Region</strong></td><td data-morhtml="true">Selezionare la stessa area geografica specificata per l'account di archiviazione creato nell'ultima procedura. HDInsight richiede che l'account di archiviazione si trovi nella stessa area geografica. Pi&ugrave; avanti nella configurazione sar&agrave; possibile selezionare un account di archiviazione situato nella stessa area geografica specificata qui.
 </td></tr>
 </table>

5.  Fare clic sulla freccia destra nell'angolo inferiore destro per configurare l'utente del cluster.
6.  Nella scheda Configure Cluster immettere **User Name** e **Password** per l'account utente relativo al cluster HDInsight. Oltre a tale account, è possibile creare un account utente RDP dopo il provisioning, in modo da potere accedere al cluster tramite Desktop remoto. Per istruzioni, vedere [Amministrazione di HDInsight tramite il portale di gestione](/it-it/documentation/articles/hdinsight-administer-use-management-portal/)
7.  Fare clic sulla freccia destra nell'angolo inferiore destro per configurare l'account di archiviazione.
8.  Nella scheda Storage Account immettere o selezionare i valori seguenti:

	<table data-morhtml="true" border="1">
 <tr data-morhtml="true"><th data-morhtml="true">Nome</th><th data-morhtml="true">Valore</th></tr>
 <tr data-morhtml="true"><td data-morhtml="true">STORAGE ACCOUNT</td><td data-morhtml="true">Selezionare <strong data-morhtml="true">Use Existing Storage</strong>. &Egrave; inoltre possibile utilizzare il portale di gestione per creare un nuovo account di archiviazione, nel caso in cui non ne sia gi&agrave; stato creato uno.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">ACCOUNT NAME</td><td data-morhtml="true">Specificare l'account di archiviazione creato nell'ultima procedura di questa esercitazione. Si noti che nell'elenco vengono visualizzati solo gli account di archiviazione disponibili nella stessa area geografica.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">DEFAULT CONTAINER</td><td data-morhtml="true">Selezionare <strong data-morhtml="true">Create default container</strong>. Quando si seleziona questa opzione, il nome del contenitore predefinito sar&agrave; uguale al nome del cluster.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">ADDITIONAL STORATGE ACCOUNT</td><td data-morhtml="true">Selezionare <strong data-morhtml="true">0</strong>. &Egrave; possibile connettere il cluster a un massimo di 7 account di archiviazione aggiuntivi.</td></tr>
 </table>

9.  Fare clic sull'icona di spunta nell'angolo inferiore destro per creare il cluster. Al termine del processo di provisioning, nella colonna relativa allo stato verrà visualizzato il valore **Running**.

Esecuzione di un processo WordCount MapReduce
---------------------------------------------

Il provisioning del cluster HDInsight è stato completato. Il passaggio successivo consiste nell'esecuzione di un processo MapReduce per il conteggio delle parole in un file di testo.

Per eseguire un processo MapReduce è necessario disporre degli elementi seguenti:

-   Un programma MapReduce. In questa esercitazione verrà utilizzato il processo di esempio WordCount, disponibile nella distribuzione del cluster HDInsight. Non sarà pertanto necessario scrivere un processo personalizzato. Il processo di esempio è disponibile in */example/jars/hadoop-mapreduce-examples.jar*. Per istruzioni sulla scrittura di un processo MapReduce personalizzato, vedere [Sviluppo di programmi MapReduce Java per HDInsight](/it-it/documentation/articles/hdinsight-develop-deploy-java-mapreduce/).

-   Un file di input. Come file di input verrà utilizzato il file */example/data/gutenberg/davinci.txt*. Per informazioni sul caricamento di file, vedere [Caricamento di dati in HDInsight](/it-it/documentation/articles/hdinsight-upload-data/).
-   Una cartella di file di output. Come cartella di file di output verrà utilizzata la cartella */example/data/WordCountOutput*. Se non esiste, tale cartella verrà creata dal sistema.

Lo schema URI per l'accesso ai file nell'archiviazione BLOB è il seguente:

    wasb[s]://<containername>@<storageaccountname>.blob.core.windows.net/<path>

> [WACOM.NOTE] Per impostazione predefinita, il nome del contenitore BLOB utilizzato per il file system predefinito corrisponde al nome del cluster HDInsight.

Lo schema URI offre accesso non crittografato mediante il prefisso *wasb:* e accesso SSL crittografato tramite WASBS. Quando possibile, è consigliabile utilizzare WASBS, anche per l'accesso a dati presenti nello stesso data center di Azure.

Poiché HDInsight utilizza un contenitore di archiviazione BLOB come file system predefinito, è possibile fare riferimento a file e directory all'interno del file system predefinito utilizzando percorsi relativi o assoluti.

Per accedere, ad esempio, a hadoop-mapreduce-examples.jar, è possibile utilizzare una delle opzioni seguenti:

	● wasb://<containername>@<storageaccountname>.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
	● wasb:///example/jars/hadoop-mapreduce-examples.jar
	● /example/jars/hadoop-mapreduce-examples.jar

L'utilizzo del prefisso *wasb://* nei percorsi di tali file è necessario per indicare che si utilizza l'archivio BLOB di Azure per i file di input e output. La directory di output presuppone un percorso predefinito relativo alla cartella *wasb:///user/&lt;nomeutente\>*.

Per ulteriori informazioni, vedere [Utilizzo dell'archivio BLOB di Azure con HDInsight](/it-it/documentation/articles/hdinsight-use-blob-storage/).

**Per eseguire l'esempio WordCount**

1.  Aprire **Azure PowerShell**. Per istruzioni sull'apertura della finestra della console Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/it-it/documentation/articles/install-configure-powershell/).

2.  Per impostare le variabili, eseguire i comandi seguenti.

         $subscriptionName = "<SubscriptionName>" 
         $clusterName = "<HDInsightClusterName>"        

3.  Per creare una definizione del processo MapReduce, eseguire i comandi seguenti:

         # Define the MapReduce job
         $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    Il file hadoop-mapreduce-examples.jar è incluso nella distribuzione del cluster HDInsight. Per il processo MapReduce sono disponibili due argomenti. Il primo argomento è il nome del file di origine, il secondo è il percorso del file di output. Il file di origine è incluso nella distribuzione del cluster HDInsight e il percorso del file di output verrà creato in fase di esecuzione.

4.  Per inviare il processo MapReduce, eseguire il comando seguente:

         # Submit the job
         Select-AzureSubscription $subscriptionName
         $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 

    Oltre alla definizione del processo MapReduce, è necessario specificare anche il nome del cluster HDInsight in cui si desidera eseguire il processo MapReduce.

    Start-AzureHDInsightJob *è una chiamata non sincronizzata.* Per verificare il completamento del processo, utilizzare il cmdlet *Wait-AzureHDInsightJob*.

5.  Per verificare il completamento del processo MapReduce, eseguire il comando seguente:

         Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

6.  Per verificare se si sono verificati errori nel processo MapReduce, eseguire il comando seguente:

         # Get the job output
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

    Nella schermata seguente viene mostrato l'output di un'esecuzione completata correttamente. In caso di esito negativo, verranno visualizzati alcuni messaggi di errore.

    ![HDI.GettingStarted.RunMRJob](./media/hdinsight-get-started-3.0/HDI.GettingStarted.RunMRJob.png)

**Per recuperare i risultati del processo MapReduce**

1.  Aprire **Azure PowerShell**.
2.  Per creare una cartella C:\\Tutorials e cambiare la directory specificando tale cartella, eseguire i comandi seguenti:

         mkdir \Tutorials
         cd \Tutorials

    La directory predefinita per Azure Powershell è *C:\\Windows\\System32\\WindowsPowerShell\\v1.0*. Per impostazione predefinita, non si dispone di autorizzazioni di scrittura per tale cartella. È necessario cambiare la directory, specificando una cartella per cui si disponga di autorizzazioni di scrittura.

3.  Impostare le tre variabili necessarie nei comandi seguenti, quindi eseguirli:

         $subscriptionName = "<SubscriptionName>"       
         $storageAccountName = "<StorageAccountName>"   
         $containerName = "<ContainerName>"              

    L'account di archiviazione di Azure corrisponde all'account creato in precedenza in questa esercitazione. L'account di archiviazione viene utilizzato per l'hosting del contenitore BLOB utilizzato come file system predefinito per il cluster HDInsight. Il nome del contenitore di archiviazione BLOB corrisponde in genere al nome del cluster HDInsight, a meno che non venga specificato un nome diverso durante il provisioning del cluster.

4.  Per creare un oggetto contesto archiviazione di Azure, eseguire i comandi seguenti:

         # Create the storage account context object
         Select-AzureSubscription $subscriptionName
         $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
         $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Il comando *Select-AzureSubscription* consente di impostare la sottoscrizione corrente, nel caso in cui siano disponibili più sottoscrizioni e non si desideri utilizzare la sottoscrizione predefinita.

5.  Per scaricare l'output del processo MapReduce dal contenitore BLOB alla workstation, eseguire il comando seguente:

         # Download the job output to the workstation
         Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    La cartella *example/data/WordCountOutput* è la cartella di output specificata durante l'esecuzione del processo MapReduce. Il valore *part-r-00000* corrisponde al nome file predefinito per l'output del processo MapReduce. Il file verrà scaricato nella stessa struttura di cartelle nella cartella locale. Ad esempio, nella schermata seguente la cartella corrente è la cartella radice C. Il file verrà scaricato nella cartella *C:\\example\\data\\WordCountOutput\\*.

6.  Per stampare il file di output del processo MapReduce, eseguire il comando seguente:

         cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    ![HDI.GettingStarted.MRJobOutput](./media/hdinsight-get-started-3.0/HDI.GettingStarted.MRJobOutput.png)

    Il processo MapReduce produce un file denominato *part-r-00000*, che include le parole e i conteggi. Lo script utilizza il comando findstr per elencare tutte le parole contenenti *"there"*.

> [WACOM.NOTE] Se si apre *./example/data/WordCountOutput/part-r-00000*, un output multilinea dal processo MapReduce, nel Blocco note, si noterà che il rendering delle interruzioni di riga non è corretto. Si tratta di un comportamento previsto.

Connessione agli strumenti di Microsoft Business Intelligence
-------------------------------------------------------------

È possibile utilizzare il componente aggiuntivo Power Query per Excel per esportare l'output da HDInsight a Excel, quindi utilizzare gli strumenti di Microsoft Business Intelligence (BI) per elaborare ulteriormente o visualizzare i risultati. Quando si crea un cluster HDInsight, viene creato anche un contenitore predefinito con nome uguale a quello del cluster nell'account di archiviazione associato al cluster durante la fase di creazione. In tale contenitore vengono inseriti automaticamente alcuni file, inclusa una tabella Hive di esempio. In questa sezione verrà illustrato come importare in Excel i dati inclusi in tale tabella, per consentirne la visualizzazione e l'ulteriore elaborazione.

Per completare questa parte dell'esercitazione, è necessario disporre di Excel 2010 o 2013. Per iniziare, verrà importata la tabella Hive predefinita inclusa in HDInsight.

**Per scaricare Microsoft PowerQuery per Excel**

-   Scaricare Microsoft Power Query per Excel dall'[Area download Microsoft](http://www.microsoft.com/en-us/download/details.aspx?id=39379) e installarlo.

**Per importare dati di HDInsight**

1.  Aprire Excel e creare una nuova cartella di lavoro vuota.
2.  Nel menu **Power Query** scegliere **Da altre origini**, quindi fare clic su **Da Azure HDInsight**.

    ![HDI.GettingStarted.PowerQuery.ImportData](./media/hdinsight-get-started-3.0/HDI.GettingStarted.PowerQuery.ImportData.png)

3.  Immettere il **Nome account** dell'account di archiviazione BLOB di Azure associato al cluster, quindi fare clic su **OK**. Si tratta dell'account di archiviazione creato in precedenza nell'esercitazione.
4.  Immettere la **Chiave account** per l'account di archiviazione BLOB di Azure, quindi fare clic su **Salva**.
5.  Nel riquadro Strumento di navigazione a destra fare doppio clic sul nome del contenitore di archiviazione BLOB. Per impostazione predefinita, il nome del contenitore è uguale al nome del cluster.

6.  Individuare **part-r-00000** nella colonna **Name** (il percorso è *.../example/data/WordCountOutput*), quindi fare clic su **Binary** a sinistra di **part-r-00000**.

    ![HDI.GettingStarted.PowerQuery.ImportData2](./media/hdinsight-get-started-3.0/HDI.GettingStarted.PowerQuery.ImportData2.png)

7.  Fare clic con il pulsante destro del mouse su **Column1.1**, quindi selezionare **Rinomina**.
8.  Cambiare il nome in **Word**.
9.  Ripetere il processo per rinominare la **Column1.2** in **Count**.

    ![HDI.GettingStarted.PowerQuery.ImportData3](./media/hdinsight-get-started-3.0/HDI.GettingStarted.PowerQuery.ImportData3.png)

10. Fare clic su **Applica e chiudi** nell'angolo superiore sinistro. La query eseguirà quindi l'importazione della tabella Hive in Excel.

Passaggi successivi
-------------------

In questa esercitazione si è appreso come eseguire il provisioning di un cluster con HDInsight, eseguire un processo MapReduce su tale cluster e importare i risultati in Excel per un'ulteriore elaborazione e per la visualizzazione grafica mediante gli strumenti di Business Intelligence. Per ulteriori informazioni, vedere gli articoli seguenti:

-   [Introduzione all'utilizzo di HDInsight](/it-it/documentation/articles/hdinsight-get-started/)
-   [Introduzione a HDInsight Emulator](/it-it/documentation/articles/hdinsight-get-started-emulator/)
-   [Utilizzo dell'archivio BLOB di Azure con HDInsight](/it-it/documentation/articles/hdinsight-use-blob-storage/)
-   [Amministrazione di HDInsight tramite PowerShell](/it-it/documentation/articles/hdinsight-administer-use-powershell/)
-   [Caricamento di dati in HDInsight](/it-it/documentation/articles/hdinsight-upload-data/)
-   [Utilizzo di Hive con HDInsight](/it-it/documentation/articles/hdinsight-use-hive/)
-   [Utilizzo di Pig con HDInsight](/it-it/documentation/articles/hdinsight-use-pig/)
-   [Utilizzo di Oozie con HDInsight](/it-it/documentation/articles/hdinsight-use-oozie/)
-   [Sviluppo di programmi MapReduce di streaming Hadoop in C\# per HDInsight](/it-it/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
-   [Sviluppo di programmi MapReduce Java per HDInsight](/it-it/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)

