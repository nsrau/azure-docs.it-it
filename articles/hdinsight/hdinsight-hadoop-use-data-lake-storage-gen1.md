---
title: Usare Data Lake Storage Gen1 con Hadoop in Azure HDInsight
description: Informazioni su come eseguire query sui dati da Azure Data Lake Storage Gen1 e archiviare i risultati dell'analisi.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 5949bab7bdf11b11e0ff71f9054098ed83d95ab4
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539837"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Usare Data Lake Storage Gen1 con i cluster Azure HDInsight

> [!Note]
> Distribuire i nuovi cluster HDInsight usando [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) per ottenere prestazioni migliori e nuove funzionalità.

Per analizzare i dati nel cluster HDInsight, è possibile archiviare i dati in [`Azure Blob storage`](../storage/common/storage-introduction.md) , [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)o [Azure Data Lake storage Gen2](../storage/blobs/data-lake-storage-introduction.md). Tutte le opzioni di archiviazione consentono l'eliminazione sicura dei cluster HDInsight utilizzati per i calcoli, senza perdita di dati utente.

Questo articolo illustra come funziona Data Lake Storage Gen1 con i cluster HDInsight. Per informazioni sul funzionamento dell'archiviazione BLOB di Azure con i cluster HDInsight, vedere [usare l'archivio BLOB di Azure con i cluster HDInsight](hdinsight-hadoop-use-blob-storage.md)di Azure. Per altre informazioni sulla creazione di un cluster HDInsight, vedere [Configurare i cluster di HDInsight con Hadoop, Spark, Kafka e altro ancora](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> L'accesso a Data Lake Storage Gen1 avviene sempre tramite un canale sicuro e quindi non è presente un nome di schema del file system `adls`. Viene usato sempre `adl`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>Disponibilità per i cluster HDInsight

Apache Hadoop supporta una nozione del file system predefinito. Il file system predefinito implica uno schema e un'autorità predefiniti e può essere usato anche per risolvere percorsi relativi. Durante il processo di creazione del cluster HDInsight, specificare un contenitore BLOB in archiviazione di Azure come predefinito file system. Oppure con HDInsight 3,5 e versioni successive, è possibile selezionare archiviazione BLOB di Azure o Azure Data Lake Storage Gen1 come file System predefinito con alcune eccezioni. L'account di archiviazione deve trovarsi nella stessa area del cluster.

I cluster HDInsight possono usare Data Lake Storage Gen1 in due modi:

* Come risorsa di archiviazione predefinita
* Come risorsa di archiviazione aggiuntiva, con archiviazione BLOB di Azure come risorsa di archiviazione predefinita.

Attualmente, solo alcuni tipi/versioni del cluster HDInsight supportano l'uso di Data Lake Storage Gen1 come archiviazione predefinita e account di archiviazione aggiuntivi:

| Tipo di cluster HDInsight | Data Lake Storage Gen1 come risorsa di archiviazione predefinita | Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva| Note |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight versione 4,0 | No | No |ADLS Gen1 non è supportato con HDInsight 4,0 |
| HDInsight versione 3.6 | Sì | Sì | Ad eccezione di HBase|
| HDInsight versione 3.5 | Sì | Sì | Ad eccezione di HBase|
| HDInsight versione 3.4 | No | Sì | |
| HDInsight versione 3.3 | No | No | |
| HDInsight versione 3.2 | No | Sì | |
| Storm | | |È possibile usare Data Lake Storage Gen1 per scrivere dati da una topologia Storm. È anche possibile usare Data Lake Storage Gen1 per i dati di riferimento che possono essere letti da una topologia Storm.|

> [!WARNING]  
> HDInsight HBase non è supportato con Azure Data Lake Storage Gen1

L'uso di Data Lake Storage Gen1 come account di archiviazione aggiuntivo non influisce sulle prestazioni. O la possibilità di leggere o scrivere nell'archiviazione BLOB di Azure dal cluster.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Usare Data Lake Storage Gen1 come risorsa di archiviazione predefinita

Quando si distribuisce HDInsight con Data Lake Storage Gen1 come risorsa di archiviazione predefinita, i file correlati al cluster vengono archiviati in `adl://mydatalakestore/<cluster_root_path>/`, dove `<cluster_root_path>` è il nome di una cartella creata in Data Lake Storage. Specificando un percorso radice per ogni cluster, è possibile usare lo stesso account Data Lake Storage per più di un cluster. Pertanto, è possibile disporre di una configurazione in cui:

* Cluster1 può usare il percorso `adl://mydatalakestore/cluster1storage`
* Cluster2 può usare il percorso `adl://mydatalakestore/cluster2storage`

Si noti che entrambi i cluster usano lo stesso account di Data Lake Storage Gen1, **mydatalakestore** . Ogni cluster ha accesso al proprio file system radice in Data Lake Storage. L'esperienza di distribuzione di portale di Azure richiede di usare un nome di cartella, ad esempio **/Clusters/ \<clustername>** , per il percorso radice.

Per usare Data Lake Storage Gen1 come risorsa di archiviazione predefinita, è necessario concedere all'entità servizio l'accesso ai percorsi seguenti:

* Radice dell'account Data Lake Storage Gen1.  ad esempio adl://mydatalakestore/.
* Cartella per tutte le cartelle del cluster,  ad esempio adl://mydatalakestore/clusters.
* Cartella per il cluster,  ad esempio adl://mydatalakestore/clusters/cluster1storage.

Per altre informazioni su come creare un'entità servizio e concedere l'accesso, consultare la sezione Configurare l'accesso a Data Lake Storage.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Estrazione di un certificato da Azure Key Vault per l'uso nella creazione di cluster

Se il certificato per l'entità servizio viene archiviato in Azure Key Vault, è necessario convertire il certificato nel formato corretto. Nei frammenti di codice seguenti viene illustrato come eseguire la conversione.

Prima di tutto, scaricare il certificato da Key Vault ed estrarre il `SecretValueText`.

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Convertire quindi il `SecretValueText` in un certificato.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

È quindi possibile usare il `$identityCertificate` per distribuire un nuovo cluster, come illustrato nel frammento di codice seguente:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $pathToArmTemplate `
    -identityCertificate $identityCertificate `
    -identityCertificatePassword $certPassword.SecretValueText `
    -clusterName  $clusterName `
    -clusterLoginPassword $SSHpassword `
    -sshPassword $SSHpassword `
    -servicePrincipalApplicationId $application.ApplicationId
```

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Usare Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva

È possibile usare Data Lake Storage Gen1 anche come risorsa di archiviazione aggiuntiva per il cluster. In questi casi, l'archiviazione predefinita del cluster può essere un archivio BLOB di Azure o un account Azure Data Lake Storage Gen1. Quando si eseguono processi HDInsight sui dati archiviati in Azure Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva, usare il percorso completo. Ad esempio:

`adl://mydatalakestore.azuredatalakestore.net/<file_path>`

Non è presente alcun **cluster_root_path** nell'URL. Questo perché Data Lake Storage non è un archivio predefinito in questo caso. Quindi, è sufficiente specificare il percorso dei file.

Per usare un Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva, concedere all'entità servizio l'accesso ai percorsi in cui sono archiviati i file.  Ad esempio:

`adl://mydatalakestore.azuredatalakestore.net/<file_path>`

Per altre informazioni su come creare un'entità servizio e concedere l'accesso, consultare la sezione Configurare l'accesso a Data Lake Storage.

## <a name="use-more-than-one-data-lake-storage-gen1-account"></a>Usare più di un account Data Lake Storage Gen1

L'aggiunta di un account Data Lake Storage come aggiuntivo e l'aggiunta di più di un account di Data Lake Storage possono essere eseguite. Concedere l'autorizzazione del cluster HDInsight per i dati in uno o più account Data Lake Storage. Vedere Configurare l'accesso Data Lake Storage Gen1 di seguito.

## <a name="configure-data-lake-storage-gen1-access"></a>Configurare l'accesso a Data Lake Storage Gen1

Per configurare l'accesso Azure Data Lake Storage Gen1 dal cluster HDInsight, è necessario disporre di un'entità servizio di Azure Active Directory (Azure AD). Solo un amministratore di Azure AD può creare un'entità servizio. L'entità servizio deve essere creata con un certificato. Per altre informazioni, vedi [Avvio rapido: impostazione dei cluster in HDInsight](./hdinsight-hadoop-provision-linux-clusters.md) e [Creare un'entità servizio con certificato autofirmato](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> Se si prevede di usare Azure Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva per il cluster HDInsight, è consigliabile aggiungere questa risorsa durante la creazione del cluster, come descritto in questo articolo. L'aggiunta di Azure Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva a un cluster HDInsight esistente non è supportata.

Per altre informazioni sul modello di controllo di accesso, vedere [controllo di accesso in Azure Data Lake storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="access-files-from-the-cluster"></a>Accedere ai file dal cluster

Esistono diversi modi per accedere ai file in Data Lake Storage da un cluster HDInsight.

* **Uso di nomi completi** . Con questo approccio viene fornito il percorso completo al file a cui si desidera accedere.

    ```
    adl://<data_lake_account>.azuredatalakestore.net/<cluster_root_path>/<file_path>
    ```

* **Uso del formato con percorso abbreviato** . Con questo approccio si sostituisce il percorso fino alla radice del cluster con:

    ```
    adl:///<file path>
    ```

* **Uso del percorso relativo** . Con questo approccio viene fornito unicamente il percorso relativo al file a cui si desidera accedere.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Esempi di accesso ai dati

Gli esempi sono basati su una [connessione SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) al nodo head del cluster. Negli esempi vengono usati tutti e tre gli schemi URI. Sostituire `DATALAKEACCOUNT` e `CLUSTERNAME` con i valori pertinenti.

#### <a name="a-few-hdfs-commands"></a>Alcuni comandi HDFS

1. Creare un file nell'archiviazione locale.

    ```bash
    touch testFile.txt
    ```

1. Creare directory nell'archiviazione del cluster.

    ```bash
    hdfs dfs -mkdir adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -mkdir adl:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Copiare i dati dall'archiviazione locale all'archiviazione del cluster.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt adl:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt /sampledata3/
    ```

1. Elencare il contenuto della directory nell'archiviazione del cluster.

    ```bash
    hdfs dfs -ls adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -ls adl:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Creazione di una tabella Hive

Per scopi illustrativi, vengono visualizzati tre percorsi di file. Per l'esecuzione effettiva, usare solo una delle voci `LOCATION`.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/example/data/';
LOCATION 'adl:///example/data/';
LOCATION '/example/data/';
```

## <a name="identify-storage-path-from-ambari"></a>Identificare il percorso di archiviazione da Ambari

Per identificare il percorso completo dell'archivio predefinito configurato, passare a **HDFS**  >  **configs** e immettere `fs.defaultFS` nella casella Filter input.

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Creare cluster HDInsight con accesso a Data Lake Storage Gen1

Usare i collegamenti seguenti per informazioni dettagliate su come creare cluster HDInsight con accesso a Data Lake Storage Gen1.

* [Uso del portale](./hdinsight-hadoop-provision-linux-clusters.md)
* [Uso di PowerShell con Data Lake Storage Gen1 come risorsa di archiviazione predefinita](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Uso di PowerShell con Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Uso di modelli di Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Aggiornare il certificato di HDInsight per l'accesso a Data Lake Storage Gen1

Il codice di PowerShell di esempio seguente legge un certificato da un file locale o da Azure Key Vault e aggiorna il cluster HDInsight con il nuovo certificato per accedere ad Azure Data Lake Storage Gen1. Specificare il nome del cluster HDInsight, il nome del gruppo di risorse, l'ID sottoscrizione, `app ID` il percorso locale del certificato. Scrivere la password quando richiesto.

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$KeyVaultName = "my-key-vault-name"
$KeyVaultSecretName = "my-key-vault-secret-name"
$certPassword = Read-Host "Enter Certificate Password"
# certSource
# 0 - create self signed cert
# 1 - read cert from file path
# 2 - read cert from key vault
$certSource = 0

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

if($certSource -eq 0)
{
    Write-Host "Generating new SelfSigned certificate"

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
elseif($certSource -eq 1)
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}
elseif($certSource -eq 2)
{

    Write-Host "Reading the cert file from Azure Key Vault $KeyVaultName"

    $cert = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName)
    $certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
    $certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue, $null,"Exportable, PersistKeySet"

    $certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);

    $certString =[System.Convert]::ToBase64String($certBytes)
}

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 7 minutes for the permissions to get propagated"
    Start-Sleep -s 420 #7 minutes
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come usare Azure Data Lake Storage Gen1, compatibile con Hadoop Distributed File System (HDFS), con HDInsight. Questa risorsa di archiviazione consente di creare soluzioni di acquisizione dei dati di archiviazione a lungo termine e adattabili. Usare HDInsight per sbloccare le informazioni all'interno dei dati strutturati e non strutturati archiviati.

Per altre informazioni, vedere:

* [Guida introduttiva: impostazione dei cluster in HDInsight](./hdinsight-hadoop-provision-linux-clusters.md)
* [Creare un cluster HDInsight per l'uso di Data Lake Storage Gen1 con Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Caricare dati in HDInsight](hdinsight-upload-data.md)
* [Usare le firme di accesso condiviso di archiviazione BLOB di Azure per limitare l'accesso ai dati con HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)