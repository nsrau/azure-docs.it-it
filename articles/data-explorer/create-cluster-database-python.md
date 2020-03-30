---
title: Creare un cluster di Azure Data Explorer & db usando PythonCreate an Azure Data Explorer cluster & DB using Python
description: Informazioni su come creare un database e un cluster di Esplora dati di Azure usando Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 8425058c9f6ac5b90c37a99f749a810672b406fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560508"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Creare un database e un cluster di Esplora dati di Azure tramite Python

> [!div class="op_single_selector"]
> * [Portale](create-cluster-database-portal.md)
> * [Cli](create-cluster-database-cli.md)
> * [Powershell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Modello ARM](create-cluster-database-resource-manager.md)

In this article, you create an Azure Data Explorer cluster and database by using Python. Esplora dati di Azure è un servizio di analisi dei dati veloce e completamente gestito per l'analisi in tempo reale di volumi elevati di dati in streaming provenienti da applicazioni, siti Web, dispositivi IoT e altro ancora. Per usare Azure Data Explorer, creare innanzitutto un cluster e uno o più database in tale cluster. Quindi inserire, o caricare, i dati in un database in modo che sia possibile eseguire query su di esso.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4+](https://www.python.org/downloads/).

* [Un'applicazione Azure AD e un'entità servizio che possono accedere alle risorse.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) Ottenere valori `Directory (tenant) ID` `Application ID`per `Client Secret`, , e .

## <a name="install-python-package"></a>Installa pacchetto Python

Per installare il pacchetto di Python per Esplora dati di Azure (Kusto), aprire un prompt dei comandi con un percorso contenente Python. Eseguire questo comando:

```
pip install azure-common
pip install azure-mgmt-kusto
```
## <a name="authentication"></a>Authentication
Per eseguire gli esempi in questo articolo, sono necessarie un'applicazione Azure AD e un'entità servizio in grado di accedere alle risorse. Selezionare [creare un'applicazione Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) per creare un'applicazione Azure AD gratuita e aggiungere l'assegnazione di ruolo nell'ambito della sottoscrizione. Viene inoltre illustrato come `Directory (tenant) ID` `Application ID`ottenere `Client Secret`il , , e .

## <a name="create-the-azure-data-explorer-cluster"></a>Creare il cluster di Esplora dati di Azure

1. Creare il cluster tramite il comando seguente:

    ```Python
    from azure.mgmt.kusto import KustoManagementClient
    from azure.mgmt.kusto.models import Cluster, AzureSku
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

    location = 'Central US'
    sku_name = 'Standard_D13_v2'
    capacity = 5
    tier = "Standard"
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    cluster = Cluster(location=location, sku=AzureSku(name=sku_name, capacity=capacity, tier=tier))
    
    kustoManagementClient = KustoManagementClient(credentials, subscription_id)
    
    cluster_operations = kustoManagementClient.clusters
    
    poller = cluster_operations.create_or_update(resource_group_name, cluster_name, cluster)
    ```

   |**Impostazione** | **Valore consigliato** | **Descrizione campo**|
   |---|---|---|
   | cluster_name | *mykustocluster* | Nome del cluster.|
   | sku_name | *Standard_D13_v2* | SKU usato per il cluster. |
   | Livello | *Standard* | Livello SKU. |
   | capacità | *Numero* | Numero di istanze del cluster. |
   | resource_group_name | *testrg* | Il nome del gruppo di risorse in cui verrà creato il cluster. |

    > [!NOTE]
    > **Creare un cluster** è un'operazione a esecuzione prolungata. Metodo **create_or_update** restituisce un'istanza di LROPoller, vedere [LROPoller classe](/python/api/msrest/msrest.polling.lropoller?view=azure-python) per ottenere ulteriori informazioni.

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
    _database = ReadWriteDatabase(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    #Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
    poller =database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

        [!NOTE]
        If you are using Python version 0.4.0 or below, use Database instead of ReadWriteDatabase.

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

* Se hai intenzione di seguire gli altri articoli, mantieni le risorse che hai creato.
* Per pulire le risorse, eliminare il cluster. Quando si elimina un cluster, vengono eliminati anche tutti i database al suo interno. Usare il comando seguente per eliminare il cluster:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire l'inserimento di dati usando la libreria Python di Azure Data ExplorerIngest data using the Azure Data Explorer Python library](python-ingest-data.md)
