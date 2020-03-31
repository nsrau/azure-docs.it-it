---
title: Aggiungere entità di database per Azure Data Explorer tramite PythonAdd database principals for Azure Data Explorer by using Python
description: In this article, you learn how to add database principals for Azure Data Explorer by using Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 8b9c4f4d5427b326c273558db0bff808068b192a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965008"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-python"></a>Aggiungere entità di database per Azure Data Explorer tramite PythonAdd database principals for Azure Data Explorer by using Python

> [!div class="op_single_selector"]
> * [C #](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Modello di Azure Resource ManagerAzure Resource Manager template](database-principal-resource-manager.md)

Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. In this article, you add database principals for Azure Data Explorer by using Python.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Creare un cluster e un database](create-cluster-database-python.md)

## <a name="install-python-package"></a>Installa pacchetto Python

Per installare il pacchetto di Python per Esplora dati di Azure (Kusto), aprire un prompt dei comandi con un percorso contenente Python. Eseguire questo comando:

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Aggiungere un'entità di databaseAdd a database principal

Nell'esempio seguente viene illustrato come aggiungere un'entità di database a livello di codice.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabasePrincipalAssignment
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
#The cluster and database that is created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.database_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, principal_assignment_name= principal_assignment_name, parameters=DatabasePrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**Impostazione** | **Valore consigliato** | **Descrizione campo**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID tenant. Noto anche come ID directory.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID sottoscrizione utilizzato per la creazione delle risorse.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID client dell'applicazione che può accedere alle risorse nel tenant.|
| client_secret | *xxxxxxxxxxxxxx* | Segreto client dell'applicazione che può accedere alle risorse nel tenant. |
| resource_group_name | *testrg* | Nome del gruppo di risorse contenente il cluster.|
| cluster_name | *mykustocluster* | Nome del cluster.|
| database_name | *mykustodatabase* | Nome del database.|
| principal_assignment_name | *databasePrincipalAssignment1* | Nome della risorsa principale di database.|
| principal_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | L'ID entità, che può essere l'indirizzo di posta elettronica dell'utente, l'ID applicazione o il nome del gruppo di sicurezza.|
| ruolo | *Amministrazione* | Il ruolo dell'entità di database, che può essere 'Admin', 'Ingestor', 'Monitor', 'User', 'UnrestrictedViewers', 'Viewer'.|
| tenant_id_for_principal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID tenant dell'entità.|
| principal_type | *App* | Il tipo di entità, che può essere 'User', 'App' o 'Group'|

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire l'inserimento di dati usando la libreria Python di Azure Data ExplorerIngest data using the Azure Data Explorer Python library](python-ingest-data.md)
