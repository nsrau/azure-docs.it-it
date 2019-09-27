---
title: Creare un database e un cluster di Esplora dati di Azure tramite Python
description: Informazioni su come creare un database e un cluster di Esplora dati di Azure usando Python.
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 2fc2b847c18cecbcea3c137312b18bb274398cc6
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326636"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Creare un database e un cluster di Esplora dati di Azure tramite Python

> [!div class="op_single_selector"]
> * [Portale](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Modello ARM](create-cluster-database-resource-manager.md)

Esplora dati di Azure è un servizio di analisi dei dati veloce e completamente gestito per l'analisi in tempo reale di volumi elevati di dati in streaming provenienti da applicazioni, siti Web, dispositivi IoT e altro ancora. Per usare Esplora dati di Azure, è necessario prima creare un cluster e quindi uno o più database al suo interno. Quindi si inseriscono (caricano) i dati in un database per poter eseguire query. In questo articolo vengono creati un cluster e un database usando Python.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="install-python-package"></a>Installa pacchetto Python

Per installare il pacchetto di Python per Esplora dati di Azure (Kusto), aprire un prompt dei comandi con un percorso contenente Python. Eseguire questo comando:

```
pip install azure-mgmt-kusto
pip install adal
pip install msrestazure
```

## <a name="create-the-azure-data-explorer-cluster"></a>Creare il cluster di Esplora dati di Azure

1. Creare il cluster tramite il comando seguente:

    ```Python
    from azure.mgmt.kusto.kusto_management_client import KustoManagementClient
    from azure.mgmt.kusto.models import Cluster, AzureSku
    from adal import AuthenticationContext
    from msrestazure.azure_active_directory import AdalAuthentication

    tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    client_secret = "xxxxxxxxxxxxxx"
    subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    context = AuthenticationContext('https://login.microsoftonline.com/{}'.format(tenant_id))
    credentials = AdalAuthentication(context.acquire_token_with_client_credentials,
                                         resource="https://management.core.windows.net/",
                                         client_id=client_id,
                                         client_secret=client_secret)

    location = 'Central US'
    sku = 'D13_v2'
    capacity = 5
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    cluster = Cluster(location=location, sku=AzureSku(name=sku, capacity=capacity))
    
    kustoManagementClient = KustoManagementClient(credentials, subscription_id)
    
    cluster_operations = kustoManagementClient.clusters
    
    cluster_operations.create_or_update(resource_group_name, cluster_name, cluster)
    ```

   |**Impostazione** | **Valore consigliato** | **Descrizione campo**|
   |---|---|---|
   | cluster_name | *mykustocluster* | Nome del cluster.|
   | sku | *D13_v2* | SKU usato per il cluster. |
   | resource_group_name | *testrg* | Il nome del gruppo di risorse in cui verrà creato il cluster. |

    Sono disponibili altri parametri facoltativi che è possibile usare, ad esempio la capacità del cluster.
    
1. Impostare le [*proprie credenziali*](/azure/python/python-sdk-azure-authenticate)

1. Per verificare se il cluster è stato creato correttamente, eseguire il comando seguente:

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

Se il risultato contiene `provisioningState` con il valore `Succeeded`, il cluster è stato creato correttamente.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Creare il database nel cluster di Esplora dati di Azure

1. Creare il database tramite il comando seguente:

    ```Python
    from azure.mgmt.kusto.models import Database
    from datetime import timedelta
    
    softDeletePeriod = timedelta(days=3650)
    hotCachePeriod = timedelta(days=3650)
    databaseName="mykustodatabase"
    
    database_operations = kusto_management_client.databases 
    _database = Database(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

   |**Impostazione** | **Valore consigliato** | **Descrizione campo**|
   |---|---|---|
   | cluster_name | *mykustocluster* | Nome del cluster in cui verrà creato il database.|
   | database_name | *mykustodatabase* | Nome del database.|
   | resource_group_name | *testrg* | Il nome del gruppo di risorse in cui verrà creato il cluster. |
   | soft_delete_period | *3650 giorni, 0:00:00* | Periodo di tempo in cui i dati verranno mantenuti disponibili in modo che sia possibile eseguire una query. |
   | hot_cache_period | *3650 giorni, 0:00:00* | Periodo di tempo in cui i dati verranno conservati nella cache. |

1. Eseguire il comando seguente per vedere il database creato:

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

A questo punto sono disponibili un cluster e un database.

## <a name="clean-up-resources"></a>Pulire le risorse

* Se si prevede di seguire gli altri articoli, è necessario salvare le risorse create.
* Per pulire le risorse, eliminare il cluster. Quando si elimina un cluster, vengono eliminati anche tutti i database al suo interno. Usare il comando seguente per eliminare il cluster:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Inserire dati usando la libreria di Esplora dati di Azure per Python](python-ingest-data.md)
