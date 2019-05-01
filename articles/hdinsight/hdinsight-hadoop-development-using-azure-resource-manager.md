---
title: Eseguire la migrazione agli strumenti di Azure Resource Manager per HDInsight
description: Come eseguire la migrazione a strumenti di sviluppo di Azure Resource Manager per i cluster HDInsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: a3bbee9324b963bac1bb01271051011fdc9bc00b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64723524"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrazione a strumenti di sviluppo basati su Azure Resource Manager per i cluster HDInsight

In HDInsight stanno per essere deprecati gli strumenti basati su Azure Service Management (ASM) per HDInsight. Se si usa Azure PowerShell, l'interfaccia della riga di comando classica di Azure o HDInsight .NET SDK per usare i cluster HDInsight, per il futuro è consigliabile usare le versioni di PowerShell, dell'interfaccia della riga di comando e di .NET SDK basate su Azure Resource Manager. Questo articolo contiene informazioni utili su come eseguire la migrazione al nuovo approccio basato su Gestione risorse. Se applicabili, il documento evidenzia le differenze tra gli approcci basati su ASM e Gestione risorse per HDInsight.

> [!IMPORTANT]  
> Il supporto per ASM basato su PowerShell, l'interfaccia della riga di comando e .NET SDK verrà sospeso il **1° gennaio 2017**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migrating-azure-classic-cli-to-azure-resource-manager"></a>Migrazione dell'interfaccia della riga di comando classica di Azure ad Azure Resource Manager

> [!IMPORTANT]  
> L'interfaccia della riga di comando di Azure non offre supporto per l'uso dei cluster di HDInsight. Anche se deprecata, è comunque possibile usare l'interfaccia della riga di comando classica di Azure con HDInsight.

Quelli che seguono sono i comandi di base per usare HDInsight tramite l'interfaccia della riga di comando classica di Azure:

* `azure hdinsight cluster create` : crea un nuovo cluster HDInsight.
* `azure hdinsight cluster delete` : elimina un cluster HDInsight esistente.
* `azure hdinsight cluster show` : visualizza informazioni su un cluster esistente.
* `azure hdinsight cluster list` : elenca i cluster HDInsight per la sottoscrizione di Azure

Usare l'opzione `-h` per esaminare i parametri e le opzioni disponibili per ogni comando.

### <a name="new-commands"></a>Nuovi comandi
Ecco i nuovi comandi disponibili con Azure Resource Manager:

* `azure hdinsight cluster resize` : modifica dinamicamente il numero di nodi di lavoro del cluster.
* `azure hdinsight cluster enable-http-access` : abilita l'accesso HTTPs al cluster (attivato per impostazione predefinita).
* `azure hdinsight cluster disable-http-access` : disabilità l'accesso HTTPs al cluster.
* `azure hdinsight script-action`: fornisce i comandi per la creazione o la gestione di azioni script in un cluster.
* `azure hdinsight config`: fornisce i comandi per la creazione di un file di configurazione che può essere usato con il comando `hdinsight cluster create` per fornire informazioni di configurazione.

### <a name="deprecated-commands"></a>Comandi deprecati
Se si usano i comandi `azure hdinsight job` per inviare processi al cluster HDInsight, questi non sono disponibili tramite i comandi di Gestione risorse. Se è necessario inviare processi a HDInsight da script a livello di programmazione, usare invece le API REST fornite da HDInsight. Per altre informazioni sull'invio di processi con le API REST, vedere i documenti seguenti.

* [Eseguire processi MapReduce con Hadoop in HDInsight mediante cURL](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Esecuzione di query Apache Hive con Apache Hadoop in HDInsight tramite cURL](hadoop/apache-hadoop-use-hive-curl.md)
* [Eseguire processi Apache Pig con Apache Hadoop in HDInsight tramite cURL](hadoop/apache-hadoop-use-pig-curl.md)

Per informazioni su altri modi per eseguire MapReduce, Apache Hive e Apache Pig con Apache Hadoop in modo interattivo, vedere [Usare MapReduce con Hadoop in HDInsight](hadoop/hdinsight-use-mapreduce.md), [Usare Apache Hive con Apache Hadoop in HDInsight](hadoop/hdinsight-use-hive.md) e [Usare Apache Pig con Apache Hadoop in HDInsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Esempi
**Creazione di un cluster**

* Comando precedente (ASM): `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Nuovo comando: `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Eliminazione di un cluster**

* Comando precedente (ASM): `azure hdinsight cluster delete myhdicluster`
* Nuovo comando: `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Elencare cluster**

* Comando precedente (ASM): `azure hdinsight cluster list`
* Nuovo comando: `azure hdinsight cluster list`

> [!NOTE]  
> Per il comando list specificando il gruppo di risorse con `-g` verranno restituiti solo i cluster nel gruppo di risorse specificato.

**Mostrare informazioni sul cluster**

* Comando precedente (ASM): `azure hdinsight cluster show myhdicluster`
* Nuovo comando: `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migrazione di Azure PowerShell ad Azure Resource Manager
Le informazioni generali su Azure PowerShell in modalità Azure Resource Manager sono disponibili in [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

I cmdlet di Azure Resource Manager per Azure PowerShell possono essere installati insieme ai cmdlet di ASM. È possibile distinguere i cmdlet delle due modalità in base ai nomi.  La modalità di Resource Manager dispone *AzHDInsight* nomi dei cmdlet al *AzureHDInsight* in modalità ASM.  Ad esempio, *New-AzHDInsightCluster* Visual Studio. *New-AzureHDInsightCluster*. I parametri e le opzioni possono avere nomi nuovi e sono disponibili molti nuovi parametri quando si usa Gestione risorse.  Ad esempio, alcuni cmdlet richiedono una nuova opzione denominata *- ResourceGroupName*. 

Prima di poter usare i cmdlet di HDInsight, è necessario connettersi con il proprio account Azure e creare un nuovo gruppo di risorse:

* [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)
* [New-AzResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Cmdlet rinominati
Per elencare i cmdlet ASM di HDInsight nella console di Windows PowerShell:

    help *azurehdinsight*

La tabella seguente include gli elenchi dei cmdlet di ASM e i relativi nomi in modalità Gestione risorse:

| Cmdlet ASM | Cmdlet di Gestione risorse |
| --- | --- |
| Add-AzureHDInsightConfigValues |[Add-AzHDInsightConfigValues](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalues) |
| Add-AzureHDInsightMetastore |[Add-AzHDInsightMetastore](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightmetastore) |
| Add-AzureHDInsightScriptAction |[Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) |
| Add-AzureHDInsightStorage |[Add-AzHDInsightStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightstorage) |
| Get-AzureHDInsightCluster |[Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) |
| Get-AzureHDInsightJob |[Get-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjob) |
| Get-AzureHDInsightJobOutput |[Get-AzHDInsightJobOutput](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjoboutput) |
| Get-AzureHDInsightProperties |[Get-AzHDInsightProperties](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightproperties) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/grant-azhdinsighthttpservicesaccess) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/grant-azhdinsightrdpservicesaccess) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzHDInsightHiveJob](https://docs.microsoft.com/powershell/module/az.hdinsight/invoke-azhdinsighthivejob) |
| New-AzureHDInsightCluster |[New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[New-AzHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) |
| New-AzureHDInsightHiveJobDefinition |[New-AzHDInsightHiveJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsighthivejobdefinition) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzHDInsightMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightmapreducejobdefinition) |
| New-AzureHDInsightPigJobDefinition |[New-AzHDInsightPigJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightpigjobdefinition) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzHDInsightSqoopJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightsqoopjobdefinition) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzHDInsightStreamingMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightstreamingmapreducejobdefinition) |
| Remove-AzureHDInsightCluster |[Remove-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/remove-azhdinsightcluster) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/revoke-azhdinsighthttpservicesaccess) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/revoke-azhdinsightrdpservicesaccess) |
| Set-AzureHDInsightClusterSize |[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) |
| Set-AzureHDInsightDefaultStorage |[Set-AzHDInsightDefaultStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightdefaultstorage) |
| Start-AzureHDInsightJob |[Start-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/start-azhdinsightjob) |
| Stop-AzureHDInsightJob |[Stop-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/stop-azhdinsightjob) |
| Use-AzureHDInsightCluster |[Use-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/use-azhdinsightcluster) |
| Wait-AzureHDInsightJob |[Wait-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/wait-azhdinsightjob) |

### <a name="new-cmdlets"></a>Nuovi cmdlet
Ecco i nuovi cmdlet disponibili solo in modalità Gestione risorse. 

**Cmdlet correlati all'azione script:**

* **Get-AzHDInsightPersistedScriptAction**: ottiene le azioni script persistenti per un cluster e le elenca in ordine cronologico oppure ottiene i dettagli per un'azione script persistente specificata. 
* **Get-AzHDInsightScriptActionHistory**: ottiene la cronologia delle azioni script per un cluster e le elenca in ordine cronologico inverso oppure ottiene i dettagli di un'azione script eseguita in precedenza. 
* **Remove-AzHDInsightPersistedScriptAction**: Rimuove un'azione script persistente da un cluster HDInsight.
* **Set-AzHDInsightPersistedScriptAction**: imposta un'azione script eseguita in precedenza come un'azione script persistente.
* **Submit-AzHDInsightScriptAction**: invia una nuova azione script a un cluster HDInsight di Azure. 

Per altre informazioni sull'utilizzo, vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md).

**Cmdlet correlati all'identità del cluster:**

* **Add-AzHDInsightClusterIdentity**: aggiunge un'identità del cluster a un oggetto di configurazione del cluster per consentire al cluster HDInsight di accedere ad Azure Data Lake Storage. Vedere [Creare un cluster HDInsight con Data Lake Storage tramite Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Esempi
**Creare cluster**

Comando precedente (ASM): 

    New-AzureHDInsightCluster `
        -Name $clusterName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainerName $containerName `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -Credential $httpCredential `
        -SshCredential $sshCredential

Nuovo comando:

    New-AzHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainer $containerName  `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpcredentials `
        -SshCredential $sshCredentials


**Eliminare cluster**

Comando precedente (ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

Nuovo comando:

    Remove-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Elencare cluster**

Comando precedente (ASM):

    Get-AzureHDInsightCluster

Nuovo comando:

    Get-AzHDInsightCluster 

**Mostrare cluster**

Comando precedente (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Nuovo comando:

    Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Altri esempi
* [Creare cluster HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Inviare i processi Apache Hive](hadoop/apache-hadoop-use-hive-powershell.md)
* [Inviare i processi Apache Pig](hadoop/apache-hadoop-use-pig-powershell.md)
* [Inviare i processi Apache Sqoop](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migrazione al nuovo HDInsight .NET SDK
[(ASM) HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt416619.aspx) basato su Azure Service Management è attualmente deprecato. È consigliabile usare [HDInsight .NET SDK basato su Gestione Risorse](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight) per la gestione delle risorse di Azure. I pacchetti HDInsight basati su ASM seguenti sono deprecati.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Questa sezione include puntatori ad altre informazioni relative all'esecuzione di determinate attività tramite l'SDK basato su Gestione risorse.

| Procedura sull'uso di HDInsight SDK basato su Gestione risorse | Collegamenti |
| --- | --- |
| Creare cluster HDInsight con .NET SDK |Vedere [Creare cluster basati su Linux in HDInsight tramite .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| Personalizzare un cluster tramite un'azione script con .NET SDK |Vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| Autenticare le applicazioni in modo interattivo tramite Azure Active Directory con .NET SDK |Vedere [Eseguire query Apache Hive con .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). Il frammento di codice in questo articolo usa l'approccio di autenticazione interattiva. |
| Autenticare le applicazioni in modo non interattivo tramite Azure Active Directory con .NET SDK |Vedere [Creare applicazioni .NET HDInsight di autenticazione non interattiva](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Inviare un processo Apache Hive con .NET SDK |Vedere [Inviare i processi Apache Hive](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Inviare un processo Apache Pig con .NET SDK |Vedere [Inviare i processi Apache Pig](hadoop/apache-hadoop-use-pig-dotnet-sdk.md) |
| Inviare un processo Apache Sqoop con .NET SDK |Vedere [Inviare i processi Apache Sqoop](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Elencare cluster HDInsight con .NET SDK |Vedere la sezione su come elencare cluster in [Gestire cluster Hadoop in HDInsight tramite .NET SDK](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Ridimensionare cluster HDInsight con .NET SDK |Vedere la sezione su come ridimensionare cluster in [Gestire cluster Hadoop in HDInsight tramite .NET SDK](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Concedere/revocare l'accesso ai cluster HDInsight con .NET SDK |Vedere la sezione su come concedere/revocare l'accesso in [Gestire cluster Hadoop in HDInsight tramite .NET SDK](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Aggiornare le credenziali utente HTTP per i cluster HDInsight con .NET SDK |Vedere la sezione su come aggiornare le credenziali utente HTTP in [Gestire cluster Hadoop in HDInsight tramite .NET SDK](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Trovare l'account di archiviazione predefinito per i cluster HDInsight con .NET SDK |Vedere la sezione su come trovare l'account di archiviazione predefinito in [Gestire cluster Hadoop in HDInsight tramite .NET SDK](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Eliminare cluster HDInsight con .NET SDK |Vedere la sezione su come eliminare cluster in [Gestire cluster Hadoop in HDInsight tramite .NET SDK](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Esempi
Di seguito sono riportati alcuni esempi sulla modalità di esecuzione di un'operazione con l'SDK basato su ASM e il frammento di codice equivalente per l'SDK basato su Gestione risorse.

**Creazione di un client CRUD del cluster**

* Comando precedente (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Nuovo comando (autorizzazione dell'entità servizio)
  
        //Service principal auth
        //This will log the application in as itself, rather than on behalf of a specific user.
        //For details, including how to set up the application, see:
        //   https://azure.microsoft.com/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* Nuovo comando (autorizzazione utente)
  
        //User auth
        //This will log the application in on behalf of the user.
        //The end-user will see a login popup.
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);

**Creazione di un cluster**

* Comando precedente (ASM)
  
        var clusterInfo = new ClusterCreateParameters
                    {
                        Name = dnsName,
                        DefaultStorageAccountKey = key,
                        DefaultStorageContainer = defaultStorageContainer,
                        DefaultStorageAccountName = storageAccountDnsName,
                        ClusterSizeInNodes = 1,
                        ClusterType = type,
                        Location = "West US",
                        UserName = "admin",
                        Password = "*******",
                        Version = version,
                        HeadNodeSize = NodeVMSize.Large,
                    };
        clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
        client.CreateCluster(clusterInfo);
* Nuovo comando
  
        var clusterCreateParameters = new ClusterCreateParameters
            {
                Location = "West US",
                ClusterType = "Hadoop",
                Version = "3.1",
                OSType = OSType.Linux,
                DefaultStorageAccountName = "mystorage.blob.core.windows.net",
                DefaultStorageAccountKey =
                    "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
                UserName = "hadoopuser",
                Password = "*******",
                HeadNodeSize = "ExtraLarge",
                RdpUsername = "hdirp",
                RdpPassword = ""*******",
                RdpAccessExpiry = new DateTime(2025, 3, 1),
                ClusterSizeInNodes = 5
            };
        var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
        clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);

**Abilitazione dell'accesso HTTP**

* Comando precedente (ASM)
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* Nuovo comando
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**Eliminazione di un cluster**

* Comando precedente (ASM)
  
        client.DeleteCluster(dnsName);
* Nuovo comando
  
        client.Clusters.Delete(resourceGroup, dnsname);

