---
title: Usare la funzionalità di database di seguito per alleghi i database in Azure Esplora dati
description: Informazioni su come alleghi i database in Azure Esplora dati usando la funzionalità di database di seguito.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: dd2c29632d70da64251c5e1736a9cb7d82f5d0dc
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667342"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>Usare il database di seguito per alleghi i database in Azure Esplora dati

La funzionalità di **database di seguito** consente di aggiungere un database che si trova in un cluster diverso al cluster di Esplora dati di Azure. Il **database di seguito** viene collegato in modalità di sola *lettura* , consentendo di visualizzare i dati ed eseguire query sui dati inseriti nel **database**principale. Il database seguente sincronizza le modifiche nei database leader. A causa della sincronizzazione, si verifica un ritardo dei dati di pochi secondi per alcuni minuti di disponibilità dei dati. La lunghezza dell'intervallo di tempo dipende dalle dimensioni complessive dei metadati del database leader. Per recuperare i dati, i database leader e i database successivi usano lo stesso account di archiviazione. Lo spazio di archiviazione è di proprietà del database leader. Il database di seguito Visualizza i dati senza che sia necessario inserirli. Poiché il database collegato è di sola lettura, i dati, le tabelle e i criteri del database non possono essere modificati tranne che per i criteri, le [entità](#manage-principals)e le [autorizzazioni](#manage-permissions)di [memorizzazione nella cache](#configure-caching-policy). Non è possibile eliminare i database collegati. Devono essere scollegati dal leader o dal seguito e solo successivamente possono essere eliminati. 

Il fissaggio di un database a un cluster diverso mediante la funzionalità di seguito viene usato come infrastruttura per condividere i dati tra organizzazioni e team. Questa funzionalità è utile per separare le risorse di calcolo per proteggere un ambiente di produzione da casi di utilizzo non di produzione. Il follower può essere usato anche per associare il costo di Azure Esplora dati cluster alla parte che esegue query sui dati.

## <a name="which-databases-are-followed"></a>Quali database vengono seguiti?

* Un cluster può seguire un database, diversi database o tutti i database di un cluster leader. 
* Un singolo cluster può seguire i database di più cluster leader. 
* Un cluster può contenere sia database di seguito che database leader

## <a name="prerequisites"></a>Prerequisiti

1. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
1. [Creare un cluster e un database](/azure/data-explorer/create-cluster-database-portal) per il leader e il follower.
1. Inserire [i dati](/azure/data-explorer/ingest-sample-data) nel database leader usando uno dei diversi metodi descritti in [Cenni preliminari sull'](/azure/data-explorer/ingest-data-overview)inserimento.

## <a name="attach-a-database"></a>Collegamento di un database

Esistono diversi metodi che è possibile utilizzare per alleghire un database. In questo articolo viene illustrato come aggiungere un database usando C# o un modello di Azure Resource Manager. Per allineare un database, è necessario disporre delle autorizzazioni per il cluster leader e il cluster di follower. Per ulteriori informazioni sulle autorizzazioni, vedere [gestire le autorizzazioni](#manage-permissions).

### <a name="attach-a-database-using-c"></a>Connessione di un database tramiteC#

#### <a name="needed-nugets"></a>NuGet necessari

* Installare [Microsoft. Azure. Management. kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installare [Microsoft. Rest. ClientRuntime. Azure. Authentication per l'autenticazione](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication).

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
var attachedDatabaseConfigurationName = "adc";
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

### <a name="attach-a-database-using-python"></a>Connessione di un database tramite Python

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
attached_database_Configuration_name = "adc"
database_name  = "db" # Can be specific database name or * for all databases
default_principals_modification_kind  = "Union"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + leader_subscription_id + "/resourceGroups/" + leader_resouce_group_name + "/providers/Microsoft.Kusto/Clusters/" + leader_cluster_name

attached_database_configuration_properties = AttachedDatabaseConfiguration(cluster_resource_id = cluster_resource_id, database_name = database_name, default_principals_modification_kind = default_principals_modification_kind, location = location)

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.create_or_update(follower_resource_group_name, follower_cluster_name, attached_database_Configuration_name, attached_database_configuration_properties)
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Connessione di un database tramite un modello di Azure Resource Manager

In questa sezione viene illustrato come aggiungere un database usando un [modello di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the follower cluster."
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
                "description": "Name of the leader cluster to create."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "",
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
            "name": "[parameters('followerClusterName')]",
            "type": "Microsoft.Kusto/clusters",
            "sku": {
                "name": "Standard_D13_v2",
                "tier": "Standard",
                "capacity": 2
            },
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]"
        },
        {
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Kusto/clusters', parameters('followerClusterName'))]"
            ],
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

È possibile distribuire il modello di Azure Resource Manager [usando il portale di Azure](https://portal.azure.com) o tramite PowerShell.

   ![distribuzione modello](media/follower/template-deployment.png)


|**Impostazione**  |**Descrizione**  |
|---------|---------|
|Nome del cluster di seguito     |  Nome del cluster di seguito       |
|Nome delle configurazioni del database associato    |    Nome dell'oggetto configurazioni del database associato. Il nome deve essere univoco a livello di cluster.     |
|Nome database     |      Nome del database da seguire. Se si desidera seguire tutti i database del leader, utilizzare "*".   |
|ID risorsa cluster leader    |   ID risorsa del cluster leader.      |
|Tipo di modifica delle entità predefinite    |   Tipo di modifica principale predefinito. Può essere `Union`, `Replace` o `None`. Per ulteriori informazioni sul tipo di modifica principale predefinito, vedere [comando di controllo del tipo di modifica principale](/azure/kusto/management/cluster-follower?branch=master#alter-follower-database-principals-modification-kind).      |
|Località   |   Percorso di tutte le risorse. Il leader e il successivo devono trovarsi nella stessa posizione.       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>Verificare che il database sia stato collegato correttamente

Per verificare che il database sia stato collegato correttamente, trovare i database collegati nel [portale di Azure](https://portal.azure.com). 

1. Passare al cluster di follower e selezionare **database**
1. Cercare nuovi database di sola lettura nell'elenco database.

    ![Database di follower di sola lettura](media/follower/read-only-follower-database.png)

In alternativa:

1. Passare al cluster leader e selezionare **database**
2. Verificare che i database pertinenti siano contrassegnati come **condivisi con altri** > **Sì**

    ![Lettura e scrittura di database collegati](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>Scollegare il database di follower usandoC# 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Scollegare il database di follower collegato dal cluster di follower

Il cluster di follower può scollegare qualsiasi database collegato come segue:

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
var attachedDatabaseConfigurationsName = "adc";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Scollegare il database di follower collegato dal cluster leader

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
        AttachedDatabaseConfigurationName = "adc",
        ClusterResourceId = $"/subscriptions/{followerSubscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="detach-the-follower-database-using-python"></a>Scollegare il database di follower con Python

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Scollegare il database di follower collegato dal cluster di follower

Il cluster di follower può scollegare qualsiasi database collegato come segue:

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
attached_database_configurationName = "adc"

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.delete(follower_resource_group_name, follower_cluster_name, attached_database_configurationName)
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Scollegare il database di follower collegato dal cluster leader

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
attached_database_configuration_name = "adc"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + follower_subscription_id + "/resourceGroups/" + follower_resource_group_name + "/providers/Microsoft.Kusto/Clusters/" + follower_cluster_name


#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.clusters.detach_follower_databases(resource_group_name = leader_resource_group_name, cluster_name = leader_cluster_name, cluster_resource_id = cluster_resource_id, attached_database_configuration_name = attached_database_configuration_name)
```

## <a name="manage-principals-permissions-and-caching-policy"></a>Gestire entità, autorizzazioni e criteri di memorizzazione nella cache

### <a name="manage-principals"></a>Gestisci entità

Quando si connette un database, specificare il **tipo di modifica "entità predefinite"** . Il valore predefinito è mantenere la raccolta di database leader delle [entità autorizzate](/azure/kusto/management/access-control/index#authorization)

|**Tipologia** |**Descrizione**  |
|---------|---------|
|**Unione**     |   Le entità di database associate includeranno sempre le entità di database originali, oltre alle nuove entità aggiuntive aggiunte al database di seguito.      |
|**Replace**   |    Nessuna ereditarietà delle entità del database originale. È necessario creare nuove entità per il database collegato.     |
|**Nessuno**   |   Le entità di database associate includono solo le entità del database originale senza entità aggiuntive.      |

Per ulteriori informazioni sull'utilizzo dei comandi di controllo per configurare le entità autorizzate, vedere [comandi di controllo per la gestione di un cluster di follower](/azure/kusto/management/cluster-follower).

### <a name="manage-permissions"></a>Gestisci autorizzazioni

La gestione dell'autorizzazione del database di sola lettura è identica a quella di tutti i tipi di database. Vedere [gestire le autorizzazioni nel portale di Azure](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

### <a name="configure-caching-policy"></a>Configurare i criteri di memorizzazione nella cache

L'amministratore del database seguito può modificare i [criteri di memorizzazione nella cache](/azure/kusto/management/cache-policy) del database collegato o delle relative tabelle nel cluster host. Per impostazione predefinita, la raccolta di database leader viene mantenuta nei criteri di memorizzazione nella cache a livello di tabella e database. È possibile, ad esempio, disporre di un criterio di memorizzazione nella cache di 30 giorni nel database leader per l'esecuzione di report mensili e di un criterio di memorizzazione nella cache di tre giorni nel database di seguito per eseguire una query solo sui dati recenti per la risoluzione dei problemi. Per ulteriori informazioni sull'utilizzo dei comandi di controllo per configurare i criteri di memorizzazione nella cache nel database o nella tabella di seguito, vedere [comandi di controllo per la gestione di un cluster di follower](/azure/kusto/management/cluster-follower).

## <a name="limitations"></a>Limitazioni

* Il follower e i cluster leader devono trovarsi nella stessa area.
* Non è possibile usare l'inserimento di [flussi](/azure/data-explorer/ingest-data-streaming) in un database che viene seguito.
* Non è possibile eliminare un database collegato a un cluster diverso prima di scollegarlo.
* Non è possibile eliminare un cluster con un database collegato a un cluster diverso prima di scollegarlo.
* Non è possibile arrestare un cluster che ha associato un o più database. 

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla configurazione del cluster di seguito, vedere [comandi di controllo per la gestione di un cluster di follower](/azure/kusto/management/cluster-follower).