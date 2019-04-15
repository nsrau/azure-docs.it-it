---
title: Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight
description: Informazioni su come eseguire query sui dati da Azure Data Lake Storage Gen2 e archiviare i risultati dell'analisi.
author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.openlocfilehash: 15d9210e356da6d7757582dc4dad25cb2882ccfa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58003232"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight

Per analizzare i dati in un cluster HDInsight è possibile archiviare i dati in qualsiasi combinazione di Archiviazione BLOB di Azure, Archiviazione BLOB di Azure con Azure Data Lake Storage Gen2 abilitata o Azure Data Lake Storage Gen1. Tutte le opzioni di archiviazione consentono l'eliminazione sicura dei cluster HDInsight utilizzati per i calcoli, senza perdita di dati utente.

Hadoop supporta una nozione del file system predefinito. Il file system predefinito implica uno schema e un'autorità predefiniti e può essere usato anche per risolvere percorsi relativi. Durante il processo di creazione del cluster HDInsight è possibile specificare un contenitore BLOB in Archiviazione di Azure o lo spazio dei nomi gerarchico offerto da Data Lake Storage Gen2 come file system predefinito. In alternativa, con HDInsight 3.5 è possibile selezionare un contenitore o lo spazio dei nomi gerarchico come file system predefinito con alcune eccezioni.

Questo articolo illustra come funziona Data Lake Storage Gen2 con i cluster HDInsight. Per altre informazioni sulla creazione di un cluster HDInsight, vedere [Configurare i cluster di HDInsight usando Archiviazione di Azure Data Lake con Hadoop, Spark, Kafka e altro](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).

Archiviazione di Azure è una soluzione di archiviazione affidabile, con finalità generali che si integra facilmente con HDInsight. HDInsight può usare Archiviazione di Azure Data Lake come file system predefinito per il cluster. Attraverso un'interfaccia HDFS (Hadoop Distributed File System), tutti i componenti disponibili in HDInsight possono agire direttamente sui file in Archiviazione di Azure Data Lake.

Non è consigliabile usare il file system predefinito per l'archiviazione dei dati aziendali. È consigliabile eliminare il file system predefinito dopo ogni utilizzo per ridurre i costi di archiviazione. Si noti che il contenitore predefinito include log di sistema e applicazioni. Assicurarsi di recuperare i log prima di eliminare il contenitore.

La condivisione di un solo file system tra più cluster non è supportata.

## <a name="hdinsight-storage-architecture"></a>Architettura di archiviazione di HDInsight

Nel diagramma seguente viene mostrata una visualizzazione astratta dell'architettura di archiviazione HDInsight dell'uso di Archiviazione di Azure:

I ![cluster Hadoop usano l'API HDFS per accedere e archiviare dati strutturati e non strutturati nell'archiviazione BLOB.](./media/data-lake-storage-use-hdi-cluster/HDI.ABFS.Arch.png "Architettura di archiviazione di HDInsight")

HDInsight offre accesso al file system distribuito collegato localmente ai nodi di calcolo. Il file system è accessibile tramite l'URI completo, ad esempio:

    hdfs://<NAME_NODE_HOST>/<PATH>

HDInsight consente anche di accedere ai dati archiviati in Archiviazione di Azure Data Lake. La sintassi è:

    abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<path>

Di seguito sono riportate alcune considerazioni sull'uso di un account di Archiviazione di Azure con cluster HDInsight.

* Per **i file negli account di archiviazione connessi a un cluster di SQL Server** il nome dell'account e la chiave sono associati al cluster durante la creazione. Questa configurazione consente di accedere ai file nel file system.

* **I file pubblici negli account di archiviazione che NON sono connessi a un cluster** espongono autorizzazioni di sola lettura ai file del file system.
  
  > [!NOTE]
  > I file system pubblici consentono di ottenere un elenco di tutti i file disponibili nel file system e accedere ai metadati. È possibile accedere ai file system pubblici solo se si conosce il loro URL esatto. Per altre informazioni, vedere [Limitare l'accesso a contenitori e BLOB](https://msdn.microsoft.com/library/windowsazure/dd179354.aspx) (le regole per i contenitori e i BLOB funzionano allo stesso modo per i file e il file system).
 
* **File system privati negli account di archiviazione NON connessi a un cluster:** non consentire l'accesso ai file nel file system a meno che non sia stato definito l'account di archiviazione quando sono stati inviati i processi WebHCat. I motivi per questa restrizione sono descritti più avanti in questo articolo.

Gli account di archiviazione definiti durante il processo di creazione, con le rispettive chiavi, sono archiviati in *%HADOOP_HOME%/conf/core-site.xml* nei nodi del cluster. Il comportamento predefinito di HDInsight consiste nell'usare gli account di archiviazione definiti nel file *core-site.xml*. È possibile modificare questa impostazione usando [Ambari](../../hdinsight/hdinsight-hadoop-manage-ambari.md)

Più processi WebHCat, inclusi Hive, MapReduce, lo streaming Hadoop e Pig, possono includere una descrizione degli account di archiviazione e dei metadati (questo approccio è valido solo per Pig con account di archiviazione, non per i metadati). Per altre informazioni, vedere [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx) (Uso di un cluster HDInsight con account di archiviazione e metastore alternativi).

## <a id="benefits"></a>Vantaggi di Archiviazione di Azure

I costi impliciti in termini di prestazioni associati alla mancata disponibilità di cluster di elaborazione e risorse di archiviazione in un ambiente condiviso sono mitigati dalla modalità di creazione dei cluster di elaborazione in prossimità delle risorse degli account di archiviazione nell'area di Azure dove la rete ad alta velocità rende efficiente l'accesso ai dati in Archiviazione di Azure dai nodi di calcolo.

L'archiviazione dei dati in Archiviazione di Azure anziché in HDFS offre numerosi vantaggi:

* **Condivisione e riutilizzo dei dati:** i dati in HDFS sono situati all'interno del cluster di elaborazione. Solo le applicazioni che hanno accesso al cluster di calcolo, quindi, possono usare i dati tramite le API HDFS. È possibile accedere ai dati in Archiviazione di Azure tramite le API HDFS o tramite le [API REST dell'archiviazione BLOB][blob-storage-restAPI]. Di conseguenza, è possibile produrre e usare i dati mediante un set più ampio di strumenti e applicazioni, compresi altri cluster HDInsight.

* **Archiviazione dati:** l'archiviazione dei dati in Archiviazione di Azure consente l'eliminazione sicura dei cluster HDInsight usati per i calcoli, senza perdita di dati utente.

* **Costo di archiviazione dei dati:** l'archiviazione a lungo termine dei dati in HDFS nativo è più costosa rispetto ad Archiviazione di Azure, dato che il costo di un cluster di elaborazione è più alto di quello di Archiviazione di Azure. Poiché, inoltre, non è necessario ricaricare i dati ogni volta che si genera un cluster di calcolo, si risparmiano anche i costi di caricamento dei dati.

* **Scalabilità orizzontale elastica:** anche se Hadoop Distributed File System offre scalabilità orizzontale del file system, la scalabilità è determinata dal numero di nodi creati per il cluster. Modificare la scalabilità può diventare quindi un processo più complicato rispetto al semplice uso delle funzionalità di scalabilità elastica automaticamente disponibili in Archiviazione di Azure.

* **Replica geografica:** è possibile eseguire la replica geografica dei dati di archiviazione di Azure. Anche se questa soluzione offre ripristino geografico e ridondanza dei dati, il supporto di un failover nella posizione sottoposta a replica geografica incide molto negativamente sulle prestazioni e può comportare costi aggiuntivi. È pertanto consigliabile scegliere la replica geografica con attenzione e solo se il valore dei dati giustifica i costi aggiuntivi.

* **Gestione del ciclo di vita dei dati:** tutti i dati in qualsiasi file system passano attraverso uno specifico ciclo di vita. All'inizio i dati hanno una notevole rilevanza e vengono usati di frequente, dopo diventano meno importanti e richiedono una minore frequenza di accesso, infine devono essere archiviati o eliminati. Archiviazione di Azure fornisce criteri di suddivisione dei dati su livelli e gestione del ciclo di vita che dividono i dati in livelli in modo appropriato in base alla fase del loro ciclo di vita.

Alcuni pacchetti e processi MapReduce possono creare risultati intermedi che non vale la pena di archiviare in Archiviazione di Azure. In questo caso, è possibile scegliere di archiviare i dati nel file system HDFS locale. In effetti, HDInsight usa l'implementazione HDFS nativa (denominata DFS) per alcuni di questi risultati intermedi nei processi Hive e in altri processi.

> [!NOTE]
> La maggior parte dei comandi Hadoop Distributed File System, ad esempio `ls`, `copyFromLocal` e `mkdir`, funziona come previsto. Solo i comandi specifici dell'implementazione DFS, ad esempio `fschk` e `dfsadmin`, mostrano un comportamento diverso in Archiviazione di Azure.

## <a name="create-a-data-lake-storage-file-system"></a>Creare un file system di Data Lake Storage

Per usare il file system, è necessario creare innanzitutto un [account di Archiviazione di Microsoft Azure][azure-storage-create]. Come parte della procedura, si deve specificare l'area di Azure in cui viene creato l'account di archiviazione. L'account di archiviazione deve trovarsi nella stessa area del cluster. Il database SQL Server del metastore Hive, inoltre, deve trovarsi nella stessa area del database SQL Server del metastore Oozie.

Ovunque si trovi, ogni oggetto BLOB creato appartiene a un file system presente nell'account di archiviazione.

Il file system predefinito di Data Lake Storage Gen2 archivia informazioni specifiche del cluster come i log e la cronologia processo. Non condividere un file system predefinito di Data Lake Storage Gen2 con più cluster HDInsight. Questa operazione potrebbe danneggiare la cronologia processo. È consigliabile usare un file system diverso per ogni cluster e inserire i dati condivisi in un account di archiviazione collegato specificato nella distribuzione di tutti i cluster interessati, anziché l'account di archiviazione predefinito. Per altre informazioni sulla configurazione degli account di archiviazione collegati, vedere [Creare cluster HDInsight][hdinsight-creation]. È comunque possibile riutilizzare un file system di archiviazione predefinito dopo l'eliminazione del cluster HDInsight originale. Per i cluster HBase è possibile mantenere i dati e lo schema della tabella HBase creando un nuovo cluster HBase tramite il contenitore BLOB predefinito usato da un cluster HBase eliminato.

[!INCLUDE [secure-transfer-enabled-storage-account](../../../includes/hdinsight-secure-transfer.md)]

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

Durante la creazione di un cluster HDInsight dal portale, sono disponibili le opzioni per indicare i dettagli dell'account di archiviazione, come mostrato nella schermata seguente. È inoltre possibile specificare se si desidera associare un account di archiviazione aggiuntivo al cluster e, in caso, scegliere fra le opzioni di archiviazione disponibili per lo spazio di archiviazione aggiuntivo.

![Origine dati della creazione di HDInsight Hadoop](./media/data-lake-storage-use-hdi-cluster/create-storage-account.png)

> [!WARNING]
> L'uso di un account di archiviazione aggiuntivo in una località diversa rispetto al cluster HDInsight non è supportato.

### <a name="use-azure-powershell"></a>Usare Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se [Azure PowerShell è stato installato e configurato][powershell-install], è possibile usare il codice seguente al prompt di Azure PowerShell per creare un account di archiviazione e un contenitore:

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

```azurepowershell
$SubscriptionID = "<Your Azure Subscription ID>"
$ResourceGroupName = "<New Azure Resource Group Name>"
$Location = "westus2"

$StorageAccountName = "<New Azure Storage Account Name>"
$containerName = "<New Azure Blob Container Name>"

Select-AzSubscription -SubscriptionId $SubscriptionID

# Create resource group
New-AzResourceGroup -name $ResourceGroupName -Location $Location

# Create default storage account
New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
  -Name $StorageAccountName `
  -Location $Location `
  -SkuName Standard_LRS `
  -Kind StorageV2 `
  -EnableHierarchicalNamespace $True

# Create default blob containers
$storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
$destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
New-AzStorageContainer -Name $containerName -Context $destContext
```

> [!NOTE]
> La creazione di un contenitore equivale alla creazione di un file system in Data Lake Storage Gen2.

### <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

[!INCLUDE [use-latest-version](../../../includes/hdinsight-use-latest-cli.md)]

Se è stata [installata e configurata l’interfaccia della riga di comando di Azure](../../cli-install-nodejs.md), il comando seguente può essere usato per un account di archiviazione e un contenitore.

```bash
az storage account create \
    --name <STORAGE_ACCOUNT_NAME> \
    --resource-group <RESOURCE_GROUP_NAME> \
    --location westus2 \
    --sku Standard_LRS \
    --kind StorageV2 \
    --Enable-hierarchical-namespace true
```

Verrà chiesto di specificare l'area geografica in cui verrà creato l'account di archiviazione. Creare l'account di archiviazione nella stessa area in cui si prevede di creare il cluster HDInsight.

Dopo aver creato l'account di archiviazione, usare il comando seguente per recuperare le chiavi dell’account di archiviazione:

    azure storage account keys list <STORAGE_ACCOUNT_NAME>

Per creare un contenitore, usare il comando seguente:

    azure storage container create <CONTAINER_NAME> --account-name <STORAGE_ACCOUNT_NAME> --account-key <STORAGE_ACCOUNT_KEY>

> [!NOTE]
> La creazione di un contenitore equivale alla creazione di un file system in Data Lake Storage Gen2.

## <a name="address-files-in-azure-storage"></a>Accedere ai file in Archiviazione di Azure

Lo schema URI per l'accesso ai file in Archiviazione di Azure da HDInsight è il seguente:

    abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>

Lo schema URI offre l'accesso non crittografato (con il prefisso *abfs:*) e l'accesso con crittografia SSL (con il prefisso *abfss*). Se possibile, è consigliabile usare *abfss* anche per accedere ai dati presenti nella stessa area di Azure.

* &lt;FILE_SYSTEM_NAME&gt; identifica il percorso del file system di Data Lake Storage Gen2.
* &lt;ACCOUNT_NAME&gt; identifica il nome dell'account di Archiviazione di Azure. È necessario specificare un nome di dominio completo (FQDN).

    Se i valori di &lt;FILE_SYSTEM_NAME&gt; e &lt;ACCOUNT_NAME&gt; non sono specificati, viene usato il file system predefinito. Per i file presenti nel file system predefinito è possibile usare un percorso relativo o un percorso assoluto. Ad esempio, è possibile fare riferimento al file *hadoop-mapreduce-examples.jar* incluso nei cluster HDInsight usando uno dei percorsi seguenti:
    
        abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
        abfs:///example/jars/hadoop-mapreduce-examples.jar
        /example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> Nei cluster HDInsight versione 2.1 e 1.6 il nome del file è *hadoop-examples.jar*.

* &lt;PATH&gt; è il nome del percorso HDFS del file o della directory.

> [!NOTE]
> Quando si usano file al di fuori di HDInsight, la maggior parte delle utilità non riconosce il formato ABFS e richiede invece un formato del percorso di base, ad esempio `example/jars/hadoop-mapreduce-examples.jar`.
 
## <a name="use-additional-storage-accounts"></a>Usare account di archiviazione aggiuntivi

Durante la creazione di un cluster HDInsight viene specificato l'account di Archiviazione di Azure da associare al cluster. Oltre a questo account di archiviazione, durante il processo di creazione o dopo la creazione di un cluster è possibile aggiungere altri account di archiviazione della stessa sottoscrizione di Azure o di sottoscrizioni di Azure diverse. Per istruzioni sull'aggiunta di altri account di archiviazione, vedere [Creare cluster HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]
> L'uso di un account di archiviazione aggiuntivo in una località diversa rispetto al cluster HDInsight non è supportato.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato descritto come usare una risorsa di archiviazione di Azure compatibile con HDFS con HDInsight. Questo approccio consente di creare soluzioni scalabili di acquisizione e archiviazione dei dati a lungo termine e usare HDInsight per sbloccare le informazioni all'interno dei dati strutturati e non strutturati archiviati.

Per altre informazioni, vedere:

* [Il driver di file System Hadoop ABFS per Archiviazione di Azure Data Lake Gen2](data-lake-storage-abfs-driver.md)
* [Introduzione ad Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
* [Inserire dati in Azure Data Lake Storage Gen2 usando distcp](data-lake-storage-use-distcp.md)

[powershell-install]: /powershell/azure/install-az-ps
[hdinsight-creation]: ../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md

[blob-storage-restAPI]: https://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/data-lake-storage-use-hdi-cluster/HDI.PowerShell.BlobCommands.png
