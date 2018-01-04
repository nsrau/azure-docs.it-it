---
title: 'PowerShell: Cluster HDInsight di Azure con Data Lake Store come risorsa di archiviazione aggiuntiva | Documentazione Microsoft'
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 164ada5a-222e-4be2-bd32-e51dbe993bc0
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/01/2017
ms.author: nitinme
ms.openlocfilehash: dc46e3987f3710bc73ef13a2c195b68f78bd104d
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-data-lake-store-as-additional-storage"></a>Usare Azure PowerShell per creare un cluster HDInsight con Data Lake Store (come risorsa di archiviazione aggiuntiva)
> [!div class="op_single_selector"]
> * [Uso del portale](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Uso di PowerShell (per l'archiviazione predefinita)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Uso di PowerShell (per l'archiviazione aggiuntiva)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Utilizzo di Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Informazioni su come usare Azure PowerShell per configurare un cluster HDInsight con Azure Data Lake Store **come risorsa di archiviazione aggiuntiva**. Per istruzioni su come creare un cluster HDInsight con Azure Data Lake Store come risorsa di archiviazione predefinita, vedere [Create an HDInsight cluster with Data Lake Store as default storage](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md) (Creare un cluster HDInsight con Data Lake Store come risorsa di archiviazione predefinita).

> [!NOTE]
> Se si prevede di usare Azure Data Lake Store come risorsa di archiviazione aggiuntiva per i cluster HDInsight, è vivamente consigliabile farlo durante la creazione del cluster, come descritto in questo articolo. L'aggiunta di Azure Data Lake Store come risorsa di archiviazione aggiuntiva a un cluster HDInsight esistente è un processo complesso e soggetto a errori.
>

Per i tipi di cluster supportati, Data Lake Store può essere usato come risorsa di archiviazione predefinita o come account di archiviazione aggiuntivo. Quando Data Lake Store viene usato come risorsa di archiviazione aggiuntiva, l'account di archiviazione predefinito per i cluster saranno i BLOB del servizio di archiviazione di Azure (WASB) e i file correlati ai cluster (ad esempio log e così via) vengono scritti nella risorsa di archiviazione predefinita, mentre i dati da elaborare possono essere archiviati in un account di Data Lake Store. L'uso di Archivio Data Lake come account di archiviazione aggiuntivo non ha impatto sulle prestazioni o sulla possibilità di leggere/scrivere nella risorsa di archiviazione dal cluster.

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>Udo di Data Lake Store per l'archiviazione di cluster HDInsight

Di seguito sono riportate alcune considerazioni importanti per l'uso di HDInsight con Data Lake Store:

* L'opzione per creare cluster HDInsight con accesso a Data Lake Store come risorsa di archiviazione aggiuntiva è disponibile per le versioni 3.2, 3.4, 3.5 e 3.6 di HDInsight.

La configurazione di HDInsight perché funzioni con Archivio Data Lake tramite PowerShell prevede i passaggi seguenti:

* Creare un Archivio Azure Data Lake
* Configurare l'autenticazione per l'accesso basato sui ruoli all'Archivio Data Lake
* Creare un cluster HDInsight con l'autenticazione per Archivio Data Lake
* Eseguire un processo di test sul cluster

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 o versioni successive**. Vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).
* **Windows SDK**. Per installarlo, fare clic [qui](https://dev.windows.com/en-us/downloads). Usarlo per creare un certificato di sicurezza.
* **Entità servizio di Azure Active Directory**. Questa esercitazione fornisce tutte le istruzioni utili su come creare un'entità servizio in Azure AD. Tuttavia, è necessario essere un amministratore di Azure AD per creare un'entità servizio. Se si è un amministratore di Azure AD, è possibile ignorare questo prerequisito e procedere con l'esercitazione.

    **Se non si è un amministratore di Azure AD**, non sarà possibile eseguire i passaggi necessari per creare un'entità servizio. In tal caso, l'amministratore di Azure AD deve creare un'entità servizio prima di creare un cluster HDInsight con l'archivio Data Lake Store. Inoltre, l'entità servizio deve essere creata usando un certificato, come descritto in [Creare un'entità servizio con certificato](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-azure-data-lake-store"></a>Creare un Archivio Azure Data Lake
Per creare un Archivio Data Lake, seguire questa procedura.

1. Sul desktop aprire una nuova finestra di Azure PowerShell e immettere il frammento di codice seguente. Quando viene richiesto di effettuare l'accesso, assicurarsi di accedere come amministratore/proprietario della sottoscrizione:

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > Se si riceve un errore simile a `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` quando si registra il provider di risorse Data Lake Store, è possibile che la sottoscrizione non sia abilitata per Azure Data Lake Store. Assicurarsi di abilitare la sottoscrizione di Azure per l'anteprima pubblica di Archivio Data Lake seguendo queste [istruzioni](data-lake-store-get-started-portal.md).
   >
   >
2. Un account di Archivio Azure Data Lake è associato a un gruppo di risorse di Azure. Per iniziare, creare un gruppo di risorse di Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    Verrà visualizzato un output simile al seguente:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Creare un account Archivio Azure Data Lake. Il nome dell'account specificato deve contenere solo lettere minuscole e numeri.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    Verrà visualizzato un output simile al seguente:

        ...
        ProvisioningState           : Succeeded
        State                       : Active
        CreationTime                : 5/5/2017 10:53:56 PM
        EncryptionState             : Enabled
        ...
        LastModifiedTime            : 5/5/2017 10:53:56 PM
        Endpoint                    : hdiadlstore.azuredatalakestore.net
        DefaultGroup                :
        Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
        Name                        : hdiadlstore
        Type                        : Microsoft.DataLakeStore/accounts
        Location                    : East US 2
        Tags                        : {}

5. Caricare alcuni dati di esempio in Azure Data Lake. Questi dati saranno usati più avanti in questo articolo per verificare che siano accessibili da un cluster HDInsight. Se si stanno cercando dati di esempio da caricare, è possibile ottenere la cartella **Ambulance Data** dal [Repository GitHub per Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

        $myrootdir = "/"
        Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Configurare l'autenticazione per l'accesso basato sui ruoli all'Archivio Data Lake
Ogni sottoscrizione di Azure è associata a una Azure Active Directory. Gli utenti e i servizi che accedono alle risorse della sottoscrizione tramite il portale di Azure o l'API di Azure Resource Manager devono prima autenticarsi con Azure Active Directory. L'accesso viene concesso alle sottoscrizioni e ai servizi di Azure tramite l'assegnazione del ruolo appropriato in una risorsa di Azure.  Per i servizi, un'entità servizio identifica il servizio in Azure Active Directory (AAD). Questa sezione illustra come concedere a un servizio dell'applicazione, ad esempio HDInsight, l'accesso a una risorsa di Azure, ovvero l'account Archivio Azure Data Lake creato in precedenza, mediante la creazione di un'entità servizio per l'applicazione e l'assegnazione di ruoli a tale entità con Azure PowerShell.

Per configurare l'autenticazione di Active Directory per Azure Data Lake, è necessario eseguire queste attività.

* Creare un certificato autofirmato
* Creare un'applicazione in Azure Active Directory e un'entità servizio

### <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato
Assicurarsi di avere installato [Windows SDK](https://dev.windows.com/en-us/downloads) prima di continuare con i passaggi descritti in questa sezione. È necessario aver creato anche una directory, ad esempio **C:\mycertdir**, in cui sarà creato il certificato.

1. Dalla finestra di PowerShell passare al percorso in cui è installato Windows SDK, in genere `C:\Program Files (x86)\Windows Kits\10\bin\x86`, e usare l'utilità [MakeCert][makecert] per creare un certificato autofirmato e una chiave privata. Usare i comandi seguenti.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Verrà richiesto di immettere la password della chiave privata. Una volta completata l'esecuzione del comando, nella directory del certificato specificata verranno visualizzati **CertFile.cer** e **mykey.pvk**.
2. Usare l'utilità [Pvk2Pfx][pvk2pfx] per convertire i file con estensione PVK e CER creati da MakeCert in un file con estensione PFX. Eseguire il comando indicato di seguito.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Quando richiesto immettere la password della chiave privata specificata in precedenza. Il valore specificato per il parametro **-po** è la password associata al file PFX. Dopo il completamento del comando, nella directory del certificato specificata dovrebbe essere visualizzato un file denominato CertFile.pfx.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Creare un'applicazione Azure Active Directory e un'entità servizio
In questa sezione seguire la procedura per creare un'entità servizio per un'applicazione Azure Active Directory, assegnare un ruolo all'entità servizio e autenticarsi come entità servizio fornendo un certificato. Eseguire i comandi seguenti per creare un'applicazione in Azure Active Directory.

1. Incollare i cmdlet seguenti nella finestra della console di PowerShell. Assicurarsi che il valore specificato per la proprietà **-DisplayName** sia univoco. I valori per **-HomePage** e **-IdentiferUris** sono valori segnaposto e non vengono verificati.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
            -DisplayName "HDIADL" `
            -HomePage "https://contoso.com" `
            -IdentifierUris "https://mycontoso.com" `
            -CertValue $credential  `
            -StartDate $certificatePFX.NotBefore  `
            -EndDate $certificatePFX.NotAfter

        $applicationId = $application.ApplicationId
2. Creare un'entità servizio usando l'ID applicazione.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Concedere l'accesso dell'entità servizio al file e alla cartella Data Lake Store a cui si vuole accedere dal cluster HDInsight. Il frammento seguente consente di accedere alla root dell'account Data Lake Store in cui è stato copiato il file di dati di esempio e al file stesso.

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-additional-storage"></a>Creare un cluster HDInsight Linux con Data Lake Store come risorsa di archiviazione aggiuntiva

In questa sezione viene creato un cluster HDInsight di Handoop Linux con Data Lake Store come risorsa di archiviazione aggiuntiva. Per questa versione il cluster HDInsight e Data Lake Store devono trovarsi nella stessa località.

1. Iniziare recuperando l'ID tenant della sottoscrizione, che sarà necessario più avanti.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId
2. In questa versione Archivio Data Lake può essere usato solo come risorsa di archiviazione aggiuntiva per un cluster Hadoop. L'archivio predefinito continuerà a essere BLOB di archiviazione di Azure (WABS). Si procederà quindi prima di tutto alla creazione dell'account di archiviazione e dei contenitori di archiviazione richiesti per il cluster.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name

        New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = (Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $containerName -Context $destContext
3. Creare il cluster HDInsight. Eseguire i cmdlet seguenti.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    Dopo il completamento del cmdlet, viene visualizzato un output simile al seguente.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Eseguire i processi di test sul cluster HDInsight per usare Archivio Data Lake.
Dopo aver configurato un cluster HDInsight, è possibile eseguire processi di test sul cluster per verificare che il cluster HDInsight possa accedere ad Archivio Data Lake. A questo scopo, verrà eseguito un processo Hive di esempio che crea una tabella con i dati di esempio caricati in precedenza in Archivio Data Lake.

In questa sezione si accede tramite SSH al cluster Linux HDInsight e viene eseguita una query Hive di esempio.

* Se si usa un client Windows per accedere tramite SSH al cluster, vedere [Uso di SSH con Hadoop basato su Linux in HDInsight da Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se si usa un client Linux per accedere tramite SSH al cluster, vedere [Uso di SSH con Hadoop basato su Linux in HDInsight da Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Dopo la connessione, avviare l'interfaccia della riga di comando di Hive mediante il comando seguente:

        hive
2. Usando l'interfaccia della riga di comando, immettere le istruzioni seguenti per creare una nuova tabella denominata **vehicles** con i dati di esempio in Archivio Data Lake:

        DROP TABLE vehicles;
        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
        SELECT * FROM vehicles LIMIT 10;

    L'output dovrebbe essere simile al seguente:

        1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
        1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
        1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
        1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
        1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
        1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
        1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
        1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
        1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
        1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1

## <a name="access-data-lake-store-using-hdfs-commands"></a>Accedere ad Archivio Data Lake tramite comandi HDFS
Dopo aver configurato il cluster HDInsight perché funzioni con Archivio Data Lake, è possibile usare i comandi della shell HDFS per accedere all'archivio.

In questa sezione si accede tramite SSH al cluster Linux HDInsight creato e viene eseguito il comando HDFS.

* Se si usa un client Windows per accedere tramite SSH al cluster, vedere [Uso di SSH con Hadoop basato su Linux in HDInsight da Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se si usa un client Linux per accedere tramite SSH al cluster, vedere [Uso di SSH con Hadoop basato su Linux in HDInsight da Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Dopo avere stabilito la connessione, usare il comando del file system HDFS seguente per elencare i file nell'Archivio Data Lake.

    hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Dovrebbe essere elencato anche il file precedentemente caricato in Archivio Data Lake.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

È inoltre possibile usare il comando `hdfs dfs -put` per caricare dei file in Archivio Data Lake e quindi usare `hdfs dfs -ls` per verificare che i file siano stati caricati correttamente.

## <a name="see-also"></a>Vedere anche
* [Usare Data Lake Store con cluster Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portale: Creare un cluster HDInsight per usare Archivio Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
