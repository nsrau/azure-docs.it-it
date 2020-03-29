---
title: Aggiungere entità cluster per Azure Data Explorer usando PythonAdd cluster principals for Azure Data Explorer by using Python
description: In this article, you learn how to add cluster principals for Azure Data Explorer by using Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 637efdfe31d1f2eb0eaa5dd532dd9e9e67de5ce2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965138"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-python"></a>Aggiungere entità cluster per Azure Data Explorer usando PythonAdd cluster principals for Azure Data Explorer by using Python

> [!div class="op_single_selector"]
> * [C #](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Modello di Azure Resource ManagerAzure Resource Manager template](cluster-principal-resource-manager.md)

Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. In this article, you add cluster principals for Azure Data Explorer by using Python.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Creare un cluster](create-cluster-database-python.md).

## <a name="install-python-package"></a>Installa pacchetto Python

Per installare il pacchetto di Python per Esplora dati di Azure (Kusto), aprire un prompt dei comandi con un percorso contenente Python. Eseguire questo comando:

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-cluster-principal"></a>Aggiungere un'entità clusterAdd a cluster principal

Nell'esempio seguente viene illustrato come aggiungere un'entità cluster a livello di codice.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import ClusterPrincipalAssignment
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster that is created as part of the Prerequisites
cluster_name = "mykustocluster"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.cluster_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, principal_assignment_name= principal_assignment_name, parameters=ClusterPrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**Impostazione** | **Valore consigliato** | **Descrizione campo**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID tenant. Noto anche come ID directory.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID sottoscrizione utilizzato per la creazione delle risorse.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID client dell'applicazione che può accedere alle risorse nel tenant.|
| client_secret | *xxxxxxxxxxxxxx* | Segreto client dell'applicazione che può accedere alle risorse nel tenant. |
| resource_group_name | *testrg* | Nome del gruppo di risorse contenente il cluster.|
| cluster_name | *mykustocluster* | Nome del cluster.|
| principal_assignment_name | *clusterPrincipalAssignment1* | Nome della risorsa principale del cluster.|
| principal_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | L'ID entità, che può essere l'indirizzo di posta elettronica dell'utente, l'ID applicazione o il nome del gruppo di sicurezza.|
| ruolo | *AllDatabasesAdmin (Amministratore di tutti i database)* | Il ruolo dell'entità cluster, che può essere 'AllDatabasesAdmin' o 'AllDatabasesViewer'.|
| tenant_id_for_principal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID tenant dell'entità.|
| principal_type | *App* | Il tipo di entità, che può essere 'User', 'App' o 'Group'|

## <a name="next-steps"></a>Passaggi successivi

* [Aggiungere entità di databaseAdd database principals](database-principal-python.md)
