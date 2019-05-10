---
title: "Limitare l'accesso usando le firme di accesso condiviso: Azure HDInsight"
description: Informazioni su come usare le firme di accesso condiviso per limitare l'accesso di HDInsight ai dati archiviati nei BLOB di archiviazione di Azure.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: hrasheed
ms.openlocfilehash: 7f7f6fe31afe35d9ccfd6ee33617bd7e4fbe46b7
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409551"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Usare le firme di accesso condiviso di archiviazione di Azure per limitare l'accesso ai dati in HDInsight

HDInsight ha accesso completo ai dati negli account di archiviazione di Azure associati al cluster. È possibile usare le firme di accesso condiviso nel contenitore BLOB per limitare l'accesso ai dati, Le firme di accesso condiviso sono una funzionalità degli account di archiviazione di Azure che consente di limitare l'accesso ai dati. Ad esempio, concedendo l'accesso in sola lettura ai dati.

> [!IMPORTANT]  
> Per una soluzione che usi Apache Ranger, considerare la possibilità di usare HDInsight aggiunto al dominio. Per altre informazioni, vedere il documento [Configurare i cluster HDInsight aggiunti al dominio](./domain-joined/apache-domain-joined-configure.md).

> [!WARNING]  
> HDInsight deve avere accesso completo alla risorsa di archiviazione predefinita per il cluster.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure.

* Un client SSH. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).

* Un oggetto esistente [contenitore di archiviazione](../storage/blobs/storage-quickstart-blobs-portal.md).  

* Se si usa PowerShell, è necessario il [modulo di Az](https://docs.microsoft.com/powershell/azure/overview).

* Se vogliono usare Azure CLI e non è stato ancora installato, vedere [installare CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Se si usa [Python](https://www.python.org/downloads/), versione 2.7 o versioni successiva.

* Se si usa C#, Visual Studio deve essere versione 2013 o versione successiva.

* Il [schema URI](./hdinsight-hadoop-linux-information.md#URI-and-scheme) dell'account di archiviazione. Il risultato sarà `wasb://` per archiviazione di Azure `abfs://` per Azure Data Lake Storage Gen2 o `adl://` per Azure Data Lake archiviazione Gen1. Se il trasferimento protetto è abilitato per l'archiviazione di Azure o Data Lake Storage Gen2, l'URI sarà `wasbs://` oppure `abfss://`rispettivamente vedere anche [trasferimento sicuro](../storage/common/storage-require-secure-transfer.md).

* Un cluster HDInsight esistente per aggiungere una firma di accesso condiviso a. In caso contrario, è possibile usare Azure PowerShell per creare un cluster e aggiungere una firma di accesso condiviso durante la creazione del cluster.

* I file di esempio da [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Il repository contiene gli elementi seguenti:

  * Un progetto di Visual Studio che può creare un contenitore di archiviazione, i criteri archiviati e la firma di accesso condiviso da usare con HDInsight.
  * Uno script di Python che può creare un contenitore di archiviazione, i criteri archiviati e la firma di accesso condiviso da usare con HDInsight.
  * Uno script di PowerShell in grado di creare un cluster HDInsight e configurarlo per l'uso della firma di accesso condiviso. Viene usata una versione aggiornata più avanti.
  * Un file di esempio: `hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>Firme di accesso condiviso

Esistono due tipi di firme di accesso condiviso:

* Ad hoc: l'ora di inizio, la scadenza e le autorizzazioni per la firma di accesso condiviso vengono tutte specificate nell'URI corrispondente.

* Criteri di accesso archiviati: i criteri di accesso archiviati vengono definiti per un contenitore di risorse, ovvero un contenitore BLOB. I criteri di accesso archiviati possono essere usati per gestire i vincoli per una o più firme di accesso condiviso. Quando si associa una firma di accesso condiviso a criteri di accesso archiviati, la firma eredita i vincoli, ovvero ora di inizio, scadenza e autorizzazioni, definiti per i criteri di accesso archiviati.

La differenza tra le due forme è importante un unico scenario chiave, la revoca. Una firma di accesso condiviso è un URL, pertanto chiunque la ottiene può utilizzarla indipendentemente da chi l'ha richiesta per iniziare. Se la firma di accesso condiviso è stata pubblicata e resa pubblica, può essere usata da chiunque in tutto il mondo. Una forma di accesso condiviso rimane valida finché non si verifica una delle quattro condizioni seguenti:

1. Viene raggiunta la scadenza specificata nella firma.

2. Viene raggiunta la scadenza specificata nei criteri di accesso archiviati a cui fa riferimento la firma di accesso condiviso. Gli scenari seguenti portano a raggiungere la scadenza:

    * L'intervallo di tempo è trascorso.
    * Per i criteri di accesso archiviati è stata impostata una scadenza nel passato. Modificando la scadenza è possibile revocare la firma di accesso condiviso.

3. I criteri di accesso archiviati cui viene fatto riferimento nella firma di accesso condiviso vengono eliminati e ciò corrisponde a un altro modo per revocare la firma. Se si ricreano i criteri di accesso archiviati specificando lo stesso nome, tutti i token di firma di accesso condiviso relativi ai criteri precedenti restano validi, a condizione che l'ora di scadenza indicata nella firma di accesso condiviso non sia trascorsa. Se si intende revocare la firma di accesso condiviso, verificare di usare un nome diverso per ricreare i criteri di accesso archiviati con scadenza nel futuro.

4. La chiave dell'account utilizzata per creare la firma di accesso condiviso viene rigenerata. Se si rigenera la chiave, l'autenticazione di tutte le applicazioni che usano la chiave precedente avrà esito negativo. Aggiornare tutti i componenti con la nuova chiave.

> [!IMPORTANT]  
> L'URI di una firma di accesso condiviso è associato alla chiave dell'account usata per creare la firma e ai relativi criteri di accesso archiviati (se presenti). Se non sono specificati criteri di accesso archiviati, l'unico modo per revocare una firma di accesso condiviso consiste nel modificare la chiave dell'account.

È consigliabile usare sempre i criteri di accesso archiviati. Con i criteri archiviati, è possibile revocare le firme o estendere la scadenza in base alle esigenze. I passaggi illustrati in questo documento permettono di usare i criteri di accesso archiviati per generare firme di accesso condiviso.

Per altre informazioni sulle firme di accesso condiviso, vedere [Informazioni sul modello di firma di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="create-a-stored-policy-and-sas"></a>Creare un criterio archiviato e una firma di accesso condiviso

Salvare il token di firma di accesso condiviso che viene generato alla fine di ogni metodo. Il token avrà un aspetto simile al seguente:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>Tramite PowerShell

Sostituire `RESOURCEGROUP`, `STORAGEACCOUNT`, e `STORAGECONTAINER` con i valori appropriati per il contenitore di archiviazione esistente. Passare alla directory `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` o esaminare i `-File` parametro per contenere il percorso assoluto per `Set-AzStorageblobcontent`. Immettere il comando PowerShell seguente:

```PowerShell
$resourceGroupName = "RESOURCEGROUP"
$storageAccountName = "STORAGEACCOUNT"
$containerName = "STORAGECONTAINER"
$policy = "myPolicyPS"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get the access key for the Azure Storage account
$storageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$storageContext = New-AzStorageContext `
                                -StorageAccountName $storageAccountName `
                                -StorageAccountKey $storageAccountKey

# Create a stored access policy for the Azure storage container
New-AzStorageContainerStoredAccessPolicy `
   -Container $containerName `
   -Policy $policy `
   -Permission "rl" `
   -ExpiryTime "12/31/2025 08:00:00" `
   -Context $storageContext

# Get the stored access policy or policies for the Azure storage container
Get-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Context $storageContext

# Generates an SAS token for the Azure storage container
New-AzStorageContainerSASToken `
    -Name $containerName `
    -Policy $policy `
    -Context $storageContext

<# Removes a stored access policy from the Azure storage container
Remove-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Policy $policy `
    -Context $storageContext
#>

# upload a file for a later example
Set-AzStorageblobcontent `
    -File "./sampledata/sample.log" `
    -Container $containerName `
    -Blob "samplePS.log" `
    -Context $storageContext
```

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

L'uso di variabili in questa sezione si basa su un ambiente di Windows. Saranno necessario leggere variazioni di bash o in altri ambienti.

1. Sostituire `STORAGEACCOUNT`, e `STORAGECONTAINER` con i valori appropriati per il contenitore di archiviazione esistente.

    ```azurecli
    # set variables
    set AZURE_STORAGE_ACCOUNT=STORAGEACCOUNT
    set AZURE_STORAGE_CONTAINER=STORAGECONTAINER

    #Login
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription SUBSCRIPTION

    # Retrieve the primary key for the storage account
    az storage account keys list --account-name %AZURE_STORAGE_ACCOUNT% --query "[0].{PrimaryKey:value}" --output table
    ```

2. Impostare la chiave primaria recuperata a una variabile per un uso successivo. Sostituire `PRIMARYKEY` con l'oggetto recuperato valore nel passaggio precedente e quindi immettere il comando seguente:

    ```azurecli
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. Passare alla directory `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` o esaminare i `--file` parametro per contenere il percorso assoluto per `az storage blob upload`. Eseguire i comandi rimanenti:

    ```azurecli
    # Create stored access policy on the containing object
    az storage container policy create --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --expiry 2025-12-31 --permissions rl

    # List stored access policies on a containing object
    az storage container policy list --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Generate a shared access signature for the container
    az storage container generate-sas --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Reversal
    # az storage container policy delete --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # upload a file for a later example
    az storage blob upload --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --name sampleCLI.log --file "./sampledata/sample.log"
    ```

### <a name="using-python"></a>Uso di Python

Aprire il `SASToken.py` file e sostituire `storage_account_name`, `storage_account_key`, e `storage_container_name` con i valori appropriati per il contenitore di archiviazione esistente e quindi eseguire lo script.

Potrebbe essere necessario eseguire `pip install --upgrade azure-storage` se si riceve il messaggio di errore `ImportError: No module named azure.storage`.

### <a name="using-c"></a>Uso di C#

1. Aprire la soluzione in Visual Studio.

2. In Esplora soluzioni fare clic sui **SASExample** del progetto e selezionare **proprietà**.

3. Selezionare **Impostazioni** e aggiungere i valori per le voci seguenti:

   * StorageConnectionString: stringa di connessione per l'account di archiviazione per cui si vogliono creare criteri archiviati e una firma di accesso condiviso. Il formato deve essere `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey`, dove `myaccount` è il nome dell'account di archiviazione e `mykey` è la chiave dell'account di archiviazione.

   * ContainerName: contenitore nell'account di archiviazione a cui si vuole limitare l'accesso.

   * SASPolicyName: nome da usare per i criteri archiviati da creare.

   * FileToUpload: percorso di un file caricato nel contenitore.

4. Eseguire il progetto. Salvare il token dei criteri di firma di accesso condiviso, il nome dell'account di archiviazione e il nome del contenitore. Questi valori vengono usati quando si associa l'account di archiviazione al cluster HDInsight.

## <a name="use-the-sas-with-hdinsight"></a>Usare la firma di accesso condiviso con HDInsight

Quando si crea un cluster HDInsight è necessario specificare un account di archiviazione primario e, facoltativamente, è possibile specificare account di archiviazione aggiuntivi. Entrambi i metodi di aggiunta di risorse di archiviazione richiedono l'accesso completo agli account di archiviazione e ai contenitori usati.

Per usare una firma di accesso condiviso allo scopo di limitare l'accesso a un contenitore, aggiungere una voce personalizzata alla configurazione del **core-site** per il cluster. È possibile aggiungere la voce durante la creazione del cluster usando PowerShell o dopo la creazione del cluster tramite Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Creare un cluster che usa la firma di accesso condiviso

Sostituire `CLUSTERNAME`, `RESOURCEGROUP`, `DEFAULTSTORAGEACCOUNT`, `STORAGECONTAINER`, `STORAGEACCOUNT`, e `TOKEN` con i valori appropriati. Immettere i comandi di PowerShell:

```powershell

$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RESOURCEGROUP'

# Replace with the Azure data center you want to the cluster to live in
$location = 'eastus'

# Replace with the name of the default storage account TO BE CREATED
$defaultStorageAccountName = 'DEFAULTSTORAGEACCOUNT'

# Replace with the name of the SAS container CREATED EARLIER
$SASContainerName = 'STORAGECONTAINER'

# Replace with the name of the SAS storage account CREATED EARLIER
$SASStorageAccountName = 'STORAGEACCOUNT'

# Replace with the SAS token generated earlier
$SASToken = 'TOKEN'

# Default cluster size (# of worker nodes), version, and type
$clusterSizeInNodes = "4"
$clusterVersion = "3.6"
$clusterType = "Hadoop"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Create an Azure Storage account and container
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey


# Create a blob container. This holds the default data store for the cluster.
New-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext 

# Cluster login is used to secure HTTPS services hosted on the cluster
$httpCredential = Get-Credential `
    -Message "Enter Cluster login credentials" `
    -UserName "admin"

# SSH user is used to remotely connect to the cluster using SSH clients
$sshCredential = Get-Credential `
    -Message "Enter SSH user credentials" `
    -UserName "sshuser"

# Create the configuration for the cluster
$config = New-AzHDInsightClusterConfig 

$config = $config | Add-AzHDInsightConfigValues `
    -Spark2Defaults @{} `
    -Core @{"fs.azure.sas.$SASContainerName.$SASStorageAccountName.blob.core.windows.net"=$SASToken}

# Create the HDInsight cluster
New-AzHDInsightCluster `
    -Config $config `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType $clusterType `
    -OSType Linux `
    -Version $clusterVersion `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $clusterName

<# REVERSAL
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

Remove-AzResourceGroup `
    -Name $resourceGroupName
#>
```

> [!IMPORTANT]  
> Quando vengono richiesti un nome e una password per HTTP/S o SSH, è necessario fornire una password che soddisfi i criteri seguenti:
>
> * Deve avere una lunghezza di almeno 10 caratteri.
> * Deve contenere almeno una cifra.
> * Deve contenere almeno un carattere non alfanumerico.
> * Deve contenere almeno una lettera maiuscola o minuscola.

Il completamento dello script richiede in genere circa 15 minuti. Se lo script viene completato senza errori, il cluster è stato creato.

### <a name="use-the-sas-with-an-existing-cluster"></a>Usare la firma di accesso condiviso con un cluster esistente

Se si dispone di un cluster esistente, è possibile aggiungere la firma di accesso condiviso per il **core-site** configurazione usando la procedura seguente:

1. Aprire l'interfaccia utente Web di Ambari per il cluster. L'indirizzo di questa pagina è `https://YOURCLUSTERNAME.azurehdinsight.net`. Quando richiesto, eseguire l'autenticazione al cluster con il nome amministratore (admin) e la password usati durante la creazione del cluster.

2. Nel lato sinistro dell'interfaccia utente Web di Ambari selezionare **HDFS** e quindi selezionare la scheda **Configs** al centro della pagina.

3. Selezionare la scheda **Advanced** e scorrere fino alla sezione **Custom core-site**.

4. Espandere la sezione **Custom core-site**, quindi scorrere fino alla fine e selezionare il collegamento **Add property**. Usare i valori seguenti per i campi **Key** e **Value**:

   * **Chiave**: `fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
   * **Value**: La firma di accesso condiviso restituiti da uno dei metodi eseguiti in precedenza.

     Sostituire `CONTAINERNAME` con il contenitore nome è stato usato con il C# o un'applicazione di firma di accesso condiviso. Sostituire `STORAGEACCOUNTNAME` con il nome di account di archiviazione usato.

5. Fare clic sul pulsate **Add** per salvare la chiave e il valore, quindi fare clic sul pulsante **Save** per salvare le modifiche alla configurazione. Quando richiesto, aggiungere una descrizione della modifica, ad esempio "aggiunta di accesso alle risorse di archiviazione per le firme di accesso condiviso", e quindi fare clic su **Save** (Salva).

    Al termine delle modifiche, fare clic su **OK** .

   > [!IMPORTANT]  
   > Perché le modifiche siano effettive, è necessario riavviare diversi servizi.

6. Nell'interfaccia utente Web di Ambari selezionare **HDFS** dall'elenco a sinistra e quindi selezionare **Restart All Affected** (Reimposta tutti gli interessati) dall'elenco a discesa **Service Actions** (Azioni servizio) a destra. Quando viene chiesto, selezionare __Confirm restart all__ (Conferma riavvio di tutti).

    Ripetere il processo per MapReduce2 e YARN.

7. Dopo il riavvio di questi servizi, selezionarli uno alla volta e disabilitare la modalità di manutenzione dall'elenco a discesa **Service Actions** (Azioni servizio).

## <a name="test-restricted-access"></a>Testare l'accesso limitato

Utilizzare la procedura seguente per verificare che è possibile solo di lettura ed elenco di elementi di firma di accesso condiviso dell'account di archiviazione.

1. Connettersi al cluster. Sostituire `CLUSTERNAME` con il nome del cluster e immettere il comando seguente:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Per elencare il contenuto del contenitore, usare il comando seguente dal prompt:

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Sostituire `SASCONTAINER` con il nome del contenitore creato per l'account di archiviazione della firma di accesso condiviso. Sostituire `SASACCOUNTNAME` con il nome dell'account di archiviazione usato per la firma di accesso condiviso.

    L'elenco include il file caricato quando sono stati creati il contenitore e la firma di accesso condiviso.

3. Usare il comando seguente per verificare che sia possibile leggere il contenuto del file. Sostituire il `SASCONTAINER` e `SASACCOUNTNAME` come nel passaggio precedente. Sostituire `sample.log` con il nome del file visualizzato nel comando precedente:

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    Verrà visualizzato il contenuto del file.

4. Usare il comando seguente per scaricare il file nel file system locale:

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    Il file verrà scaricato in un file locale denominato **testfile.txt**.

5. Usare il comando seguente per caricare il file locale in un nuovo file denominato **testupload.txt** nella risorsa di archiviazione della firma di accesso condiviso:

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Verrà visualizzato un messaggio simile al testo seguente:

        put: java.io.IOException

    Questo errore si verifica perché il percorso di archiviazione è di sola lettura+elenco. Usare il comando seguente per inserire i dati nella risorsa di archiviazione predefinita per il cluster, accessibile in scrittura:

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    Questa volta l'operazione avrà esito positivo.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come aggiungere risorse di archiviazione ad accesso limitato al cluster HDInsight, è possibile conoscere altri modi per usare i dati nel cluster:

* [Usare Apache Hive con HDInsight](hadoop/hdinsight-use-hive.md)
* [Usare Pig con Hadoop in HDInsight](hadoop/hdinsight-use-pig.md)
* [Usare MapReduce con HDInsight](hadoop/hdinsight-use-mapreduce.md)

