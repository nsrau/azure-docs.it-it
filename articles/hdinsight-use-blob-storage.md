<properties urlDisplayName="Blob Storage with  Hadoop in HDInsight" pageTitle="Usare l'archiviazione BLOB con Hadoop in HDInsight | Azure" metaKeywords="" description="Learn how HDInsight uses Blob storage as the underlying data store for HDFS and how you can query data from the store." metaCanonical="" services="storage,hdinsight" documentationCenter="" title="Use Azure Blob storage with Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />




#Usare l'archiviazione BLOB di Azure con Hadoop in HDInsight




Azure HDInsight supporta sia HDFS (Hadoop Distributed Files System) che l'archivio BLOB di Azure per l'archiviazione dei dati. L'archiviazione BLOB è una soluzione Azure per l'archiviazione affidabile e l'utilizzo generale. Include un'interfaccia HDFS completa che offre un'esperienza lineare abilitando per impostazione predefinita l'operatività diretta sui dati dell'intero set di componenti nell'ecosistema Hadoop. L'archiviazione BLOB non è semplicemente una soluzione a basso costo. Consente infatti di eliminare in sicurezza i cluster HDInsight usati per l'elaborazione senza perdita di dati utente. 

> [WACOM.NOTE]	La sintassi *asv://* non è supportata nei cluster HDInsight della versione 3.0 e non sarà supportata nelle versioni successive. Ciò vuol dire che i processi inviati a un cluster HDInsight versione 3.0 che utilizzano esplicitamente la sintassi "asv://" avranno esito negativo. Sarà pertanto necessario usare la sintassi *wasb://*. Avrà inoltre esito negativo qualsiasi processo inviato a un cluster HDInsight versione 3.0 e creato con un metastore esistente che contiene riferimenti espliciti a risorse che utilizzano la sintassi asv://. Sarà necessario ricreare i metastore in modo da fare riferimento alle risorse usando la sintassi.

> [WACOM.NOTE] In HDInsight sono attualmente supportati solo i BLOB in blocchi.

> [WACOM.NOTE]
> La maggior parte dei comandi HDFS, ad esempio <b>ls</b>, <b>copyFromLocal</b>, <b>mkdir</b> e così via funziona come previsto. Solo i comandi specifici dell'implementazione HDFS nativa (denominata DFS) ad esempio <b>fschk</b> and <b>dfsadmin</b> mostreranno un comportamento diverso nell'archivio BLOB di Azure.

Per informazioni sul provisioning di un cluster HDInsight, vedere [Introduzione a HDInsight][hdinsight-get-started] or [Provisioning di cluster HDInsight][hdinsight-provision].

##Contenuto dell'articolo

* [Architettura di archiviazione di HDInsight](#architecture)
* [Vantaggi dell'archivio BLOB di Azure](#benefits)
* [Preparazione di un contenitore per l'archiviazione BLOB](#preparingblobstorage)
* [Riferimenti a file nell'archiviazione BLOB](#addressing)
* [Accesso all'archiviazione BLOB tramite PowerShell](#powershell)
* [Passaggi successivi](#nextsteps)

##<a id="architecture"></a>Architettura di archiviazione di HDInsight
Nel diagramma seguente viene mostrata una visualizzazione astratta dell'architettura di archiviazione di HDInsight:

![HDI.ASVArch](./media/hdinsight-use-blob-storage/HDI.ASVArch.png "HDInsight Storage Architecture")
  
HDInsight offre accesso al file system distribuito collegato localmente ai nodi di calcolo. Il file system è accessibile tramite l'URI completo. ad esempio: 

	hdfs://<namenodehost>/<path>

HDInsight offre inoltre la possibilità di accedere ai dati archiviati nell'archiviazione BLOB. La sintassi per accedere all'archiviazione BLOB è la seguente:

	wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>


Hadoop supporta una nozione del file system predefinito. Il file system predefinito implica uno schema e un'autorità predefiniti e può inoltre essere usato per risolvere i percorsi relativi. Nel corso del processo di provisioning di HDInsight, un account di archiviazione di Azure e uno specifico contenitore di archiviazione BLOB di tale account vengono designati come file system predefinito.

Durante il processo di provisioning è possibile aggiungere a tale account di archiviazione ulteriori account, dalla stessa sottoscrizione di Azure o da sottoscrizioni diverse. Per istruzioni sull'aggiunta di ulteriori account di archiviazione, vedere [Provisioning di cluster HDInsight][hdinsight-provision]. 

- **Contenitori negli account di archiviazione connessi a un cluster   :** poiché il nome e la chiave dell'account sono archiviati in *core-site.xml*, si disporrà di accesso completo ai BLOB in tali contenitori.
- **Contenitori pubblici o BLOB pubblici negli account di archiviazione NON connessi a un cluster:** si disporrà di accesso in sola lettura ai BLOB in tali contenitori.

	> [WACOM.NOTE]
        > Un contenitore pubblico consente di ottenere un elenco di tutti i BLOB disponibili al suo interno, nonché i metadati del contenitore stesso. È possibile  accedere a un BLOB pubblico solo se se ne conosce l'URL esatto. Per altre informazioni, vedere <a href="http://msdn.microsoft.com/it-it/library/windowsazure/dd179354.aspx">Limitare l'accesso a contenitori e Blob</a>.

- **Contenitori privati negli account di archiviazione NON connessi a un cluster:** Non è possibile accedere ai BLOB nei contenitori a meno che non sia stato definito l'account di archiviazione quando sono stati inviati i processi WebHCat. Ciò verrà spiegato più avanti in questo articolo.


Gli account di archiviazione definiti nel processo di provisioning e le relative chiavi sono archiviati in %HADOOP_HOME%/conf/core-site.xml.  Il comportamento predefinito di HDInsight determina l'utilizzo degli account di archiviazione definiti nel file core-site.xml. Non è consigliabile modificare il file core-site.xml perché è possibile che venga ricreata l'immagine del nodo head del cluster (master) oppure che ne venga eseguita la migrazione in qualsiasi momento, e le eventuali modifiche apportate a tali file andranno perse.

Più processi WebHCat, inclusi Hive, MapReduce, lo streaming Hadoop e Pig, possono includere una descrizione degli account di archiviazione e dei metadati (attualmente ciò funziona per Pig con gli account di archiviazione, ma non per i metadati). La sezione [Accesso all'archiviazione BLOB tramite PowerShell](#powershell) di questo articolo contiene un esempio di questa funzionalità. Per altre informazioni, vedere [Uso di un cluster HDInsight con account di archiviazione e metastore alternativi](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

I contenitori di archiviazione BLOB archiviano i dati come coppie chiave-valore e non esiste una gerarchia di directory. È tuttavia possibile usare il carattere "/" all'interno del nome della chiave per far sembrare che un file sia archiviato in una struttura di directory. Ad esempio, la chiave di un BLOB potrebbe essere *input/log1.txt*. Non esiste realmente una directory *input*, ma grazie alla presenza del carattere "/", il nome della chiave ha l'aspetto di un percorso di file.










##<a id="benefits"></a>Vantaggi dell'archivio BLOB di Azure
I costi impliciti in termini di prestazioni associati al non disporre di risorse di elaborazione e di storage in ambiente condiviso sono mitigati dalla modalità di provisioning dei cluster di calcolo, in prossimità delle risorse degli account di archiviazione nel data center di Azure. Qui, grazie alla rete ad alta velocità, l'accesso ai dati nell'archiviazione BLOB da parte dei nodi di calcolo è estremamente efficiente.

L'archiviazione dei dati nell'archiviazione BLOB anziché in HDFS offre numerosi vantaggi:

* **Riutilizzo e condivisione dei dati:** i dati in HDFS sono situati all'interno del cluster di calcolo. Solo le applicazioni che hanno accesso al cluster di calcolo possono usare i dati usando l'API HDFS. È possibile accedere ai dati nell'archiviazione BLOB tramite le API HDFS o tramite le [API REST del servizio Blob][blob-storage-restAPI]. Di conseguenza, è possibile produrre e usare i dati mediante un set più ampio di strumenti e applicazioni, compresi altri cluster HDInsight.
* **Archiviazione dei dati:** l'archiviazione dei dati nell'archiviazione BLOB consente pertanto l'eliminazione sicura dei cluster HDInsight usati per i calcoli, senza perdita di dati utente. 
* **Costo di archiviazione dei dati:** l'archiviazione a lungo termine dei dati in DFS è più costosa rispetto all'archiviazione BLOB, in quanto il costo di un cluster di calcolo è più alto di quello di un contenitore di archiviazione BLOB. Poiché inoltre non è necessario ricaricare i dati ogni volta che si genera un cluster di calcolo, si risparmiano i costi di caricamento dei dati.
* **Scalabilità orizzontale elastica:** anche se HDFS offre scalabilità orizzontale del file system, la scala è determinata dal numero di nodi di cui si effettua il provisioning per il cluster. Modificare la scala può diventare un processo più complicato rispetto al semplice fare affidamento sulla scalabilità elastica offerta automaticamente dall'archiviazione BLOB.
* **Replica geografica:** è possibile effettuare la replica geografica dei contenitori di archiviazione BLOB tramite il portale di Azure. Sebbene questo offra ripristino geografico e ridondanza dei dati, un failover nella posizione sottoposta a replica geografica inciderà molto negativamente sulle prestazioni e può comportare costi aggiuntivi. È pertanto consigliabile scegliere la scelta della replica geografica con oculatezza e solo se il valore dei dati giustifica i costi aggiuntivi.

Alcuni pacchetti e processi MapReduce possono creare risultati intermedi che non vale la pena di archiviare nel contenitore di archiviazione BLOB. In questo caso, è comunque possibile scegliere di archiviare i dati nel file system HDFS locale. In effetti, HDInsight utilizza DFS per molti di questi risultati intermedi nei processi Hive e in altri processi. 





##<a id="preparingblobstorage"></a>Preparazione di un contenitore per l'archiviazione BLOB
Per usare i BLOB, è necessario creare innanzitutto un [account di archiviazione di Azure][azure-storage-create]. Nel corso di questa procedura si specifica il data center di Azure in cui verranno archiviati gli oggetti creati usando questo account. Sia il cluster che l'account di archiviazione devono essere ospitati nello stesso data center. Anche il database SQL del metastore Hive e il database SQL del metastore Oozie devono trovarsi nello stesso data center. Dovunque si trovi, ogni oggetto BLOB creato appartiene a un contenitore nell'account di archiviazione. Può trattarsi di u contenitore di archiviazione BLOB creato all'esterno di HDInsight, oppure di un contenitore creato per un cluster HDInsight. 



###Creare un contenitore BLOB per HDInsight usando il portale di gestione

Quando si effettua il provisioning di un cluster HDInsight dal portale di gestione di Azure sono disponibili due opzioni: *creazione rapida* e *creazione personalizzata*. L'opzione di creazione rapida richiede la creazione anticipata di un account di archiviazione di Azure.  Per istruzioni, vedere [Come creare un account di archiviazione][azure-storage-create]. 

Usando l'opzione di creazione rapida è possibile scegliere un account di archiviazione esistente. Il processo di provisioning crea un nuovo contenitore con lo stesso nome del cluster HDInsight. Se un contenitore con lo stesso nome esiste già, verrà usato <nomeCluster>-<x>. Ad esempio, myHDIcluster-1. Tale contenitore viene usato come file system predefinito.

![HDI.QuickCreate][img-hdi-quick-create]
 
Usando l'opzione di creazione personalizzata si avrà una delle opzioni seguenti per l'account di archiviazione predefinito:

- Usare l'archiviazione esistente
- Crea nuova archiviazione
- Usare l'archiviazione da un'altra sottoscrizione.

Sarà anche possibile creare il proprio contenitore BLOB oppure usarne uno esistente.
 
![HDI.CustomCreateStorageAccount][img-hdi-custom-create-storage-account]
  




### Creare un contenitore usando Azure PowerShell.
[È possibile usare Azure PowerShell][powershell-install] per la creazione di contenitori BLOB. Di seguito è riportato un esempio di script di PowerShell:

	$subscriptionName = "<SubscriptionName>"	# Azure subscription name
	$storageAccountName = "<AzureStorageAccountName>" # The storage account that you will create
	$containerName="<BlobContainerToBeCreated>" # The Blob container name that you will create

	# Connect to your Azure account and selec the current subscription
	Add-AzureAccount # The connection will expire in a few hours.
	Select-AzureSubscription $subscriptionName #only required if you have multiple subscriptions
	
	# Create a storage context object
	$storageAccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext 


##<a id="addressing"></a>Riferimenti a file nell'archiviazione BLOB

Lo schema URI per l'accesso ai file nell'archiviazione BLOB è il seguente: 

	wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


> [WACOM.NOTE] La sintassi per fare riferimento ai file nell'emulatore di archiviazione eseguito in HDInsight Emulator è <i>wasb://&lt;ContainerName&gt;@storageemulator</i>.



Lo schema URI offre accesso non crittografato mediante il prefisso *wasb:* e accesso SSL crittografato tramite *wasbs*. Quando possibile, è consigliabile usare *wasbs* anche per l'accesso a dati presenti nello stesso data center di Azure.
	
&lt;BlobStorageContainerName&gt; identifica il nome del contenitore di archiviazione BLOB.
&lt;StorageAccountName&gt; identifica il nome dell'account di archiviazione di Azure. È necessario specificare un nome di dominio completo (FQDN).
	
Se non viene specificato &lt;BlobStorageContainerName&gt; né &lt;StorageAccountName&gt; verrà usato il file system predefinito. Per i file nel file system predefinito è possibile usare un percorso relativo o un percorso assoluto. Ad esempio, è possibile fare riferimento al file hadoop-mapreduce-examples.jar incluso nei cluster HDInsight nei due modi seguenti:

	wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
	wasb:///example/jars/hadoop-mapreduce-examples.jar
	/example/jars/hadoop-mapreduce-examples.jar
	
> [WACOM.NOTE] Nei cluster HDInsight versione 1.6 e 2.1 il nome del file è <i>hadoop-examples.jar</i>.


&lt;path&gt; è il nome del percorso HDFS del file o della directory. Poiché i contenitori di archiviazione BLOB sono semplicemente archivi chiave-valore, non esiste un vero file system gerarchico. Un carattere "/" all'interno di una chiave BLOB viene interpretato come un separatore di directory. Ad esempio, il nome BLOB di *hadoop-mapreduce-examples.jar* è:

	example/jars/hadoop-mapreduce-examples.jar
	

##<a id="powershell"></a>Accesso all'archiviazione BLOB tramite Azure PowerShell

Per informazioni sull'installazione e la configurazione di Azure PowerShell nella workstation, vedere [Come installare e configurare Azure PowerShell][powershell-install]. È possibile usare la finestra della console Azure PowerShell oppure PowerShell_ISE per l'esecuzione dei cmdlet di PowerShell. 

Usare il comando seguente per ottenere un elenco dei cmdlet relativi ai BLOB:

	Get-Command *blob*

![Blob.PowerShell.cmdlets][img-hdi-powershell-blobcommands]


**Esempio di PowerShell per il caricamento di un file**

Vedere [Caricare i dati in HDInsight][hdinsight-upload-data].

**Esempio di PowerShell per il download di un file**

Lo script seguente consente di scaricare un BLOB in blocchi nella cartella corrente. Prima di eseguire lo script, modificare la directory in una cartella per cui si dispone di autorizzazioni di scrittura. 


	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blob = "example/data/sample.log" # The name of the blob to be downloaded.
	
	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours
	
	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"       
	#Select-AzureSubscription $subscriptionName
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	Write-Host "Download the blob ..." -ForegroundColor Green
	Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force
	
	Write-Host "List the downloaded file ..." -ForegroundColor Green
	cat "./$blob"

**Esempio di PowerShell per l'eliminazione di un file**

Nello script seguente viene illustrato come eliminare un file.
	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blob = "example/data/sample.log" # The name of the blob to be downloaded.
	
	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours
	
	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"       
	#Select-AzureSubscription $subscriptionName
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	Write-Host "Delete the blob ..." -ForegroundColor Green
	Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob 
	

**Esempio di PowerShell per ottenere l'elenco dei file in una cartella**

Nello script seguente viene illustrato come elencare i file all'interno di una "cartella". Nell'esempio successivo verrà illustrato come usare il cmdlet Invoke-Hive per eseguire il comando dfs ls che consente di elencare una cartella.

	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blobPrefix = "example/data/"
	
	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours
	
	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"       
	#Select-AzureSubscription $subscriptionName
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	Write-Host "List the files in $blobPrefix ..."
	Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix $blobPrefix

**Esempio di PowerShell per accedere a un account di archiviazione non definito**
	
In questo esempio viene illustrato come elencare una cartella da un account di archiviazione non definito durante il processo di provisioning.
	$clusterName = "<HDInsightClusterName>"
	
	$undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
	$undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"
	
	$undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }
	
	Use-AzureHDInsightCluster $clusterName
	
	$defines = @{}
	$defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

	Invoke-Hive -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"
 
##<a id="nextsteps"></a>Passaggi successivi

In questo articolo si è appreso come usare l'archiviazione BLOB con HDInsight e che l'archiviazione BLOB è un componente fondamentale di HDInsight. In questo modo sarà possibile creare soluzioni scalabili di acquisizione e archiviazione a lungo termine dei dati con l'archivio BLOB di Azure e usare HDInsight per sbloccare le informazioni all'interno dei dati archiviati.

Per altre informazioni, vedere gli articoli seguenti:

* [Introduzione all'utilizzo di Azure HDInsight][hdinsight-get-started]
* [Caricare i dati in HDInsight][hdinsight-upload-data]
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Usare Pig con HDInsight][hdinsight-use-pig]

[Powershell-install]: ../install-configure-powershell/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/

[Powershell-install]: ../install-configure-powershell/
[blob-storage-restAPI]: http://msdn.microsoft.com/it-it/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage-create-storage-account/

[img-hdi-powershell-blobcommands]: ./media/hdinsight-use-blob-storage/HDI.PowerShell.BlobCommands.png 
[img-hdi-quick-create]: ./media/hdinsight-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-use-blob-storage/HDI.CustomCreateStorageAccount.png  
