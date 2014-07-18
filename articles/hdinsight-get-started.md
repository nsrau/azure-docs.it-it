<properties  linkid="manage-services-hdinsight-get-started-hdinsight" urlDisplayName="Get Started" pageTitle="Get started using HDInsight | Azure" metaKeywords="" description="Get started with HDInsight, a big data solution. Learn how to provision clusters, run MapReduce jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Azure HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

# Introduzione all'utilizzo di Azure HDInsight

HDInsight rende [Apache Hadoop][1] disponibile come servizio nel cloud, rendendo disponibile il framework software MapReduce in un ambiente di Azure più semplice, scalabile ed efficiente a livello di costi. HDInsight offre inoltre un approccio economicamente vantaggioso alla gestione e all'archiviazione di dati tramite l'archiviazione BLOB di Azure.

In questa esercitazione verrà eseguito il provisioning di un cluster HDInsight tramite il portale di gestione di Azure, un processo MapReduce di Hadoop verrà inviato tramite PowerShell e quindi i dati di output del processo MapReduce verranno importati in Excel per essere esaminati.

> [WACOM.NOTE] In questa esercitazione viene illustrato l'utilizzo di
> cluster Hadoop 1.2 su HDInsight. Per l'esercitazione sull'utilizzo
> di cluster Hadoop 2.2 su HDInsight, vedere [Introduzione all'utilizzo
> di cluster Hadoop 2.2 con
> HDInsight](/en-us/documentation/articles/hdinsight-get-started-30/).

In concomitanza con la fase GA (General Availability) di Azure HDInsight, Microsoft ha rilasciato anche HDInsight Emulator per Azure, noto in precedenza come Microsoft HDInsight Developer Preview. Questo prodotto è stato progettato per scenari relativi allo sviluppo e supporta pertanto solo distribuzioni a nodo singolo. Per informazioni sull'utilizzo di HDInsight Emulator, vedere [Introduzione a HDInsight Emulator](/en-us/documentation/articles/hdinsight-get-started-emulator/).

**Prerequisiti:**

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* Una sottoscrizione di Azure. Per ulteriori informazioni su come
  ottenere una sottoscrizione, vedere [Opzioni di acquisto][2], [Offerte
  per i membri][3] oppure [Versione di valutazione gratuita][4].
* Un computer che esegue Windows 8, Windows 7, Windows Server 2012 o
  Windows Server 2008 R2. Tale computer verrà utilizzato per l'invio di
  processi MapReduce.
* Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013
  Standalone oppure Office 2010 Professional Plus.

**Tempo previsto per il completamento:** 30 minuti

## Contenuto dell'esercitazione:

* [Configurare l'ambiente locale per l'esecuzione di
  PowerShell](#setup)
* [Eseguire il provisioning di un cluster HDInsight](#provision)
* [Eseguire un processo WordCount MapReduce](#sample)
* [Effettuare la connessione agli strumenti di Microsoft Business
  Intelligence](#powerquery)
* [Passaggi successivi](#nextsteps)

## <a  id="setup" ></a> Configurare l'ambiente locale per l'esecuzione di PowerShell

È possibile inviare processi MapReduce a HDInsight in molti modi. In
questa esercitazione verrà utilizzato Azure PowerShell. Per installare Azure PowerShell, eseguire l'[Installazione guidata piattaforma Web Microsoft][5]. Quando richiesto, fare clic su **Esegui**, quindi su
**Installa** e infine seguire le istruzioni visualizzate. Per ulteriori
informazioni, vedere [Come installare e configurare Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

Per poter gestire i servizi tramite i cmdlet di PowerShell, è necessario specificare le informazioni della sottoscrizione.

**Per connettersi alla sottoscrizione mediante Azure AD**

1.  Aprire la console di Azure PowerShell come descritto in [Procedura:
    Installare Azure
    PowerShell](/en-us/documentation/articles/install-configure-powershell/#install).
2.  Eseguire il comando seguente:
    
         Add-AzureAccount

3.  Nella finestra digitare l'indirizzo di posta elettronica e la
    password associati all'account. Le informazioni delle credenziali
    vengono autenticate e salvate in Azure, quindi la finestra viene
    chiusa.

Per connettersi alla sottoscrizione è anche possibile utilizzare il metodo basato sul certificato. Per istruzioni, vedere [Come installare e configurare Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

## <a name="provision"></a>Eseguire il provisioning di un cluster HDInsight

Per eseguire il processo di provisioning di un cluster HDInsight, è necessario utilizzare un account di archiviazione di Azure come file system predefinito. L'account di archiviazione deve trovarsi nello stesso data center che include le risorse di calcolo di HDInsight. È attualmente possibile eseguire il provisioning di cluster HDInsight solo nei data center seguenti:

* Asia sudorientale
* Europa settentrionale
* Europa occidentale
* Stati Uniti orientali
* Stati Uniti occidentali

È necessario scegliere uno dei cinque data center disponibili per
l'account di archiviazione di Azure.

**Per creare un account di archiviazione di Azure**

1.  Accedere al [portale di gestione di Azure][6]. 2.  Fare clic su **NEW** nell'angolo inferiore sinistro, selezionare
    **DATA SERVICES**, quindi **STORAGE** e infine **QUICK CREATE**.
    
    ![HDI.StorageAccount.QuickCreate](./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png)

3.  Immettere i valori desiderati per **URL**, **LOCATION** e
    **REPLICATION**, quindi fare clic su **CREATE STORAGE ACCOUNT**. I
    gruppi di affinità non sono supportati. Il nuovo account di
    archiviazione verrà incluso nell'elenco di archiviazione.
4.  Attendere che il valore di **STATUS** per il nuovo account di
    archiviazione venga modificato in **Online**.
5.  Fare clic sul nuovo account di archiviazione nell'elenco per
    selezionarlo.
6.  Fare clic su **MANAGE ACCESS KEYS** nella parte inferiore della
    pagina.
7.  Prendere nota dei valori di **STORAGE ACCOUNT NAME** e **PRIMARY
    ACCESS KEY**. Sarà necessario utilizzarli più avanti
    nell'esercitazione.

Per istruzioni dettagliate, vedere [Come creare un account di archiviazione](/en-us/documentation/articles/storage-create-storage-account/) e [Utilizzo dell'archiviazione BLOB di Azure con HDInsight](/en-us/documentation/articles/hdinsight-use-blob-storage/).

**Per eseguire il provisioning di un cluster HDInsight**

1.  Accedere al [portale di gestione di Azure][6].

2.  Fare clic su **HDInsight** a sinistra per elencare lo stato dei
    cluster disponibili nell'account. Nella schermata seguente non è
    disponibile alcun cluster HDInsight.
    
    ![HDI.ClusterStatus](./media/hdinsight-get-started/HDI.ClusterStatus.png)

3.  Fare clic su **NEW** nell'angolo inferiore sinistro, quindi su
    **Data Services**, **HDInsight** e infine su **Quick Create**.
    
    ![HDI.QuickCreateCluster](./media/hdinsight-get-started/HDI.QuickCreateCluster.png)

4.  Immettere o selezionare i valori seguenti:
    
    <table  border="1">
     <tr><th>Nome</th>
    <th>Valore</th>
    </tr>
    
     <tr><td>Cluster Name</td>
    <td>Nome del cluster</td>
    </tr>
    
     <tr><td>Cluster Size</td>
    <td>Numero di nodi di dati che si desidera distribuire. Il valore predefinito è 4, ma nel menu a discesa sono disponibili anche cluster a 8, 16 e 32 nodi di dati. Quando si utilizza l'opzione <strong>Custom Create</strong>
     è possibile specificare un numero qualsiasi di nodi di dati. Sono disponibili i dettagli sui prezzi relativi alle tariffe di fatturazione per le diverse dimensioni di cluster. Fare clic sul simbolo <strong>?</strong>
     immediatamente sopra la casella a discesa, quindi selezionare il collegamento disponibile nel popup.</td>
    </tr>
    
     <tr><td>Password (cluster admin)</td>
    <td>Password per l'account <i>admin</i>
    . Se si utilizza l'opzione Quick Create, il nome utente del cluster sarà "admin" per impostazione predefinita. È possibile modificare tale valore solo se si utilizza la procedura guidata <strong>Custom Create</strong>
    . È necessario immettere nel campo della password almeno 10 caratteri, inclusi una lettera maiuscola, una lettera minuscola, un numero e un carattere speciale.</td>
    </tr>
    
     <tr><td>Storage Account</td>
    <td>Selezionare l'account di archiviazione creato dalla casella a discesa. <br  />
    
    
     > [WACOM.NOTE] > Dopo la selezione, non sarà possibile modificare l'account di archiviazione. In caso di rimozione dell'account di archiviazione, il cluster non sarà più disponibile per l'utilizzo.
    
     Il percorso del cluster HDInsight corrisponderà a quello dell'account di archiviazione.
     </td>
    </tr>
    
     </table>

5.  Fare clic su **Create HDInsight Cluster** in basso a destra. Al
    termine del processo di provisioning, nella colonna relativa allo
    stato verrà visualizzato il valore **Running**.

Per informazioni sull'utilizzo dell'opzione **CUSTOM CREATE**, vedere
[Provisioning di cluster
HDInsight](/en-us/documentation/articles/hdinsight-provision-clusters/).

## <a name="sample"></a>Eseguire un processo WordCount MapReduce

Il provisioning del cluster HDInsight è stato completato. Il passaggio successivo consiste nell'esecuzione di un processo MapReduce per il conteggio delle parole in un file di testo.

Per eseguire un processo MapReduce è necessario disporre degli elementi seguenti:

* Un programma MapReduce. In questa esercitazione verrà utilizzato il
  processo di esempio WordCount, disponibile nella distribuzione del
  cluster HDInsight. Non sarà pertanto necessario scrivere un processo
  personalizzato. Il processo di esempio è disponibile in
  */example/jars/hadoop-examples.jar*. Per istruzioni sulla scrittura di
  un processo MapReduce personalizzato, vedere [Sviluppo di programmi
  MapReduce Java per
  HDInsight](/en-us/documentation/articles/hdinsight-develop-deploy-java-mapreduce/).

* Un file di input. Come file di input verrà utilizzato il file
  */example/data/gutenberg/davinci.txt*. Per informazioni sul
  caricamento di file, vedere [Caricamento di dati in
  HDInsight](/en-us/documentation/articles/hdinsight-upload-data/).
* Una cartella di file di output. Come cartella di file di output verrà
  utilizzata la cartella */example/data/WordCountOutput*. Se non esiste,
  tale cartella verrà creata dal sistema.

Lo schema URI per l'accesso ai file nell'archiviazione BLOB è il seguente:

    wasb[s]://<containername>@<storageaccountname>.blob.core.windows.net/<path>

> [WACOM.NOTE] Per impostazione predefinita, il nome del contenitore
> BLOB utilizzato per il file system predefinito corrisponde al nome del
> cluster HDInsight.

Lo schema URI offre accesso non crittografato mediante il prefisso
*wasb:* e accesso SSL crittografato tramite WASBS. Quando possibile, è
consigliabile utilizzare WASBS, anche per l'accesso a dati presenti nello stesso data center di Azure.

Poiché HDInsight utilizza un contenitore di archiviazione BLOB come file system predefinito, è possibile fare riferimento a file e directory all'interno del file system predefinito utilizzando percorsi relativi o assoluti.

Per accedere, ad esempio, a hadoop-examples.jar, è possibile utilizzare una delle opzioni seguenti:

	* wasb://<containername>@<storageaccountname>.blob.core.windows.net/example/jars/hadoop-examples.jar
    * wasb:///example/jars/hadoop-examples.jar
    * /example/jars/hadoop-examples.jar

L'utilizzo del prefisso *wasb://* nei percorsi di tali file è necessario per indicare che si utilizza l'archiviazione BLOB di Azure per i file di input e output. La directory di output presuppone un percorso predefinito relativo alla cartella
*wasb:///utente/<nomeutente>*.

Per ulteriori informazioni, vedere [Utilizzo dell'archiviazione BLOB di Azure con HDInsight](/en-us/documentation/articles/hdinsight-use-blob-storage/).

**Per eseguire l'esempio WordCount**

1.  Aprire **Azure PowerShell**. Per istruzioni sull'apertura della
    finestra della console Azure PowerShell, vedere [Come installare e
    configurare Azure
    PowerShell](/en-us/documentation/articles/install-configure-powershell/).

2.  Per impostare le variabili, eseguire i comandi seguenti.
    
         $subscriptionName = "<SubscriptionName>" 
         $clusterName = "<HDInsightClusterName>"        

3.  Per creare una definizione del processo MapReduce, eseguire i comandi seguenti: 4.  
		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"
    
    Il file hadoop-examples.jar è incluso nella distribuzione del
    cluster HDInsight. Per il processo MapReduce sono disponibili due
    argomenti. Il primo argomento è il nome del file di origine, il
    secondo è il percorso del file di output. Il file di origine è
    incluso nella distribuzione del cluster HDInsight e il percorso del
    file di output verrà creato in fase di esecuzione.

4.  Per inviare il processo MapReduce, eseguire il comando seguente: 5.  
		# Submit the job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 
    
    Oltre alla definizione del processo MapReduce, è necessario
    specificare anche il nome del cluster HDInsight in cui si desidera
    eseguire il processo MapReduce.
    
    Start-AzureHDInsightJob<em> è una chiamata non sincronizzata.</em>
    Per verificare il completamento del processo, utilizzare il cmdlet
    *Wait-AzureHDInsightJob*.

5.  Per verificare il completamento del processo MapReduce, eseguire il
    comando seguente:
    
         Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

6.  Per verificare se si sono verificati errori nel processo MapReduce, eseguire il comando seguente: 7.  
		# Get the job output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError
    
    Nella schermata seguente viene mostrato l'output di un'esecuzione
    completata correttamente. In caso di esito negativo, verranno
    visualizzati alcuni messaggi di errore.
    
    ![HDI.GettingStarted.RunMRJob](./media/hdinsight-get-started/HDI.GettingStarted.RunMRJob.png)

**Per recuperare i risultati del processo MapReduce**

1.  Aprire **Azure PowerShell**. 2.  Per creare una cartella C:\\Tutorials e cambiare la directory
    specificando tale cartella, eseguire i comandi seguenti:
    
         mkdir \Tutorials
         cd \Tutorials
    
    La directory predefinita per Azure Powershell è
    *C:\\Windows\\System32\\WindowsPowerShell\\v1.0*. Per impostazione
    predefinita, non si dispone di autorizzazioni di scrittura per tale
    cartella. È necessario cambiare la directory, specificando una
    cartella per cui si disponga di autorizzazioni di scrittura.

3.  Impostare le tre variabili necessarie nei comandi seguenti, quindi eseguirli:
    
		$subscriptionName = "<SubscriptionName>"       
		$storageAccountName = "<StorageAccountName>"   
		$containerName = "<ContainerName>"	   
    
    L'account di archiviazione di Azure corrisponde all'account creato
    in precedenza in questa esercitazione. L'account di archiviazione
    viene utilizzato per l'hosting del contenitore BLOB utilizzato come
    file system predefinito per il cluster HDInsight. Il nome del
    contenitore di archiviazione BLOB corrisponde in genere al nome del
    cluster HDInsight, a meno che non venga specificato un nome diverso
    durante il provisioning del cluster.

4.  Per creare un oggetto contesto archiviazione di Azure, eseguire i comandi seguenti: 5.  
		# Create the storage account context object
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    
    Il comando *Select-AzureSubscription* consente di impostare la
    sottoscrizione corrente, nel caso in cui siano disponibili più
    sottoscrizioni e non si desideri utilizzare la sottoscrizione
    predefinita.

5.  Per scaricare l'output del processo MapReduce dal contenitore BLOB alla workstation, eseguire il comando seguente: 6.  
		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
    
    La cartella *example/data/WordCountOutput* è la cartella di output
    specificata durante l'esecuzione del processo MapReduce. Il valore
    *part-r-00000* corrisponde al nome file predefinito per l'output
    del processo MapReduce. Il file verrà scaricato nella stessa
    struttura di cartelle nella cartella locale. Ad esempio, nella
    schermata seguente la cartella corrente è la cartella radice C. Il
    file verrà scaricato nella cartella
    *C:\\example\\data\\WordCountOutput\\*.

6.  Per stampare il file di output del processo MapReduce, eseguire il
    comando seguente:
    
         cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"
    
    ![HDI.GettingStarted.MRJobOutput](./media/hdinsight-get-started/HDI.GettingStarted.MRJobOutput.png)
    
    Il processo MapReduce produce un file denominato *part-r-00000*, che
    include le parole e i conteggi. Lo script utilizza il comando
    findstr per elencare tutte le parole contenenti *"there"*.

> [WACOM.NOTE] Se si apre
> *./example/data/WordCountOutput/part-r-00000*,
> un output multilinea dal processo MapReduce, in Blocco note, si noterà
> che il rendering delle interruzioni di riga non è corretto. Si tratta> di un comportamento previsto.

## <a name="powerquery"></a>Effettuare la connessione agli strumenti di Microsoft Business Intelligence

È possibile utilizzare il componente aggiuntivo Power Query per Excel
per esportare l'output da HDInsight a Excel, quindi utilizzare gli strumenti di Microsoft Business Intelligence (BI) per elaborare ulteriormente o visualizzare i risultati. Quando si crea un cluster HDInsight, viene creato anche un contenitore predefinito con nome uguale a quello del cluster nell'account di archiviazione associato al cluster durante la fase di creazione. In tale contenitore vengono inseriti automaticamente alcuni file, inclusa una tabella Hive di esempio. In questa sezione verrà illustrato come importare in Excel i dati inclusi in tale tabella, per consentirne la visualizzazione e l'ulteriore elaborazione.

Per completare questa parte dell'esercitazione, è necessario disporre di Excel 2010 o 2013. Per iniziare, verrà importata la tabella Hive predefinita inclusa in HDInsight.

**Per scaricare Microsoft PowerQuery per Excel**

* Scaricare Microsoft Power Query per Excel dall'[Area download
  Microsoft][7] e installarlo.

**Per importare dati di HDInsight**

1.  Aprire Excel e creare una nuova cartella di lavoro vuota. 2.  Dal menu **Power Query** scegliere **Da altre origini**, quindi fare
    clic su **Da Azure HDInsight**.
    
    ![HDI.GettingStarted.PowerQuery.ImportData](./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png)

3.  Immettere il **Nome account** dell'account di archiviazione BLOB di
    Azure associato al cluster, quindi fare clic su **OK**. Si tratta
    dell'account di archiviazione creato in precedenza
    nell'esercitazione.
4.  Immettere la **Chiave account** per l'account di archiviazione BLOB
    di Azure, quindi fare clic su **Salva**.
5.  Nel riquadro Strumento di navigazione a destra fare doppio clic sul
    nome del contenitore di archiviazione BLOB. Per impostazione
    predefinita, il nome del contenitore è uguale al nome del cluster.

6.  Individuare **part-r-00000** nella colonna **Name** (il percorso è
    *.../example/data/WordCountOutput*), quindi fare clic su **Binary**
    a sinistra di **part-r-00000**.
    
    ![HDI.GettingStarted.PowerQuery.ImportData2](./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png)

7.  Fare clic con il pulsante destro del mouse su **Column1.1**, quindi
    selezionare **Rinomina**.
8.  Cambiare il nome in **Word**. 9.  Ripetere il processo per rinominare la **Column1.2** in **Count**.
    
    ![HDI.GettingStarted.PowerQuery.ImportData3](./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData3.png)

10. Fare clic su **Applica e chiudi** nell'angolo superiore sinistro.
    La query eseguirà quindi l'importazione della tabella Hive in
    Excel.

## <a name="nextsteps"></a>Passaggi successivi

In questa esercitazione si è appreso come eseguire il provisioning di un cluster con HDInsight, eseguire un processo MapReduce su tale cluster e importare i risultati in Excel per un'ulteriore elaborazione e per la visualizzazione grafica mediante gli strumenti di Business Intelligence. Per ulteriori informazioni, vedere gli articoli seguenti:

* [Introduzione all'utilizzo di cluster Hadoop 2.2 con
  HDInsight](/en-us/documentation/articles/hdinsight-get-started-30/)
* [Introduzione a HDInsight
  Emulator](/en-us/documentation/articles/hdinsight-get-started-emulator/)
* [Utilizzo dell'archiviazione BLOB di Azure con
  HDInsight](/en-us/documentation/articles/hdinsight-use-blob-storage/)
* [Amministrazione di HDInsight tramite
  PowerShell](/en-us/documentation/articles/hdinsight-administer-use-powershell/)
* [Caricamento di dati in
  HDInsight](/en-us/documentation/articles/hdinsight-upload-data/)
* [Utilizzo di MapReduce con
  HDInsight](/en-us/documentation/articles/hdinsight-use-mapreduce)
* [Utilizzo di Hive con
  HDInsight](/en-us/documentation/articles/hdinsight-use-hive/)
* [Utilizzo di Pig con
  HDInsight](/en-us/documentation/articles/hdinsight-use-pig/)
* [Utilizzo di Oozie con
  HDInsight](/en-us/documentation/articles/hdinsight-use-oozie/)
* [Sviluppo di programmi di streaming Hadoop in C# per
  HDInsight](/en-us/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
* [Sviluppo di programmi MapReduce Java per
  HDInsight](/en-us/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)



[1]: http://hadoop.apache.org/
[2]: https://www.windowsazure.com/en-us/pricing/purchase-options/
[3]: https://www.windowsazure.com/en-us/pricing/member-offers/
[4]: https://www.windowsazure.com/en-us/pricing/free-trial/
[5]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[6]: https://manage.windowsazure.com/
[7]: http://www.microsoft.com/en-us/download/details.aspx?id=39379