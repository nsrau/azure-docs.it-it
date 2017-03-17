---
title: 'PowerShell: Cluster HDInsight di Azure con Data Lake Store come risorsa di archiviazione predefinita | Documentazione Microsoft'
description: Utilizzare Azure PowerShell per creare e usare cluster HDInsight con Azure Data Lake
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/02/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 01c75f20909c7334981bf407e775656476972276
ms.lasthandoff: 03/03/2017


---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-data-lake-store-as-default-storage"></a>Usare Azure PowerShell per creare un cluster HDInsight con Data Lake Store (come risorsa di archiviazione predefinita)
> [!div class="op_single_selector"]
> * [Uso del portale](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Uso di PowerShell (per l'archiviazione predefinita)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Uso di PowerShell (per l'archiviazione aggiuntiva)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Utilizzo di Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Informazioni su come usare Azure PowerShell per configurare un cluster HDInsight con Azure Data Lake Store **come risorsa di archiviazione predefinita**. Per istruzioni su come creare un cluster HDInsight con Azure Data Lake Store come risorsa di archiviazione predefinita, vedere [Usare Azure PowerShell per creare un cluster HDInsight con Data Lake Store (come risorsa di archiviazione predefinita)](data-lake-store-hdinsight-hadoop-use-powershell.md).

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>Udo di Data Lake Store per l'archiviazione di cluster HDInsight

Di seguito sono riportate alcune considerazioni importanti per l'uso di HDInsight con Data Lake Store:

* L'opzione per creare cluster HDInsight con accesso a Data Lake Store come risorsa di archiviazione predefinita è disponibile per HDInsight versione 3.5.

* L'opzione per creare cluster HDInsight con accesso a Data Lake Store come risorsa di archiviazione predefinita non è disponibile per i cluster HDInsight Premium.

* Per i cluster HBase (Windows e Linux), Data Lake Store **non è supportato** come opzione di archiviazione, per la risorsa di archiviazione predefinita o aggiuntiva.


La configurazione di HDInsight perché funzioni con Archivio Data Lake tramite PowerShell prevede i passaggi seguenti:

* Creare un Archivio Azure Data Lake
* Configurare l'autenticazione per l'accesso basato sui ruoli all'Archivio Data Lake
* Creare un cluster HDInsight con l'autenticazione per Archivio Data Lake
* Eseguire un processo di test sul cluster

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 o versioni successive**. Vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).
* **Windows SDK**. Per installarlo, fare clic [qui](https://dev.windows.com/en-us/downloads). Usarlo per creare un certificato di sicurezza.
* **Entità servizio di Azure Active Directory**. Questa esercitazione fornisce tutte le istruzioni utili su come creare un'entità servizio in Azure AD. Tuttavia, è necessario essere un amministratore di Azure AD per creare un'entità servizio. Se si è un amministratore di Azure AD, è possibile ignorare questo prerequisito e procedere con l'esercitazione.

    **Se non si è un amministratore di Azure AD**, non sarà possibile eseguire i passaggi necessari per creare un'entità servizio. In tal caso, l'amministratore di Azure AD deve creare un'entità servizio prima di creare un cluster HDInsight con l'archivio Data Lake Store. Inoltre, l'entità servizio deve essere creata usando un certificato, come descritto in [Creare un'entità servizio con certificato](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate).

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
   > Se si riceve un errore simile a `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` quando si registra il provider di risorse Archivio Data Lake, è possibile che la sottoscrizione non sia abilitata per Archivio Data Lake di Azure. Assicurarsi di abilitare la sottoscrizione di Azure per l'anteprima pubblica di Archivio Data Lake seguendo queste [istruzioni](data-lake-store-get-started-portal.md).
   >
   >
2. Un account di Archivio Azure Data Lake è associato a un gruppo di risorse di Azure. Per iniziare, creare un gruppo di risorse di Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Creare un gruppo di risorse di Azure](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "Create an Azure Resource Group")
3. Creare un account Archivio Azure Data Lake. Il nome dell'account specificato deve contenere solo lettere minuscole e numeri.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Creare un account Azure Data Lake](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "Creare un account Azure Data Lake")
4. Verificare che l'account sia stato creato correttamente.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    L'output di questa operazione deve essere **True**.

5. L'uso di Data Lake Store come risorsa di archiviazione predefinita richiede di specificare il percorso radice in cui vengono copiati i file specifici del cluster durante la creazione del cluster. Usare i cmdlet seguenti per creare un percorso radice, che nel frammento di codice riportato di seguito è **/cluster/hdiadlcluster**.

        $myrootdir = "/"
        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Configurare l'autenticazione per l'accesso basato sui ruoli all'Archivio Data Lake
Ogni sottoscrizione di Azure è associata a una Azure Active Directory. Gli utenti e i servizi che accedono alle risorse della sottoscrizione tramite il portale di Azure classico o l'API di Azure Resource Manager devono prima autenticarsi con Azure Active Directory. L'accesso viene concesso alle sottoscrizioni e ai servizi di Azure tramite l'assegnazione del ruolo appropriato in una risorsa di Azure.  Per i servizi, un'entità servizio identifica il servizio in Azure Active Directory (AAD). Questa sezione illustra come concedere a un servizio dell'applicazione, ad esempio HDInsight, l'accesso a una risorsa di Azure, ovvero l'account Archivio Azure Data Lake creato in precedenza, mediante la creazione di un'entità servizio per l'applicazione e l'assegnazione di ruoli a tale entità con Azure PowerShell.

Per configurare l'autenticazione di Active Directory per Azure Data Lake, è necessario eseguire queste attività.

* Creare un certificato autofirmato
* Creare un'applicazione in Azure Active Directory e un'entità servizio

### <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato
Assicurarsi di avere installato [Windows SDK](https://dev.windows.com/en-us/downloads) prima di continuare con i passaggi descritti in questa sezione. È necessario aver creato anche una directory, ad esempio **C:\mycertdir**, in cui sarà creato il certificato.

1. Dalla finestra di PowerShell passare al percorso in cui è installato Windows SDK, in genere `C:\Program Files (x86)\Windows Kits\10\bin\x86`, e usare l'utilità [MakeCert][makecert] per creare un certificato autofirmato e una chiave privata. Usare il comandi seguenti:

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
3. Concedere l'accesso dell'entità servizio alla radice di Data Lake Store e a tutte le cartelle nel percorso radice specificato in precedenza. Usare i cmdlet seguenti.

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-default-storage"></a>Creare un cluster HDInsight di Linux con Data Lake Store come risorsa di archiviazione predefinita

In questa sezione viene creato un cluster HDInsight di Handoop Linux con Data Lake Store come risorsa di archiviazione predefinita. Per questa versione il cluster HDInsight e Data Lake Store devono trovarsi nella stessa posizione.

1. Iniziare recuperando l'ID tenant della sottoscrizione, che sarà necessario più avanti.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId
        
2. Creare il cluster HDInsight. Eseguire i cmdlet seguenti.

        # Set these variables
        
        $location = "East US 2"
        $storageAccountName = $dataLakeStoreName                          # Data Lake Store account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster `
               -ClusterType Hadoop `
               -OSType Linux `
               -ClusterSizeInNodes $clusterNodes `
               -ResourceGroupName $resourceGroupName `
               -ClusterName $clusterName `
               -HttpCredential $httpCredentials `
               -Location $location `
               -DefaultStorageAccountType AzureDataLakeStore `
               -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
               -DefaultStorageRootPath $storageRootPath `
               -Version "3.5" `
               -SshCredential $sshCredentials `
               -AadTenantId $tenantId `
               -ObjectId $objectId `
               -CertificateFilePath $certificateFilePath `
               -CertificatePassword $password

    Dopo il completamento del cmdlet, viene visualizzato un output simile al seguente.

        
## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Eseguire i processi di test sul cluster HDInsight per usare Archivio Data Lake.
Dopo aver configurato un cluster HDInsight, è possibile eseguire processi di test sul cluster per verificare che il cluster HDInsight possa accedere ad Archivio Data Lake. A tale scopo, verrà eseguito un processo Hive di esempio che crea una tabella con i dati di esempio già disponibili nel Data Lake Store al percorso **<cluster root>/example/data/sample.log**.

In questa sezione si accede tramite SSH al cluster Linux HDInsight e viene eseguita una query Hive di esempio.

* Se si usa un client Windows per accedere tramite SSH al cluster, vedere [Uso di SSH con Hadoop basato su Linux in HDInsight da Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se si usa un client Linux per accedere tramite SSH al cluster, vedere [Uso di SSH con Hadoop basato su Linux in HDInsight da Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Dopo la connessione, avviare l'interfaccia della riga di comando di Hive mediante il comando seguente:

        hive
2. Usando l'interfaccia della riga di comando, immettere le istruzioni seguenti per creare una nuova tabella denominata **vehicles** con i dati di esempio in Archivio Data Lake:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    L'output della query dovrebbe essere visualizzato nella console SSH. 

    > [!NOTE]
       > Il percorso ai dati di esempio nel comando CREATE TABLE precedente è `adl:///example/data/`, dove `adl:///` è la radice del cluster. Secondo l'esempio della radice del cluster specificato in questa esercitazione, questo sarà `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`. Pertanto, è possibile usare l'alternativa più breve o fornire il percorso completo alla radice del cluster. 
       >
       >



## <a name="access-data-lake-store-using-hdfs-commands"></a>Accedere ad Archivio Data Lake tramite comandi HDFS
Dopo aver configurato il cluster HDInsight perché funzioni con Archivio Data Lake, è possibile usare i comandi della shell HDFS per accedere all'archivio.

In questa sezione si accede tramite SSH al cluster Linux HDInsight creato e viene eseguito il comando HDFS. 

* Se si usa un client Windows per accedere tramite SSH al cluster, vedere [Uso di SSH con Hadoop basato su Linux in HDInsight da Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se si usa un client Linux per accedere tramite SSH al cluster, vedere [Uso di SSH con Hadoop basato su Linux in HDInsight da Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Dopo avere stabilito la connessione, usare il comando del file system HDFS seguente per elencare i file nell'Archivio Data Lake.

    hdfs dfs -ls adl:///

È inoltre possibile usare il comando `hdfs dfs -put` per caricare dei file in Archivio Data Lake e quindi usare `hdfs dfs -ls` per verificare che i file siano stati caricati correttamente.

## <a name="see-also"></a>Vedere anche
* [Portale: Creare un cluster HDInsight per usare Archivio Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

