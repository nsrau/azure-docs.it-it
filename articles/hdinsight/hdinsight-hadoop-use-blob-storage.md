---
title: Eseguire query sui dati da un&quot;Archiviazione di Azure compatibile con HDFS | Microsoft Docs
description: Informazioni su come eseguire query sui dati da Archiviazione di Azure e Azure Data Lake Store per archiviare i risultati dell&quot;analisi.
keywords: archiviazione BLOB, hdfs, dati strutturati, dati non strutturati, Data Lake Store
services: hdinsight,storage
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 1d2e65f2-16de-449e-915f-3ffbc230f815
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/27/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: f85f6484db177285e0fd64a15991a7eb227cd29b
ms.lasthandoff: 03/21/2017


---
# <a name="use-hdfs-compatible-storage-with-hadoop-in-hdinsight"></a>Usare un'archiviazione compatibile con HDFS con Hadoop in HDInsight

Per analizzare i dati in un cluster HDInsight è possibile archiviarli in Archiviazione di Azure, Azure Data Lake Store o in entrambe le soluzioni. Entrambe le opzioni di archiviazione consentono l'eliminazione sicura dei cluster HDInsight usati per i calcoli, senza perdita di dati utente.

Hadoop supporta una nozione del file system predefinito. Il file system predefinito implica uno schema e un'autorità predefiniti e può essere usato anche per risolvere percorsi relativi. Durante il processo di creazione del cluster HDInsight è possibile specificare un contenitore BLOB in Archiviazione di Azure come file system predefinito. In alternativa, con HDInsight 3.5 è possibile selezionare Archiviazione di Azure o Azure Data Lake Store come file system predefinito.

Questo articolo illustra l'uso delle due opzioni di archiviazione con i cluster HDInsight. Per altre informazioni sulla creazione di un cluster HDInsight, vedere [Introduzione a HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

## <a name="using-azure-storage-with-hdinsight-clusters"></a>Uso di Archiviazione di Azure con cluster HDInsight

Archiviazione di Azure è una soluzione di archiviazione affidabile, con finalità generali che si integra facilmente con HDInsight. HDInsight può usare un contenitore BLOB in Archiviazione di Azure come file system predefinito per il cluster. Grazie a un'interfaccia HDFS (Hadoop Distributed File System), tutti i componenti disponibili in HDInsight possono agire direttamente sui dati strutturati o non strutturati archiviati come BLOB.

> [!WARNING]
> Sono disponibili diverse opzioni per la creazione di un account di archiviazione di Azure. La tabella seguente offre informazioni sulle opzioni supportate con HDInsight:
> 
> | Tipo di account di archiviazione | Livello di archiviazione | Supportato con HDInsight |
> | ------- | ------- | ------- |
> | Account di archiviazione di uso generico | Standard | __Sì__ |
> | &nbsp; | Premium | No |
> | Account di archiviazione BLOB | Accesso frequente | No |
> | &nbsp; | Accesso sporadico | No |

### <a name="hdinsight-storage-architecture"></a>Architettura di archiviazione di HDInsight
Nel diagramma seguente viene mostrata una visualizzazione astratta dell'architettura di archiviazione di HDInsight:

I ![cluster Hadoop usano l'API HDFS per accedere e archiviare dati strutturati e non strutturati nell'archiviazione BLOB.](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "Architettura di archiviazione di HDInsight")

HDInsight offre accesso al file system distribuito collegato localmente ai nodi di calcolo. Il file system è accessibile tramite l'URI completo, ad esempio:

    hdfs://<namenodehost>/<path>

HDInsight offre anche la possibilità di accedere ai dati archiviati in Archiviazione di Azure. La sintassi è:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Di seguito sono riportate alcune considerazioni sull'uso di un account di Archiviazione di Azure con i cluster HDInsight.

* **Contenitori negli account di archiviazione connessi a un cluster:** poiché il nome e la chiave dell'account sono associati al cluster durante il processo di archiviazione, si disporrà di un accesso completo ai BLOB presenti in tali contenitori.

* **Contenitori pubblici o BLOB pubblici negli account di archiviazione NON connessi a un cluster:** si disporrà di un accesso in sola lettura ai BLOB presenti in tali contenitori.
  
  > [!NOTE]
  > Un contenitore pubblico consente di ottenere un elenco di tutti i BLOB disponibili al suo interno, nonché i metadati del contenitore stesso. È possibile accedere a un BLOB pubblico solo se ne conosce l'URL esatto. Per altre informazioni, vedere <a href="http://msdn.microsoft.com/library/windowsazure/dd179354.aspx">Limitare l'accesso a contenitori e Blob</a>.
  > 
  > 
* **Contenitori privati negli account di archiviazione NON connessi a un cluster:** non è possibile accedere ai BLOB nei contenitori a meno che non sia stato definito l'account di archiviazione quando sono stati inviati i processi WebHCat. Questo concetto verrà spiegato più avanti nell'articolo.

Gli account di archiviazione definiti durante il processo di creazione, con le rispettive chiavi, sono archiviati in %HADOOP_HOME%/conf/core-site.xml nei nodi del cluster. Il comportamento predefinito di HDInsight consiste nell'usare gli account di archiviazione definiti nel file core-site.xml. Non è consigliabile modificare direttamente il file core-site.xml perché è possibile che venga ricreata l'immagine del nodo head del cluster (master) oppure che ne venga eseguita la migrazione in qualsiasi momento, e le eventuali modifiche apportate al file non sono persistenti.

Più processi WebHCat, inclusi Hive, MapReduce, lo streaming Hadoop e Pig, possono includere una descrizione degli account di archiviazione e dei metadati (questo è valido solo per Pig con account di archiviazione, non per i metadati). La sezione [Accedere ai BLOB con Azure PowerShell](#powershell) di questo articolo contiene un esempio di questa funzionalità. Per altre informazioni, vedere [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx) (Uso di un cluster HDInsight con account di archiviazione e metastore alternativi).

I BLOB possono essere usati per i dati strutturati e non strutturati. I contenitori BLOB archiviano i dati come coppie chiave-valore e non esiste una gerarchia di directory. È tuttavia possibile usare il carattere barra ( / ) all'interno del nome della chiave per far sembrare che un file sia archiviato in una struttura di directory. Ad esempio, la chiave di un BLOB potrebbe essere *input/log1.txt*. Non esiste realmente una directory *input* ma, grazie alla presenza del carattere barra, il nome della chiave ha l'aspetto di un percorso di file.

### <a id="benefits"></a>Vantaggi di Archiviazione di Azure
I costi impliciti in termini di prestazioni associati alla mancata disponibilità di cluster di elaborazione e risorse di archiviazione in un ambiente condiviso sono mitigati dalla modalità di creazione dei cluster di elaborazione, in prossimità delle risorse degli account di archiviazione nell'area di Azure. Qui, grazie alla rete ad alta velocità, l'accesso ai dati in Archiviazione di Azure da parte dei nodi di calcolo è estremamente efficiente.

L'archiviazione dei dati in Archiviazione di Azure anziché in HDFS offre numerosi vantaggi:

* **Riuso e condivisione dei dati:** i dati in HDFS sono situati all'interno del cluster di calcolo. Solo le applicazioni che hanno accesso al cluster di calcolo, quindi, possono usare i dati tramite le API HDFS. È possibile accedere ai dati in Archiviazione di Azure tramite le API HDFS o tramite le [API REST dell'archiviazione BLOB][blob-storage-restAPI]. Di conseguenza, è possibile produrre e usare i dati mediante un set più ampio di strumenti e applicazioni, compresi altri cluster HDInsight.
* **Archiviazione dei dati:** l'archiviazione dei dati in Archiviazione di Azure consente l'eliminazione sicura dei cluster HDInsight usati per i calcoli, senza perdita di dati utente.
* **Costo di archiviazione dei dati:** l'archiviazione a lungo termine dei dati in DFS è più costosa rispetto ad Archiviazione di Azure, dato che il costo di un cluster di elaborazione è più alto di quello di Archiviazione di Azure. Poiché, inoltre, non è necessario ricaricare i dati ogni volta che si genera un cluster di calcolo, si risparmiano anche i costi di caricamento dei dati.
* **Scalabilità orizzontale elastica:** anche se HDFS offre scalabilità orizzontale del file system, la scala è determinata dal numero di nodi di cui si effettua la creazione per il cluster. Modificare la scalabilità può diventare quindi un processo più complicato rispetto al semplice uso delle funzionalità di scalabilità elastica automaticamente disponibili in Archiviazione di Azure.
* **Replica geografica:** è possibile eseguire la replica geografica di Archiviazione di Azure. Sebbene questo offra ripristino geografico e ridondanza dei dati, un failover nella posizione sottoposta a replica geografica incide molto negativamente sulle prestazioni e può comportare costi aggiuntivi. È pertanto consigliabile scegliere la scelta della replica geografica con oculatezza e solo se il valore dei dati giustifica i costi aggiuntivi.

Alcuni pacchetti e processi MapReduce possono creare risultati intermedi che non vale la pena di archiviare in Archiviazione di Azure. In questo caso, è possibile scegliere di archiviare i dati nel file system HDFS locale. In effetti, HDInsight utilizza DFS per molti di questi risultati intermedi nei processi Hive e in altri processi.

> [!NOTE]
> La maggior parte dei comandi HDFS, ad esempio <b>ls</b>, <b>copyFromLocal</b> e <b>mkdir</b>, funziona come previsto. Solo i comandi specifici dell'implementazione HDFS nativa (denominata DFS), ad esempio <b>fschk</b> e <b>dfsadmin</b>, hanno un comportamento diverso in Archiviazione di Azure.
> 
> 

### <a name="create-blob-containers"></a>Creare dei contenitori BLOB
Per usare i BLOB, è necessario creare innanzitutto un [account di archiviazione di Azure][azure-storage-create]. Come parte della procedura, è necessario specificare l'area di Azure in cui viene creato l'account di archiviazione. L'account di archiviazione deve trovarsi nella stessa area del cluster. Il database SQL Server del metastore Hive, inoltre, deve trovarsi nella stessa area del database SQL Server del metastore Oozie.

Ovunque si trovi, ogni oggetto BLOB creato appartiene a un contenitore presente nell'account di archiviazione di Azure. Può trattarsi di un contenitore BLOB esistente, creato all'esterno di HDInsight, oppure di un contenitore creato per un cluster HDInsight.

Il contenitore Blob predefinito archivia informazioni specifiche del cluster, come i log e la cronologia processo. Non condividere un contenitore BLOB predefinito con più cluster HDInsight. Questa operazione potrebbe danneggiare la cronologia processo. È consigliabile utilizzare un contenitore diverso per ogni cluster e inserire i dati condivisi in un account di archiviazione collegato specificato nella distribuzione di tutti i cluster rilevanti, anziché l'account di archiviazione predefinito. Per altre informazioni sulla configurazione degli account di archiviazione collegati, vedere [Creare cluster HDInsight][hdinsight-creation]. È comunque possibile riusare un contenitore di archiviazione predefinito dopo l'eliminazione del cluster HDInsight originale. Per i cluster HBase è in realtà possibile mantenere i dati e lo schema della tabella HBase creando un nuovo cluster HBase tramite il contenitore BLOB predefinito usato da un cluster HBase eliminato.

#### <a name="using-the-azure-portal"></a>Uso del portale di Azure
Durante la creazione di un cluster HDInsight dal portale, sono disponibili le opzioni per indicare i dettagli dell'account di archiviazione, come mostrato di seguito. È inoltre possibile specificare se si desidera associare un account di archiviazione aggiuntivo al cluster e, in caso, se sceglierlo da Data Lake Store o da un altro BLOB del servizio di archiviazione di Azure.

![Origine dati della creazione di HDInsight Hadoop](./media/hdinsight-hadoop-use-blob-storage/hdinsight.provision.data.source.png)

#### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

Se è stata [installata e configurata l’interfaccia della riga di comando di Azure](../cli-install-nodejs.md), il comando seguente può essere usato per un account di archiviazione e un contenitore.

    azure storage account create <storageaccountname> --type LRS

> [!NOTE]
> Il parametro `--type` indica la modalità di replica dell'account di archiviazione. Per altre informazioni, vedere [Replica di Archiviazione di Azure](../storage/storage-redundancy.md). Non utilizzare ZRS poiché ZRS non supporta BLOB di pagine, file, tabelle o code.
> 
> 

Verrà chiesto di specificare l'area geografica in cui verrà creato l'account di archiviazione. È necessario creare l'account di archiviazione nella stessa area in cui si prevede di creare il cluster HDInsight.

Dopo aver creato l'account di archiviazione, usare il comando seguente per recuperare le chiavi dell’account di archiviazione:

    azure storage account keys list <storageaccountname>

Per creare un contenitore, usare il comando seguente:

    azure storage container create <containername> --account-name <storageaccountname> --account-key <storageaccountkey>

#### <a name="using-azure-powershell"></a>Uso di Azure PowerShell
Se [Azure PowerShell è stato installato e configurato][powershell-install], è possibile usare il comando seguente del prompt di Azure PowerShell per creare un account di archiviazione e un contenitore:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"

    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"

    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionID

    # Create resource group
    New-AzureRmResourceGroup -name $ResourceGroupName -Location $Location

    # Create default storage account
    New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS 

    # Create default blob containers
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer -Name $containerName -Context $destContext

### <a name="address-files-in-azure-storage"></a>Accedere ai file in Archiviazione di Azure
Lo schema URI per l'accesso ai file in Archiviazione di Azure da HDInsight è il seguente:

    wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>

Lo schema URI offre l'accesso non crittografato (con il prefisso *wasb:*) e l'accesso con crittografia SSL (con il prefisso *wasbs*). Se possibile, è consigliabile usare *wasbs* anche per accedere ai dati presenti nella stessa area di Azure.

&lt;BlobStorageContainerName&gt; identifica il nome del contenitore BLOB in Archiviazione di Azure.
&lt;StorageAccountName&gt; identifica il nome dell'account di archiviazione di Azure. È necessario specificare un nome di dominio completo (FQDN).

Se non viene specificato &lt;BlobStorageContainerName&gt; né &lt;StorageAccountName&gt;, verrà usato il file system predefinito. Per i file presenti nel file system predefinito è possibile usare un percorso relativo o un percorso assoluto. Ad esempio, è possibile fare riferimento al file *hadoop-mapreduce-examples.jar* nei cluster HDInsight nei due modi seguenti:

    wasbs://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
    wasbs:///example/jars/hadoop-mapreduce-examples.jar
    /example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> Nei cluster HDInsight versione 2.1 e 1.6 il nome del file è <i>hadoop-examples.jar</i>.
> 
> 

&lt;path&gt; è il nome del percorso HDFS del file o della directory. Poiché i contenitori in Archiviazione di Azure sono semplicemente archivi chiave-valore, non esiste un vero file system gerarchico. Un carattere barra ( / ) all'interno di una chiave BLOB viene interpretato come un separatore di directory. Ad esempio, il nome BLOB di *hadoop-mapreduce-examples.jar* è:

    example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> Quando si usano i BLOB al di fuori di HDInsight, la maggior parte delle utilità non riconosce il formato WASB e richiede invece un formato del percorso di base, ad esempio `example/jars/hadoop-mapreduce-examples.jar`.
> 
> 

### <a name="access-blobs-using-azure-cli"></a>BLOB di accesso mediante l’interfaccia della riga di comando di Azure
Usare il comando seguente per ottenere un elenco dei comandi relativi ai BLOB:

    azure storage blob

**Esempio di uso dell’interfaccia della riga di comando di Azure per caricare un file**

    azure storage blob upload <sourcefilename> <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**Esempio di uso dell’interfaccia della riga di comando di Azure per scaricare un file**

    azure storage blob download <containername> <blobname> <destinationfilename> --account-name <storageaccountname> --account-key <storageaccountkey>

**Esempio di uso dell’interfaccia della riga di comando di Azure per eliminare un file**

    azure storage blob delete <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**Esempio di uso dell’interfaccia della riga di comando di Azure per elencare file**

    azure storage blob list <containername> <blobname|prefix> --account-name <storageaccountname> --account-key <storageaccountkey>

### <a name="access-blobs-using-azure-powershell"></a>Accedere ai BLOB con Azure PowerShell
> [!NOTE]
> I comandi in questa sezione forniscono un esempio di base dell'utilizzo di PowerShell per accedere ai dati archiviati nei BLOB. Per un esempio completo personalizzato per l'utilizzo di HDInsight, vedere [Strumenti HDInsight](https://github.com/Blackmist/hdinsight-tools).
> 
> 

Usare il comando seguente per ottenere un elenco dei cmdlet relativi ai BLOB:

    Get-Command *blob*

![Elenco dei cmdlet PowerShell correlati al BLOB.][img-hdi-powershell-blobcommands]

#### <a name="upload-files"></a>Caricare file
Vedere[Caricare dati in HDInsight][hdinsight-upload-data].

#### <a name="download-files"></a>Download dei file
Lo script seguente consente di scaricare un BLOB in blocchi nella cartella corrente. Prima di eseguire lo script, modificare la directory in una cartella per cui si dispone di autorizzazioni di scrittura.

    $resourceGroupName = "<AzureResourceGroupName>"
    $storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at creation.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.

    # Use Add-AzureAccount if you haven't connected to your Azure subscription
    Login-AzureRmAccount 
    Select-AzureRmSubscription -SubscriptionID "<Your Azure Subscription ID>"

    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force

    Write-Host "List the downloaded file ..." -ForegroundColor Green
    cat "./$blob"

Fornendo il nome del gruppo di risorse e il nome del cluster, è possibile utilizzare il codice seguente:

    $resourceGroupName = "<AzureResourceGroupName>"
    $clusterName = "<HDInsightClusterName>"
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.

    $cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
    $defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
    $defaultStorageContainer = $cluster.DefaultStorageContainer
    $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 

    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob $blob -Context $storageContext -Force

#### <a name="delete-files"></a>Eliminare file
    Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob

#### <a name="list-files"></a>Elencare file
    Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix "example/data/"

#### <a name="run-hive-queries-using-an-undefined-storage-account"></a>Esecuzione di query Hive utilizzando un account di archiviazione non definito
Questo esempio illustra come elencare una cartella da un account di archiviazione non definito durante il processo di creazione.
$clusterName = "<HDInsightClusterName>"

    $undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
    $undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

    $undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }

    Use-AzureRmHDInsightCluster $clusterName

    $defines = @{}
    $defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

    Invoke-AzureRmHDInsightHiveJob -Defines $defines -Query "dfs -ls wasbs://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"


### <a name="using-additional-storage-accounts"></a>Uso di account di archiviazione aggiuntivi

Durante la creazione di un cluster HDInsight viene specificato l'account di Archiviazione di Azure da associare al cluster. Oltre a questo account di archiviazione, durante il processo di creazione o dopo la creazione di un cluster è possibile aggiungere altri account di archiviazione della stessa sottoscrizione di Azure o di sottoscrizioni di Azure diverse. Per istruzioni sull'aggiunta di altri account di archiviazione, vedere [Creare cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md).


## <a name="using-azure-data-lake-store-with-hdinsight-clusters"></a>Uso di Azure Data Lake Store con i cluster HDInsight

I cluster HDInsight possono usare Azure Data Lake Store in due modi:

* Azure Data Lake Store come risorsa di archiviazione predefinita
* Azure Data Lake Store risorsa di archiviazione aggiuntiva, con BLOB del servizio di archiviazione di Azure come risorsa predefinita.

> [!NOTE]
> L'accesso ad Azure Data Lake Store avviene sempre tramite un canale protetto, pertanto non è presente un nome di schema del file system `adls`. Viene usato sempre `adl`.
> 
> 

### <a name="using-azure-data-lake-store-as-default-storage"></a>Uso di Azure Data Lake Store come risorsa di archiviazione predefinita

Quando si distribuisce HDInsight con Azure Data Lake Store come risorsa di archiviazione predefinita, i file legati al cluster vengono archiviati in Azure Data Lake Store nel percorso seguente:

    adl://mydatalakestore/<cluster_root_path>/

dove `<cluster_root_path>` è il nome di una cartella creata in Azure Data Lake Store. Specificando un percorso radice per ogni cluster, è possibile usare lo stesso account di Azure Data Lake Store per più di un cluster. Pertanto, è possibile disporre di una configurazione in cui:

* Cluster1 può usare il percorso `adl://mydatalakestore/cluster1storage`
* Cluster2 può usare il percorso `adl://mydatalakestore/cluster2storage`

Si noti che entrambi i cluster usano lo stesso account Data Lake Store **mydatalakestore**. Ogni cluster ha accesso al proprio file system radice in Data Lake Store. Più nello specifico, l'esperienza di distribuzione del Portale di Azure richiede di utilizzare un nome di cartella come **/clusters/\<clustername>** per il percorso radice.

#### <a name="accessing-files-from-the-cluster"></a>Accesso ai file dal cluster

Esistono diversi modi per accedere ai file in Azure Data Lake Store da un cluster HDInsight.

* **Uso di nomi completi**. Con questo approccio viene fornito il percorso completo al file a cui si desidera accedere.

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **Uso del formato con percorso abbreviato**. Con questo approccio si sostituisce con adl:/// il percorso fino alla radice del cluster. Nell'esempio precedente, pertanto, è possibile sostituire `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` con `adl:///`.

        adl:///<file path>

* **Uso del percorso relativo**. Con questo approccio viene fornito unicamente il percorso relativo al file a cui si desidera accedere. Ad esempio, se il percorso completo del file è:

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    È possibile accedere al medesimo file sample.log usando invece il percorso relativo.

        /example/data/sample.log

### <a name="using-azure-data-lake-store-as-additional-storage"></a>Uso di Azure Data Lake Store come risorsa di archiviazione aggiuntiva

È anche possibile usare Data Lake Store come risorsa di archiviazione aggiuntiva per il cluster. In questi casi, l'archiviazione predefinita del cluster può essere un BLOB del servizio di archiviazione di Azure o un account Azure Data Lake Store. Se si eseguono processi di HDInsight con i dati archiviati in Azure Data Lake Store come risorsa di archiviazione aggiuntiva, è necessario usare il percorso con nomi completi ai file. Ad esempio:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Si noti che al momento non esiste alcun **cluster_root_path** nell'URL. Infatti, Data Lake Store non è una risorsa di archiviazione predefinita in questo caso. È sufficiente pertanto indicare il percorso ai file.


### <a name="creating-hdinsight-clusters-with-access-to-data-lake-store"></a>Creazione di cluster HDInsight con accesso a Data Lake Store

Seguire i collegamenti seguenti per informazioni dettagliate su come creare cluster HDInsight con accesso a Data Lake Store.

* [Uso del portale](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
* [Uso di PowerShell con Data Lake Store come risorsa di archiviazione predefinita](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Uso di PowerShell con Data Lake Store come risorsa di archiviazione aggiuntiva](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Uso di modelli di Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)


## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato descritto come usare Archiviazione di Azure e Azure Data Lake Store compatibili con HDFS con HDInsight. In questo modo sarà possibile creare soluzioni scalabili di acquisizione e archiviazione a lungo termine dei dati e usare HDInsight per sbloccare le informazioni all'interno dei dati strutturati e non strutturati archiviati.

Per altre informazioni, vedere:

* [Introduzione ad Azure HDInsight][hdinsight-get-started]
* [Introduzione ad Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)
* [Caricare dati in HDInsight][hdinsight-upload-data]
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Usare Pig con HDInsight][hdinsight-use-pig]
* [Usare le firme di accesso condiviso di Archiviazione di Azure per limitare l'accesso ai dati con HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  

