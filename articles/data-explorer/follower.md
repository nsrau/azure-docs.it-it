---
title: Usare la funzionalità del database follower per collegare i database in Azure Data ExplorerUse follower database feature to attach databases in Azure Data Explorer
description: Informazioni su come collegare i database in Azure Data Explorer usando la funzionalità del database dei follower.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: f6dbdb54c1c5a5d477c3ccb988963758faab83b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79140015"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>Usare il database follower per collegare i database in Azure Data ExplorerUse follower database to attach databases in Azure Data Explorer

La funzionalità del **database dei follower** consente di collegare un database situato in un cluster diverso al cluster di Azure Data Explorer.The follower database feature allows you to attach a database located in a different cluster to your Azure Data Explorer cluster. Il **database dei follower** è collegato in modalità *di sola lettura,* consentendo di visualizzare i dati ed eseguire query sui dati inseriti nel **database direttrice.** Il database dei follower sincronizza le modifiche nei database delle direttrice. A causa della sincronizzazione, c'è un ritardo dei dati di pochi secondi a pochi minuti nella disponibilità dei dati. La durata dell'intervallo di tempo dipende dalle dimensioni complessive dei metadati del database della direttrice. I database leader e follower usano lo stesso account di archiviazione per recuperare i dati. L'archiviazione è di proprietà del database leader. Il database follower visualizza i dati senza doverli ingerire. Poiché il database collegato è un database di sola lettura, i dati, le tabelle e i criteri nel database non possono essere modificati ad eccezione dei criteri di [memorizzazione nella cache,](#configure-caching-policy) [delle entità](#manage-principals)e delle [autorizzazioni.](#manage-permissions) I database collegati non possono essere eliminati. Essi devono essere staccati dal leader o seguace e solo allora possono essere eliminati. 

Il collegamento di un database a un cluster diverso utilizzando la funzionalità follower viene utilizzato come infrastruttura per condividere dati tra organizzazioni e team. La funzionalità è utile per separare le risorse di calcolo per proteggere un ambiente di produzione da casi d'uso non di produzione. Follower può essere usato anche per associare il costo del cluster Azure Data Explorer all'entità che esegue query sui dati.

## <a name="which-databases-are-followed"></a>Quali database vengono seguiti?

* Un cluster può seguire un database, più database o tutti i database di un cluster leader. 
* Un singolo cluster può seguire i database da più cluster leader. 
* Un cluster può contenere sia database follower che database leader

## <a name="prerequisites"></a>Prerequisiti

1. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
1. [Creare cluster e DB](/azure/data-explorer/create-cluster-database-portal) per il leader e il follower.
1. [Eseguire l'inserimento](/azure/data-explorer/ingest-sample-data) dei dati nel database leader utilizzando uno dei vari metodi illustrati nella [panoramica dell'inserimento.](/azure/data-explorer/ingest-data-overview)

## <a name="attach-a-database"></a>Collegare un database

Esistono vari metodi che è possibile utilizzare per collegare un database. In questo articolo viene illustrato il collegamento di un database usando c'è o un modello di Azure Resource Manager.In this article, we discuss attaching a database using C'è or an Azure Resource Manager template. Per collegare un database, è necessario disporre delle autorizzazioni per il cluster leader e il cluster follower. Per ulteriori informazioni sulle autorizzazioni, vedere [Gestire le autorizzazioni](#manage-permissions).

### <a name="attach-a-database-using-c"></a>Collegare un database utilizzando C #

#### <a name="needed-nugets"></a>NuGets necessari

* Installare [Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installare [Microsoft.Rest.ClientRuntime.Azure.Authentication per l'autenticazione](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication).

#### <a name="code-example"></a>Esempio di codice

```Csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "followerResouceGroup";
var leaderResourceGroup = "leaderResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
var attachedDatabaseConfigurationName = "uniqueNameForAttachedDatabaseConfiguration";
var databaseName = "db"; // Can be specific database name or * for all databases
var defaultPrincipalsModificationKind = "Union"; 
var location = "North Central US";

AttachedDatabaseConfiguration attachedDatabaseConfigurationProperties = new AttachedDatabaseConfiguration()
{
    ClusterResourceId = $"/subscriptions/{leaderSubscriptionId}/resourceGroups/{leaderResourceGroup}/providers/Microsoft.Kusto/Clusters/{leaderClusterName}",
    DatabaseName = databaseName,
    DefaultPrincipalsModificationKind = defaultPrincipalsModificationKind,
    Location = location
};

var attachedDatabaseConfigurations = resourceManagementClient.AttachedDatabaseConfigurations.CreateOrUpdate(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationName, attachedDatabaseConfigurationProperties);
```

### <a name="attach-a-database-using-python"></a>Collegare un database utilizzando Python

#### <a name="needed-modules"></a>Moduli necessari

```
pip install azure-common
pip install azure-mgmt-kusto
```

#### <a name="code-example"></a>Esempio di codice

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import AttachedDatabaseConfiguration
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
leader_resouce_group_name = "leaderResouceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_Configuration_name = "uniqueNameForAttachedDatabaseConfiguration"
database_name  = "db" # Can be specific database name or * for all databases
default_principals_modification_kind  = "Union"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + leader_subscription_id + "/resourceGroups/" + leader_resouce_group_name + "/providers/Microsoft.Kusto/Clusters/" + leader_cluster_name

attached_database_configuration_properties = AttachedDatabaseConfiguration(cluster_resource_id = cluster_resource_id, database_name = database_name, default_principals_modification_kind = default_principals_modification_kind, location = location)

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.create_or_update(follower_resource_group_name, follower_cluster_name, attached_database_Configuration_name, attached_database_configuration_properties)
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Collegare un database usando un modello di Azure Resource ManagerAttach a database using an Azure Resource Manager template

In questa sezione si apprenderà a collegare un database a un utente finale usando un modello di [Azure Resource Manager.](../azure-resource-manager/management/overview.md) 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the cluster to which the database will be attached."
            }
        },
        "attachedDatabaseConfigurationsName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the attached database configurations to create."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name of the database to follow. You can follow all databases by using '*'."
            }
        },
        "leaderClusterResourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of the leader cluster."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "Union",
            "metadata": {
                "description": "The default principal modification kind."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
            "properties": {
                "databaseName": "[parameters('databaseName')]",
                "clusterResourceId": "[parameters('leaderClusterResourceId')]",
                "defaultPrincipalsModificationKind": "[parameters('defaultPrincipalsModificationKind')]"
            }
        }
    ]
}
```

### <a name="deploy-the-template"></a>Distribuire il modello 

È possibile distribuire il modello di Azure Resource Manager usando il portale di Azure o Powershell.You can deploy the Azure Resource Manager template by [using the Azure portal](https://portal.azure.com) or using powershell.

   ![distribuzione di modelli](media/follower/template-deployment.png)


|**Impostazione**  |**Descrizione**  |
|---------|---------|
|Nome cluster follower     |  Il nome del cluster follower; in cui verrà distribuito il modello.  |
|Nome configurazioni database collegato    |    Nome dell'oggetto di configurazione di database collegato. Il nome può essere qualsiasi stringa univoca a livello di cluster.     |
|Nome database     |      Nome del database da seguire. Se si desidera seguire tutti i database del leader, utilizzare ' s '.   |
|ID risorsa cluster leader    |   ID risorsa del cluster leader.      |
|Tipo di modifica delle entità predefinite    |   Tipo di modifica principale predefinito. Può `Union`essere `Replace` `None`, o . Per ulteriori informazioni sul tipo di modifica principale predefinito, vedere comando di [controllo del tipo di modifica dell'entità](/azure/kusto/management/cluster-follower?branch=master#alter-follower-database-principals-modification-kind).      |
|Location   |   La posizione di tutte le risorse. Il leader e il seguace devono trovarsi nella stessa posizione.       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>Verificare che il database sia stato collegato correttamente

Per verificare che il database sia stato collegato correttamente, trovare i database collegati nel portale di [Azure.](https://portal.azure.com) 

1. Passare al cluster follower e selezionare **Database**
1. Cercare nuovi database di sola lettura nell'elenco dei database.

    ![Database dei follower di sola letturaRead-only follower database](media/follower/read-only-follower-database.png)

In alternativa:

1. Passare al cluster leader e selezionare **Database**
2. Verificare che i database pertinenti siano **contrassegnati** > come CONDIVIDI CON ALTRI**Sì**

    ![Lettura e scrittura di database collegati](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>Scollegare il database dei follower usando C # 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Scollegare il database dei follower collegato dal cluster follower

Il cluster follower può scollegare qualsiasi database collegato come segue:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var followerClusterName = "follower";
var attachedDatabaseConfigurationsName = "uniqueName";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Scollegare il database dei follower collegato dal cluster leader

Il cluster leader può scollegare qualsiasi database collegato come segue:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = leaderSubscriptionId
};

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
//The cluster and database that are created as part of the Prerequisites
var followerDatabaseDefinition = new FollowerDatabaseDefinition()
    {
        AttachedDatabaseConfigurationName = "uniqueName",
        ClusterResourceId = $"/subscriptions/{followerSubscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="detach-the-follower-database-using-python"></a>Scollegare il database dei follower utilizzando Python

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Scollegare il database dei follower collegato dal cluster follower

Il cluster follower può scollegare qualsiasi database collegato come segue:

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
follower_cluster_name = "follower"
attached_database_configurationName = "uniqueName"

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.delete(follower_resource_group_name, follower_cluster_name, attached_database_configurationName)
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Scollegare il database dei follower collegato dal cluster leader

Il cluster leader può scollegare qualsiasi database collegato come segue:

```python

from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import FollowerDatabaseDefinition
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResourceGroup"
leader_resource_group_name = "leaderResourceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_configuration_name = "uniqueName"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + follower_subscription_id + "/resourceGroups/" + follower_resource_group_name + "/providers/Microsoft.Kusto/Clusters/" + follower_cluster_name


#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.clusters.detach_follower_databases(resource_group_name = leader_resource_group_name, cluster_name = leader_cluster_name, cluster_resource_id = cluster_resource_id, attached_database_configuration_name = attached_database_configuration_name)
```

## <a name="manage-principals-permissions-and-caching-policy"></a>Gestire le entità, le autorizzazioni e i criteri di memorizzazione nella cacheManage principals, permissions, and caching policy

### <a name="manage-principals"></a>Gestire le entità

Quando si collega un database, specificare il **tipo di modifica delle entità predefinite.** L'impostazione predefinita è mantenere la raccolta di database leader di [entità autorizzate](/azure/kusto/management/access-control/index#authorization)

|**Tipo** |**Descrizione**  |
|---------|---------|
|**Union**     |   Le entità di database collegate includeranno sempre le entità di database originali e le nuove entità aggiuntive aggiunte al database dei follower.      |
|**Sostituire**   |    Nessuna ereditarietà delle entità dal database originale. È necessario creare nuove entità per il database collegato.     |
|**Nessuno**   |   Le entità di database collegate includono solo le entità del database originale senza entità aggiuntive.      |

Per ulteriori informazioni sull'utilizzo dei comandi di controllo per configurare le entità autorizzate, vedere [Comandi di controllo per la gestione di un cluster di follower](/azure/kusto/management/cluster-follower).

### <a name="manage-permissions"></a>Gestione autorizzazioni

La gestione dell'autorizzazione di sola lettura del database è la stessa di tutti i tipi di database. Vedere Gestire le autorizzazioni nel portale di [Azure.](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal)

### <a name="configure-caching-policy"></a>Configurare i criteri di memorizzazione nella cacheConfigure caching policy

L'amministratore del database follower può modificare i criteri di [memorizzazione nella cache](/azure/kusto/management/cache-policy) del database collegato o di una delle relative tabelle nel cluster di hosting. L'impostazione predefinita è mantenere la raccolta di database leader dei criteri di memorizzazione nella cache a livello di database e tabella. È possibile, ad esempio, avere un criterio di memorizzazione nella cache di 30 giorni nel database leader per l'esecuzione di report mensili e un criterio di memorizzazione nella cache di tre giorni nel database dei follower per eseguire query solo sui dati recenti per la risoluzione dei problemi. Per ulteriori informazioni sull'utilizzo dei comandi di controllo per configurare i criteri di memorizzazione nella cache nel database o nella tabella dei follower, vedere [Controllare i comandi per la gestione di un cluster di follower.](/azure/kusto/management/cluster-follower)

## <a name="limitations"></a>Limitazioni

* Il follower e i cluster leader devono trovarsi nella stessa regione.
* [L'inserimento di streaming](/azure/data-explorer/ingest-data-streaming) non può essere usato in un database che viene seguito.
* La crittografia dei dati tramite [chiavi gestite dai clienti](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault) non è supportata nei cluster leader e follower. 
* Non è possibile eliminare un database collegato a un cluster diverso prima di scollegarlo.
* Non è possibile eliminare un cluster con un database collegato a un cluster diverso prima di scollegarlo.
* Non è possibile arrestare un cluster che ha collegato i database follower o leader. 

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla configurazione del cluster follower, vedere Comandi di [controllo per la gestione di un cluster di follower.](/azure/kusto/management/cluster-follower)
