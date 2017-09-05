---
title: Eseguire la migrazione agli strumenti di Azure Resource Manager per HDInsight | Documentazione Microsoft
description: Come eseguire la migrazione a strumenti di sviluppo di Azure Resource Manager per i cluster HDInsight
services: hdinsight
editor: cgronlun
manager: jhubbard
author: nitinme
documentationcenter: 
ms.assetid: 05efedb5-6456-4552-87ff-156d77fbe2e1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: bb700c7de96712666bc4be1f8e430a2e94761f69
ms.openlocfilehash: 3606df64b619b62f8b9e5aec2abc4efc994c37e3
ms.contentlocale: it-it
ms.lasthandoff: 01/24/2017

---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrazione a strumenti di sviluppo basati su Azure Resource Manager per i cluster HDInsight

In HDInsight stanno per essere deprecati gli strumenti basati su Azure Service Management (ASM) per HDInsight. Se si usa Azure PowerShell, l'interfaccia della riga di comando di Azure o HDInsight .NET SDK per l'utilizzo dei cluster HDInsight, in futuro è consigliabile passare alle versioni di PowerShell, dell'interfaccia della riga di comando e di .NET SDK basate su Azure Resource Manager (ARM). Questo articolo fornisce informazioni utili su come eseguire la migrazione al nuovo approccio basato su ARM. Se applicabili, l'articolo evidenzia anche le differenze tra gli approcci basati su ASM e ARM per HDInsight.

> [!IMPORTANT]
> Il supporto per ASM basato su PowerShell, l'interfaccia della riga di comando e .NET SDK verrà sospeso il **1° gennaio 2017**.
> 
> 

## <a name="migrating-azure-cli-to-azure-resource-manager"></a>Migrazione dell'interfaccia della riga di comando di Azure ad Azure Resource Manager
L'interfaccia della riga di comando di Azure ora passa per impostazione predefinita alla modalità Azure Resource Manager (ARM), a meno che non si sta eseguendo l'aggiornamento da un'installazione precedente. In questo caso, può essere necessario usare il comando necessario `azure config mode arm` per passare alla modalità ARM.

I comandi di base forniti dall'interfaccia della riga di comando di Azure per l'utilizzo di HDInsight tramite Azure Service Management (ASM) sono gli stessi per ARM. Quando si usa ARM, alcuni parametri e opzioni possono tuttavia avere nomi nuovi e sono disponibili molti parametri nuovi. Ad esempio, è ora possibile usare `azure hdinsight cluster create` per specificare la rete virtuale di Azure in cui deve essere creato un cluster o le informazioni dei metastore Hive e Oozie.

Ecco i comandi di base per l'utilizzo di HDInsight tramite Azure Resource Manager:

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
Se si usano i comandi `azure hdinsight job` per inviare processi al cluster HDInsight, questi non sono disponibili tramite i comandi di ARM. Se è necessario inviare processi a HDInsight da script a livello di programmazione, usare invece le API REST fornite da HDInsight. Per altre informazioni sull'invio di processi con le API REST, vedere i documenti seguenti.

* [Eseguire processi MapReduce con Hadoop in HDInsight mediante cURL](hdinsight-hadoop-use-mapreduce-curl.md)
* [Eseguire query Hive con Hadoop in HDInsight mediante Curl](hdinsight-hadoop-use-hive-curl.md)
* [Eseguire processi Pig con Hadoop in HDInsight mediante cUrl](hdinsight-hadoop-use-pig-curl.md)

Per informazioni su altri modi per eseguire MapReduce, Hive e Pig in modo interattivo, vedere [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md), [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md) e [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md).

### <a name="examples"></a>esempi
**Creazione di un cluster**

* Comando precedente (ASM): `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Nuovo comando (ARM): `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Eliminazione di un cluster**

* Comando precedente (ASM): `azure hdinsight cluster delete myhdicluster`
* Nuovo comando (ARM): `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Elencare i cluster**

* Comando precedente (ASM): `azure hdinsight cluster list`
* Nuovo comando (ARM): `azure hdinsight cluster list`

> [!NOTE]
> Per il comando list specificando il gruppo di risorse con `-g` verranno restituiti solo i cluster nel gruppo di risorse specificato.
> 
> 

**Mostrare informazioni sul cluster**

* Comando precedente (ASM): `azure hdinsight cluster show myhdicluster`
* Nuovo comando (ARM): `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migrazione di Azure PowerShell ad Azure Resource Manager
Le informazioni generali su Azure PowerShell in modalità Azure Resource Manager(ARM) sono disponibili in [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

I cmdlet ARM di Azure PowerShell possono essere installati side-by-side con i cmdlet ASM. È possibile distinguere i cmdlet delle due modalità in base ai nomi.  I nomi dei cmdlet in modalità ARM includono *AzureRmHDInsight*, mentre quelli in modalità ASM includono *AzureHDInsight*.  Ad esempio, *New-AzureRmHDInsightCluster* e *New-AzureHDInsightCluster*. Parametri e le opzioni possono avere nomi nuovi e sono disponibili molti nuovi parametri quando si usa ARM.  Ad esempio, alcuni cmdlet richiedono una nuova opzione denominata *- ResourceGroupName*. 

Prima di poter usare i cmdlet di HDInsight, è necessario connettersi con il proprio account Azure e creare un nuovo gruppo di risorse:

* Login-AzureRmAccount o [Select-AzureRmProfile](https://msdn.microsoft.com/library/mt619310.aspx). Vedere [Autenticazione di un'entità servizio con Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Cmdlet rinominati
Per elencare i cmdlet ASM di HDInsight nella console di Windows PowerShell:

    help *azurermhdinsight*

La tabella include l'elenco dei cmdlet ASM e i relativi nomi in modalità ARM:

| Cmdlet ASM | Cmdlet ARM |
| --- | --- |
| Add-AzureHDInsightConfigValues |[Add-AzureRmHDInsightConfigValues](https://msdn.microsoft.com/library/mt603530.aspx) |
| Add-AzureHDInsightMetastore |[Add-AzureRmHDInsightMetastore](https://msdn.microsoft.com/library/mt603670.aspx) |
| Add-AzureHDInsightScriptAction |[Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) |
| Add-AzureHDInsightStorage |[Add-AzureRmHDInsightStorage](https://msdn.microsoft.com/library/mt619445.aspx) |
| Get-AzureHDInsightCluster |[Get-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619371.aspx) |
| Get-AzureHDInsightJob |[Get-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603590.aspx) |
| Get-AzureHDInsightJobOutput |[Get-AzureRmHDInsightJobOutput](https://msdn.microsoft.com/library/mt603793.aspx) |
| Get-AzureHDInsightProperties |[Get-AzureRmHDInsightProperties](https://msdn.microsoft.com/library/mt603546.aspx) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619407.aspx) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603717.aspx) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzureRmHDInsightHiveJob](https://msdn.microsoft.com/library/mt603593.aspx) |
| New-AzureHDInsightCluster |[New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) |
| New-AzureHDInsightClusterConfig |[New-AzureRmHDInsightClusterConfig](https://msdn.microsoft.com/library/mt603700.aspx) |
| New-AzureHDInsightHiveJobDefinition |[New-AzureRmHDInsightHiveJobDefinition](https://msdn.microsoft.com/library/mt619448.aspx) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzureRmHDInsightMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| New-AzureHDInsightPigJobDefinition |[New-AzureRmHDInsightPigJobDefinition](https://msdn.microsoft.com/library/mt603671.aspx) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzureRmHDInsightSqoopJobDefinition](https://msdn.microsoft.com/library/mt608551.aspx) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzureRmHDInsightStreamingMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| Remove-AzureHDInsightCluster |[Remove-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619431.aspx) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619375.aspx) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603523.aspx) |
| Set-AzureHDInsightClusterSize |[Set-AzureRmHDInsightClusterSize](https://msdn.microsoft.com/library/mt603513.aspx) |
| Set-AzureHDInsightDefaultStorage |[Set-AzureRmHDInsightDefaultStorage](https://msdn.microsoft.com/library/mt603486.aspx) |
| Start-AzureHDInsightJob |[Start-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603798.aspx) |
| Stop-AzureHDInsightJob |[Stop-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt619424.aspx) |
| Use-AzureHDInsightCluster |[Use-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619442.aspx) |
| Wait-AzureHDInsightJob |[Wait-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603834.aspx) |

### <a name="new-cmdlets"></a>Nuovi cmdlet
Ecco i nuovi cmdlet disponibili solo in modalità ARM. 

**Cmdlet correlati all'azione script:**

* **Get-AzureRmHDInsightPersistedScriptAction**: ottiene le azioni script persistenti per un cluster e le elenca in ordine cronologico oppure ottiene i dettagli per un'azione script persistente specificata. 
* **Get-AzureRmHDInsightScriptActionHistory**: ottiene la cronologia delle azioni script per un cluster e le elenca in ordine cronologico inverso oppure ottiene i dettagli di un'azione script eseguita in precedenza. 
* **Remove AzureRmHDInsightPersistedScriptAction**: rimuove un'azione script persistente da un cluster HDInsight.
* **Set AzureRmHDInsightPersistedScriptAction**: imposta un'azione script eseguita in precedenza come un'azione script persistente.
* **Submit-AzureRmHDInsightScriptAction**: invia una nuova azione script a un cluster HDInsight di Azure. 

Per altre informazioni sull'utilizzo, vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md).

**Cmdlet correlati a identità del cluster:**

* **Add-AzureRmHDInsightClusterIdentity**: aggiunge un'identità del cluster a un oggetto di configurazione del cluster per consentire al cluster HDInsight di accedere ad archivi Azure Data Lake. Vedere [Creare un cluster HDInsight con Archivio Data Lake tramite Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>esempi
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

Nuovo comando (ARM):

    New-AzureRmHDInsightCluster `
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

Nuovo comando (ARM):

    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Elencare cluster**

Comando precedente (ASM):

    Get-AzureHDInsightCluster

Nuovo comando (ARM):

    Get-AzureRmHDInsightCluster 

**Mostrare cluster**

Comando precedente (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Nuovo comando (ARM):

    Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Altri esempi
* [Creare cluster HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Invio di processi Hive](hdinsight-hadoop-use-hive-powershell.md)
* [Eseguire processi Pig mediante PowerShell](hdinsight-hadoop-use-pig-powershell.md)
* [Eseguire processi Sqoop con Azure PowerShell per Hadoop in HDInsight](hdinsight-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-arm-based-hdinsight-net-sdk"></a>Migrazione di HDInsight .NET SDK basato su ARM
[(ASM) HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt416619.aspx) basato su Azure Service Management è attualmente deprecato. È consigliabile usare [(ARM) HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt271028.aspx)basato su Azure Resource Manager. I pacchetti HDInsight basati su ASM seguenti sono deprecati.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Questa sezione include i puntatori ad altre informazioni relative all'esecuzione di determinate attività tramite l'SDK basato su ARM.

| Utilizzo HDInsight SDK basato su ARM - Procedure | Collegamenti |
| --- | --- |
| Creare cluster HDInsight con .NET SDK |Vedere [Creare cluster basati su Linux in HDInsight tramite .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| Personalizzare un cluster tramite un'azione script con .NET SDK |Vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| Autenticare le applicazioni in modo interattivo tramite Azure Active Directory con .NET SDK |Vedere [Eseguire query Hive con HDInsight .NET SDK](hdinsight-hadoop-use-hive-dotnet-sdk.md) Il frammento di codice in questo articolo usa l'approccio di autenticazione interattiva. |
| Autenticare le applicazioni in modo non interattivo tramite Azure Active Directory con .NET SDK |Vedere [Creare applicazioni .NET HDInsight di autenticazione non interattiva](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Inviare un processo Hive con .NET SDK |Vedere [Eseguire query Hive con HDInsight .NET SDK](hdinsight-hadoop-use-hive-dotnet-sdk.md) |
| Inviare un processo Pig con .NET SDK |Vedere [Eseguire processi Pig con .NET SDK per Hadoop in HDInsight](hdinsight-hadoop-use-pig-dotnet-sdk.md) |
| Inviare un processo Sqoop con .NET SDK |Vedere [Eseguire processi Sqoop con .NET SDK per Hadoop in HDInsight](hdinsight-hadoop-use-sqoop-dotnet-sdk.md) |
| Elencare cluster HDInsight con .NET SDK |Vedere la sezione su come elencare cluster in [Gestire cluster Hadoop in HDInsight tramite .NET SDK](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Ridimensionare cluster HDInsight con .NET SDK |Vedere la sezione su come ridimensionare cluster in [Gestire cluster Hadoop in HDInsight tramite .NET SDK](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Concedere/revocare l'accesso ai cluster HDInsight con .NET SDK |Vedere la sezione su come concedere/revocare l'accesso in [Gestire cluster Hadoop in HDInsight tramite .NET SDK](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Aggiornare le credenziali utente HTTP per i cluster HDInsight con .NET SDK |Vedere la sezione su come aggiornare le credenziali utente HTTP in [Gestire cluster Hadoop in HDInsight tramite .NET SDK](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Trovare l'account di archiviazione predefinito per i cluster HDInsight con .NET SDK |Vedere la sezione su come trovare l'account di archiviazione predefinito in [Gestire cluster Hadoop in HDInsight tramite .NET SDK](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Eliminare cluster HDInsight con .NET SDK |Vedere la sezione su come eliminare cluster in [Gestire cluster Hadoop in HDInsight tramite .NET SDK](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>esempi
Di seguito sono riportati alcuni esempi sulla modalità di esecuzione di un'operazione con l'SDK basato su ASM e il frammento di codice equivalente per l'SDK basato su ARM.

**Creazione di un client CRUD del cluster**

* Comando precedente (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager (ARM)
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Nuovo comando (ARM) (autorizzazione dell'entità servizio)
  
        //Service principal auth
        //This will log the application in as itself, rather than on behalf of a specific user.
        //For details, including how to set up the application, see:
        //   https://azure.microsoft.com/en-us/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* Nuovo comando (ARM) (autorizzazione utente)
  
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
* Nuovo comando (ARM)
  
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
* Nuovo comando (ARM)
  
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
* Nuovo comando (ARM)
  
        client.Clusters.Delete(resourceGroup, dnsname);


