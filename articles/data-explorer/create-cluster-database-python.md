---
title: 'Guida introduttiva: Creare un database e un cluster di Esplora dati di Azure tramite Python'
description: Informazioni su come creare un database e un cluster di Esplora dati di Azure tramite Python
services: data-explorer
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/17/2019
ms.openlocfilehash: 4f87c5996ea323c26c32c1680ba6f627bf8f95c2
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287391"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Creare un database e un cluster di Esplora dati di Azure tramite Python

> [!div class="op_single_selector"]
> * [Portale](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Questo argomento di avvio rapido descrive come creare un database e un cluster di Esplora dati di Azure tramite Python.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva è necessaria una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="install-python-package"></a>Installa pacchetto Python

Per installare il pacchetto di Python per Esplora dati di Azure (Kusto), aprire un prompt dei comandi con un percorso contenente Python e quindi eseguire questo comando:

```
pip install azure-mgmt-kusto
```

## <a name="create-the-azure-data-explorer-cluster"></a>Creare il cluster di Esplora dati di Azure

1. Creare il cluster tramite il comando seguente:

    

   |**Impostazione** | **Valore consigliato** | **Descrizione campo**|
   |---|---|---|
   | cluster_name | *mykustocluster* | Nome del cluster.|
   | sku | *D13_v2* | SKU usato per il cluster. |
   | resource_group_name | *testrg* | Il nome del gruppo di risorse in cui verrà creato il cluster. |

    Sono disponibili altri parametri facoltativi che è possibile usare, ad esempio la capacità del cluster.
    
    Impostare "credentials" sulle proprie credenziali. Per altre informazioni, vedere https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python.

2. Per verificare se il cluster è stato creato correttamente, eseguire il comando seguente:

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

2. Eseguire il comando seguente per vedere il database creato:

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

A questo punto sono disponibili un cluster e un database.

## <a name="clean-up-resources"></a>Pulire le risorse

* Se si prevede di seguire altre guide introduttive ed esercitazioni, non eliminare le risorse create.
* Per pulire le risorse, eliminare il cluster. Quando si elimina un cluster, vengono eliminati anche tutti i database al suo interno. Usare il comando seguente per eliminare il cluster:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Guida introduttiva: Inserire dati usando la libreria di Esplora dati di Azure per Python](python-ingest-data.md)
