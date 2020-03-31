---
title: 'Creare criteri per il cluster e il database di Azure Data Explorer usando la libreria Python di Azure Data ExplorerCreate policies for Azure Data Explorer cluster and database using the Azure Data Explorer Python library '
description: In questo articolo imparerai a creare criteri usando Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: a0fe86e2dcb802b822cb08ed0922b5da9c5cfd1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667286"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-python"></a>Creare criteri di database e tabelle per Azure Data Explorer usando PythonCreate database and table policies for Azure Data Explorer by using Python

> [!div class="op_single_selector"]
> * [C #](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. In this article, you create database and table policies for Azure Data Explorer using Python.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Un cluster e un database di test](create-cluster-database-python.md)
* [Una tabella di prova](python-ingest-data.md#create-a-table-on-your-cluster)

## <a name="install-the-data-libraries"></a>Installare le raccolte dati

```
pip install azure-common
pip install azure-mgmt-kusto
pip install azure-kusto-data (Optional, for changing table's policies)
```

## <a name="authentication"></a>Authentication
Per eseguire gli esempi in questo articolo, sono necessarie un'applicazione Azure AD e un'entità servizio in grado di accedere alle risorse. È possibile usare la stessa applicazione Azure AD per l'autenticazione da un cluster di test e da [un database.](create-cluster-database-csharp.md#authentication) Se si vuole usare un'applicazione Azure AD diversa, vedere [Creare un'applicazione Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) per creare un'applicazione Azure AD gratuita e aggiungere un'assegnazione di ruolo nell'ambito della sottoscrizione. Viene inoltre illustrato come `Directory (tenant) ID` `Application ID`ottenere `Client Secret`il , , e . Potrebbe essere necessario aggiungere la nuova applicazione Azure AD come entità nel database, vedere Gestire le autorizzazioni del database di Azure Data Explorer.You may need to add the new Azure AD Application as a principal in the database, see [Manage Azure Data Explorer database permissions](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions).    

## <a name="alter-database-retention-policy"></a>Modificare i criteri di conservazione del database
Imposta un criterio di conservazione con un periodo di eliminazione temporanea di 10 giorni.

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabaseUpdate
from azure.common.credentials import ServicePrincipalCredentials
import datetime

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

resource_group_name = "testrg";
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster";
database_name = "mykustodatabase";

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kustoManagementClient.databases.update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name,
                                           parameters=DatabaseUpdate(soft_delete_period=datetime.timedelta(days=10)))
```

## <a name="alter-database-cache-policy"></a>Modificare i criteri di cache del database
Imposta un criterio di cache per il database in base al fatto che gli ultimi cinque giorni di dati saranno nell'SSD del cluster.

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabaseUpdate
from azure.common.credentials import ServicePrincipalCredentials
import datetime

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

resource_group_name = "testrg";
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster";
database_name = "mykustodatabase";

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kustoManagementClient.databases.update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name,
                                           parameters=DatabaseUpdate(hot_cache_period=datetime.timedelta(days=5)))
```

## <a name="alter-table-cache-policy"></a>Modificare i criteri di cache delle tabelle
Imposta un criterio di cache per la tabella in base al fatto che gli ultimi cinque giorni di dati saranno nell'SSD del cluster.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
kusto_uri = "https://<ClusterName>.<Region>.kusto.windows.net"
database_name = "<DatabaseName>"
#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
#Application ID
client_id_to_add = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

kusto_connection_string_builder = KustoConnectionStringBuilder.with_aad_application_key_authentication(connection_string=kusto_uri, aad_app_id=client_id, app_key=client_secret, authority_id=tenant_id)

#The table that is created as part of the Prerequisites
table_name = "<TableName>"
caching_policy = r'hot = 5d'
command = '.alter table {} policy caching '.format(table_name) +  caching_policy
kusto_client.execute_mgmt(database_name, command)
```

## <a name="add-a-new-principal-for-database"></a>Aggiungere una nuova entità per il databaseAdd a new principal for database
Aggiungere una nuova applicazione Azure AD come entità di amministrazione per il databaseAdd a new Azure AD application as admin principal for the database

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabasePrincipal
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
#Application ID
client_id_to_add = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg";
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster";
database_name = "mykustodatabase";
role = "Admin"
principle_name = "<database_principle_name>";
type_name = "App"
kustoManagementClient.databases.add_principals(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name,
                         value=[DatabasePrincipal(role=role, name=principle_name, type=type_name, app_id=client_id_to_add, tenant_name=tenant_id)])
```

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sui criteri di database e tabelle](https://docs.microsoft.com/azure/kusto/management/policies)
