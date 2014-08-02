<properties linkid="manage-services-hdinsight-administer-hdinsight-using-powershell" urlDisplayName="HDInsight Administration" pageTitle="Administer HDInsight using PowerShell | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to perform administrative tasks for the HDInsight clusters using PowerShell." services="hdinsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Administer HDInsight using PowerShell" authors="bradsev" />

Amministrazione di HDInsight tramite PowerShell
===============================================

Azure PowerShell è un ambiente di scripting potente che può essere utilizzato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. In questo articolo verrà illustrato come gestire cluster HDInsight utilizzando una console di Azure PowerShell tramite Windows PowerShell. Per l'elenco dei cmdlet PowerShell per HDInsight, vedere [Documentazione di riferimento di cmdlet di HDInsight](http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx).

**Prerequisiti:**

Per eseguire le procedure descritte nell'articolo, è necessario disporre di quanto segue:

-   Una sottoscrizione di Azure. Azure è una piattaforma basata su sottoscrizione. I cmdlet di PowerShell HDInsight eseguono le attività con la sottoscrizione. Per ulteriori informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto](https://www.windowsazure.com/en-us/pricing/purchase-options/), [Offerte per i membri](https://www.windowsazure.com/en-us/pricing/member-offers/) oppure [Versione di valutazione gratuita](https://www.windowsazure.com/en-us/pricing/free-trial/).

-   Workstation con Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

Contenuto dell'articolo
-----------------------

-   [Provisioning di un cluster](#provision)
-   [Elenco e visualizzazione di cluster](#listshow)
-   [Eliminazione di un cluster](#delete)
-   [Concessione/revoca dell'accesso ai servizi HTTP](#httpservices)
-   [Invio di processi MapReduce](#mapreduce)
-   [Invio di processi Hive](#hive)
-   [Caricamento di dati nell'archiviazione BLOB](#upload)
-   [Download dei dati di output di MapReduce dall'archiviazione BLOB](#download)

Provisioning di un cluster HDInsight
------------------------------------

HDInsight utilizza un contenitore dell'archivio BLOB di Azure come file system predefinito. Per poter creare un cluster HDInsight, sono necessari un account di archiviazione di Azure e un contenitore dell'archiviazione.

**Per creare un account di archiviazione di Azure**

Dopo avere importato il file publishsettings è possibile utilizzare il comando seguente per creare un account di archiviazione:

    # Create an Azure storage account
    $storageAccountName = "<StorageAcccountName>"
    $location = "<Microsoft data center>"           # For example, "West US"

    New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

> [WACOM.NOTE] L'account di archiviazione deve trovarsi nello stesso data center che include il cluster HDInsight. È attualmente possibile eseguire il provisioning di cluster HDInsight solo nei data center seguenti:

><ul>
<li>Asia sudorientale</li>
<li>Europa settentrionale</li>
<li>Europa occidentale</li>
<li>Stati Uniti orientali</li>
<li>Stati Uniti occidentali</li>
</ul>

Per informazioni sulla creazione di un account di archiviazione di Azure mediante il portale di gestione di Azure, vedere [Come creare un account di archiviazione](/en-us/manage/services/storage/how-to-create-a-storage-account/).

Se si dispone già di un account di archiviazione, ma non si conosce il nome account e la chiave dell'account, è possibile utilizzare i comandi seguenti per recuperare le informazioni:

    # List storage accounts for the current subscription
    Get-AzureStorageAccount
    # List the keys for a storage account
    Get-AzureStorageKey <StorageAccountName>

Per i dettagli sull'acquisizione delle informazioni mediante il portale di gestione, vedere la sezione *Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione* di [Come gestire gli account di archiviazione](/en-us/manage/services/storage/how-to-manage-a-storage-account/).

**Per creare il contenitore dell'archiviazione di Azure**

PowerShell non è in grado di creare un contenitore BLOB durante il processo di provisioning di HDInsight. È possibile crearne uno utilizzando lo script seguente:

    $storageAccountName = "<StorageAccountName>"
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $containerName="<ContainerName>"

    # Create a storage context object
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
                                           -StorageAccountKey $storageAccountKey  

    # Create a Blob storage container
    New-AzureStorageContainer -Name $containerName -Context $destContext

**Per eseguire il provisioning di un cluster**

Dopo aver preparato l'account di archiviazione e il contenitore BLOB, è possibile creare un cluster.

    $storageAccountName = "<StorageAccountName>"
    $containerName = "<ContainerName>"

    $clusterName = "<HDInsightClusterName>"
    $location = "<MicrosoftDataCenter>"
    $clusterNodes = <ClusterSizeInNodes>

    # Get the storage account key
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

    # Create a new HDInsight cluster
    New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

Nella schermata seguent è illustrata l'esecuzione dello script:

![HDI.PS.Provision](./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png)

Elenco e visualizzazione di cluster
-----------------------------------

Utilizzare i comandi seguenti per elencare e mostrare i dettagli dei cluster:

**Per visualizzare l'elenco di tutti i cluster nella sottoscrizione corrente**

    Get-AzureHDInsightCluster 

**Per visualizzare i dettagli del cluster specifico nella sottoscrizione corrente**

    Get-AzureHDInsightCluster -Name <ClusterName> 

Eliminazione di un cluster
--------------------------

Utilizzare il comando seguente per eliminare un cluster:

    Remove-AzureHDInsightCluster -Name <ClusterName> 

Concessione/revoca dell'accesso ai servizi HTTP
-----------------------------------------------

Per i cluster HDInsight sono disponibili i servizi Web HTTP seguenti (tutti con endpoint RESTful):

-   ODBC
-   JDBC
-   Ambari
-   Oozie
-   Templeton

Per impostazione predefinita, a questi servizi è concesso l'accesso. L'accesso può essere revocato/concesso, come illustrato nell'esempio seguente:

    Revoke-AzureHDInsightHttpServicesAccess -Name hdiv2 -Location "East US"

Nell'esempio *hdiv2* è un nome del cluster HDInsight.

> [WACOM.NOTE] La concessione/revoca dell'accesso implica la reimpostazione del nome utente e della password del cluster.

È possibile effettuare tale operazione anche tramite il portale di gestione di Azure. Vedere [Amministrazione di HDInsight tramite il portale di gestione](/en-us/documentation/articles/hdinsight-administer-use-management-portal/).

Invio di processi MapReduce
---------------------------

Con la distribuzione dei cluster HDInsight vengono forniti alcuni esempi di MapReduce. Uno degli esempi è relativo al conteggio della frequenza delle parole nei file di origine.

**Per inviare un processo MapReduce**

Lo script di PowerShell seguente consente di inviare il processo di esempio relativo al conteggio delle parole:

    $clusterName = "<HDInsightClusterName>"            

    # Define the MapReduce job
    $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    # Run the job and show the standard error 
    $wordCountJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $_.JobId -StandardError}

> [WACOM.NOTE] Il file *hadoop-examples.jar* è incluso nei cluster HDInsight della versione 2.1. Il file è stato rinominato in *hadoop-mapreduce.jar* nei cluster HDInsight della versione 3.0.

Per ulteriori informazioni sul prefisso WASB, vedere [Utilizzare l'archivio BLOB di Azure per HDInsight][hdinsight- storage].

**Per scaricare l'output del processo MapReduce**

Lo script di PowerShell seguente consente di recuperare l'output del processo MapReduce dall'ultima routine:

    $storageAccountName = "<StorageAccountName>"   
    $containerName = "<ContainerName>"             
        
    # Create the storage account context object
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    # Download the output to local computer
    Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    # Display the output
    cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

Per ulteriori informazioni sullo sviluppo e l'esecuzione di processi MapReduce, vedere [Utilizzo di MapReduce con HDInsight](/en-us/documentation/articles/hdinsight-use-mapreduce/).

Invio di processi Hive
----------------------

La distribuzione di cluster HDInsight include una tabella Hive di esempio denominata *hivesampletable*. È possibile utilizzare una query HiveQL "show tables;" per visualizzare l'elenco delle tabelle Hive in un cluster.

**Per inviare un processo Hive**

Lo script seguente consente di inviare un processo Hive per visualizzare l'elenco delle tabelle Hive:

    $clusterName = "<HDInsightClusterName>"               

    # HiveQL query
    $querystring = @"
        SHOW TABLES;
        SELECT * FROM hivesampletable 
            WHERE Country='United Kingdom'
            LIMIT 10;
        "@
        
        Use-AzureHDInsightCluster -Name $clusterName
    Invoke-Hive $querystring

Il processo Hive visualizzerà innanzitutto le tabelle Hive create nel cluster e i dati restituiti da hivesampletable.

Per ulteriori informazioni sull'utilizzo di Hive, vedere [Utilizzo di Hive con HDInsight](/en-us/documentation/articles/hdinsight-use-hive/).

Caricamento di dati nell'archiviazione BLOB
-------------------------------------------

Vedere [Caricamento di dati in HDInsight](/en-us/documentation/articles/hdinsight-upload-data/).

Download dei dati di output di MapReduce dall'archiviazione BLOB
----------------------------------------------------------------

Vedere la sezione [Invio di processi MapReduce](#mapreduce) in questo articolo.

Vedere anche
------------

-   [Documentazione di riferimento relativa ai cmdlet di HDInsight](http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx)
-   [Amministrazione di HDInsight tramite il portale di gestione](/en-us/documentation/articles/hdinsight-administer-use-management-portal/)
-   [Amministrazione di HDInsight tramite l'interfaccia della riga di comando](/en-us/documentation/articles/hdinsight-administer-use-command-line/)
-   [Provisioning di cluster HDInsight](/en-us/documentation/articles/hdinsight-provision-clusters/)
-   [Caricamento di dati in HDInsight](/en-us/documentation/articles/hdinsight-upload-data/)
-   [Invio di processi Hadoop a livello di codice](/en-us/documentation/articles/hdinsight-submit-hadoop-jobs-programmatically/)
-   [Introduzione all'utilizzo di Azure HDInsight](/en-us/documentation/articles/hdinsight-get-started/)

