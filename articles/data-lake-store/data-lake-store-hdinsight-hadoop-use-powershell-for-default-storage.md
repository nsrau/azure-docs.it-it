---
title: Creare cluster HDInsight con Data Lake Store come risorsa di archiviazione predefinita usando PowerShell | Microsoft Docs
description: Usare Azure PowerShell per creare e usare cluster HDInsight con Azure Data Lake Store
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
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 64bd0a3ec0598fd7f78e93e510f0a6443f3edbd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-hdinsight-clusters-with-data-lake-store-as-default-storage-by-using-powershell"></a>Creare cluster HDInsight con Data Lake Store come risorsa di archiviazione predefinita usando PowerShell
> [!div class="op_single_selector"]
> * [Usare il portale di Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Usare PowerShell (per l'archiviazione predefinita)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Usare PowerShell (per l'archiviazione aggiuntiva)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Usare Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Informazioni su come usare Azure PowerShell per configurare cluster Azure HDInsight con Azure Data Lake Store come risorsa di archiviazione predefinita. Per istruzioni su come creare un cluster HDInsight con Data Lake Store come risorsa di archiviazione aggiuntiva, vedere [Usare Azure PowerShell per creare un cluster HDInsight con Data Lake Store (come risorsa di archiviazione predefinita)](data-lake-store-hdinsight-hadoop-use-powershell.md).

Di seguito sono riportate alcune considerazioni importanti per l'uso di HDInsight con Data Lake Store:

* L'opzione per creare cluster HDInsight con accesso a Data Lake Store come risorsa di archiviazione predefinita è disponibile per le versioni 3.5 e 3.6 di HDInsight.

* L'opzione per creare cluster HDInsight con accesso a Data Lake Store come risorsa di archiviazione predefinita *non è disponibile* per i cluster HDInsight Premium.

Per configurare HDInsight per il funzionamento con Data Lake Store usando PowerShell, seguire le istruzioni nelle cinque sezioni seguenti.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare l'esercitazione, verificare di soddisfare i requisiti seguenti:

* **Sottoscrizione di Azure**: passare a [Crea subito il tuo account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 o versione successiva**: vedere l'articolo su [come installare e configurare PowerShell](/powershell/azure/overview).
* **Windows Software Development Kit (SDK)**: per installare Windows SDK, passare a [Download e strumenti per Windows 10](https://dev.windows.com/en-us/downloads). L'SDK viene usato per creare un certificato di sicurezza.
* **Entità servizio Azure Active Directory**: questa esercitazione descrive come creare un'entità servizio in Azure Active Directory (Azure AD). Tuttavia, per creare un'entità servizio è necessario essere un amministratore di Azure AD. Se si è un amministratore, è possibile ignorare questo prerequisito e procedere con l'esercitazione.

    >[!NOTE]
    >È possibile creare un'entità servizio solo se si è un amministratore di Azure AD. Prima di poter creare un cluster HDInsight con Data Lake Store, un amministratore di Azure AD deve creare un'entità servizio. L'entità servizio deve essere creata con un certificato, come descritto in [Creare un'entità servizio con certificato](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-a-data-lake-store-account"></a>Creare un account Archivio Data Lake
Per creare un account Data Lake Store, procedere come segue:

1. Sul desktop aprire una finestra di PowerShell e quindi immettere i frammenti di codice seguenti. Quando viene richiesto di effettuare l'accesso, accedere come amministratore o proprietario della sottoscrizione. 

        # Sign in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > Se si registra il provider di risorse Data Lake Store e viene visualizzato un errore simile a `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`, la sottoscrizione potrebbe non essere inclusa nell'elenco elementi consentiti per Data Lake Store. Per abilitare la sottoscrizione di Azure per l'anteprima pubblica di Data Lake Store, seguire le istruzioni in [Introduzione all'uso di Azure Data Lake Store tramite il portale di Azure](data-lake-store-get-started-portal.md).
    >

2. Un account Data Lake Store è associato a un gruppo di risorse di Azure. Iniziare creando un gruppo di risorse.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    Verrà visualizzato un output simile al seguente:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Creare un account Data Lake Store. Il nome dell'account specificato deve contenere solo lettere minuscole e numeri.

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

4. Per usare Data Lake Store come risorsa di archiviazione predefinita, è necessario specificare un percorso radice in cui vengono copiati i file specifici del cluster durante la creazione del cluster. Per creare un percorso radice, che nel frammento di codice è **/cluster/hdiadlcluster**, usare i cmdlet seguenti:

        $myrootdir = "/"
        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Configurare l'autenticazione per l'accesso basato sui ruoli all'Archivio Data Lake
Ogni sottoscrizione di Azure è associata a un'entità di Azure AD. Gli utenti e i servizi che accedono alle risorse della sottoscrizione usando il portale di Azure o l'API di Azure Resource Manager devono prima autenticarsi con Azure AD. L'accesso viene concesso alle sottoscrizioni e ai servizi di Azure tramite l'assegnazione del ruolo appropriato in una risorsa di Azure. Per i servizi, un'entità servizio identifica il servizio in Azure AD.

Questa sezione illustra come concedere a un servizio dell'applicazione, ad esempio HDInsight, l'accesso a una risorsa di Azure, ovvero l'account Data Lake Store creato in precedenza. A tale scopo, è necessario creare un'entità servizio per l'applicazione e assegnare ruoli a tale entità tramite PowerShell.

Per configurare l'autenticazione di Active Directory per Azure Data Lake, eseguire le attività nelle due sezioni seguenti.

### <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato
Assicurarsi di avere installato [Windows SDK](https://dev.windows.com/en-us/downloads) prima di continuare con i passaggi descritti in questa sezione. È necessario aver creato anche una directory, ad esempio *C:\mycertdir*, in cui creare il certificato.

1. Dalla finestra di PowerShell passare al percorso in cui è installato Windows SDK, in genere *C:\Programmi (x86)\Windows Kits\10\bin\x86*, e usare l'utilità [MakeCert][makecert] per creare un certificato autofirmato e una chiave privata. Usare il seguente comando:

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Verrà richiesto di immettere la password della chiave privata. Una volta completata l'esecuzione del comando, nella directory del certificato specificata saranno presenti **CertFile.cer** e **mykey.pvk**.
2. Usare l'utilità [Pvk2Pfx][pvk2pfx] per convertire i file con estensione PVK e CER creati da MakeCert in un file con estensione PFX. Eseguire il comando seguente:

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Quando viene chiesto, immettere la password della chiave privata specificata in precedenza. Il valore specificato per il parametro **-po** è la password associata al file PFX. Dopo il completamento del comando, nella directory del certificato specificata dovrebbe essere presente anche un file denominato **CertFile.pfx**.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Creare un'applicazione Azure AD e un'entità servizio
In questa sezione si crea un'entità servizio per un'applicazione Azure AD, si assegna un ruolo all'entità servizio e si esegue l'autenticazione come entità servizio fornendo un certificato. Per creare un'applicazione in Azure AD, eseguire i comandi seguenti:

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
3. Concedere l'accesso dell'entità servizio alla radice di Data Lake Store e a tutte le cartelle nel percorso radice specificato in precedenza. Usare i cmdlet seguenti:

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-the-default-storage"></a>Creare un cluster HDInsight Linux con Data Lake Store come risorsa di archiviazione predefinita

In questa sezione viene creato un cluster HDInsight Hadoop Linux con Data Lake Store come risorsa di archiviazione predefinita. Per questa versione, il cluster HDInsight e Data Lake Store devono trovarsi nella stessa posizione.

1. Recuperare l'ID tenant della sottoscrizione e archiviarlo per usarlo in seguito.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId

2. Creare il cluster HDInsight usando i cmdlet seguenti:

        # Set these variables

        $location = "East US 2"
        $storageAccountName = $dataLakeStoreName                       # Data Lake Store account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
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
               -Version "3.6" `
               -SshCredential $sshCredentials `
               -AadTenantId $tenantId `
               -ObjectId $objectId `
               -CertificateFilePath $certificateFilePath `
               -CertificatePassword $password

    Dopo il completamento del cmdlet, dovrebbe viene visualizzato un output con i dettagli del cluster.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-store"></a>Eseguire i processi di test sul cluster HDInsight per usare Data Lake Store
Dopo aver configurato un cluster HDInsight, è possibile eseguire i processi di test sul cluster per verificare che possa accedere a Data Lake Store. A tale scopo, eseguire un processo Hive di esempio che crea una tabella che usa i dati di esempio già disponibili in Data Lake Store in *<cluster root>/example/data/sample.log*.

In questa sezione si stabilisce una connessione SSH (Secure Shell) al cluster HDInsight Linux creato e quindi si esegue una query Hive di esempio.

* Se si usa un client Windows per stabilire una connessione SSH al cluster, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se si usa un client Linux per stabilire una connessione SSH al cluster, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Dopo aver stabilito la connessione, avviare l'interfaccia della riga di comando di Hive usando il comando seguente:

        hive
2. Usare l'interfaccia della riga di comando per immettere le istruzioni seguenti per creare una nuova tabella denominata **vehicles** con i dati di esempio in Data Lake Store:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    L'output della query dovrebbe essere visualizzato nella console SSH.

    >[!NOTE]
    >Il percorso ai dati di esempio nel comando CREATE TABLE precedente è `adl:///example/data/`, dove `adl:///` è la radice del cluster. In base all'esempio della radice del cluster specificato in questa esercitazione, il comando è `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`. È possibile usare l'alternativa più breve o indicare il percorso completo della radice del cluster.
    >

## <a name="access-data-lake-store-by-using-hdfs-commands"></a>Accedere a Data Lake Store tramite comandi HDFS
Dopo aver configurato il cluster HDInsight per usare Data Lake Store, è possibile usare i comandi della shell HDFS (Hadoop Distributed File System) per accedere all'archivio.

In questa sezione si stabilisce una connessione SSH al cluster HDInsight Linux creato e quindi si eseguono i comandi HDFS.

* Se si usa un client Windows per stabilire una connessione SSH al cluster, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se si usa un client Linux per stabilire una connessione SSH al cluster, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Dopo avere stabilito la connessione, elencare i file in Data Lake Store usando il comando del file system HDFS seguente.

    hdfs dfs -ls adl:///

È anche possibile usare il comando `hdfs dfs -put` per caricare alcuni file in Data Lake Store e quindi usare `hdfs dfs -ls` per verificare se i file sono stati caricati correttamente.

## <a name="see-also"></a>Vedere anche
* [Portale di Azure: Creare un cluster HDInsight per usare Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
