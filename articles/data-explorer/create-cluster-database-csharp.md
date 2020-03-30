---
title: 'Creare un cluster di Azure Data Explorer & database usando CCreate an Azure Data Explorer cluster & DB using C #'
description: Informazioni su come creare un database e un cluster di Esplora dati di Azure tramite C#
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 0c32d438ac8551f061343edb747e9fc035b498e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246409"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Creare un database e un cluster di Esplora dati di Azure tramite C#

> [!div class="op_single_selector"]
> * [Portale](create-cluster-database-portal.md)
> * [Cli](create-cluster-database-cli.md)
> * [Powershell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Modello di Azure Resource ManagerAzure Resource Manager template](create-cluster-database-resource-manager.md)

Esplora dati di Azure è un servizio di analisi dei dati veloce e completamente gestito per l'analisi in tempo reale di volumi elevati di dati in streaming provenienti da applicazioni, siti Web, dispositivi IoT e altro ancora. Per usare Esplora dati di Azure, è necessario prima creare un cluster e quindi uno o più database al suo interno. Quindi si inseriscono (caricano) i dati in un database per poter eseguire query. In questo articolo, si crea un cluster e un database utilizzando C .

## <a name="prerequisites"></a>Prerequisiti

* Se Visual Studio 2019 non è installato, è possibile scaricare e usare l'edizione **gratuita** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Assicurarsi di abilitare lo sviluppo di Azure durante l'installazione di Visual Studio.Make sure that you enable **Azure development** during the Visual Studio setup.
* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

## <a name="authentication"></a>Authentication
Per eseguire gli esempi in questo articolo, sono necessarie un'applicazione Azure AD e un'entità servizio in grado di accedere alle risorse. Selezionare [creare un'applicazione Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) per creare un'applicazione Azure AD gratuita e aggiungere l'assegnazione di ruolo nell'ambito della sottoscrizione. Viene inoltre illustrato come `Directory (tenant) ID` `Application ID`ottenere `Client Secret`il , , e .

## <a name="create-the-azure-data-explorer-cluster"></a>Creare il cluster di Esplora dati di Azure

1. Creare il cluster usando il codice seguente:

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
    var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
    var credential = new ClientCredential(clientId, clientSecret);
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = subscriptionId
    };

    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var cluster = new Cluster(location, sku);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```

   |**Impostazione** | **Valore consigliato** | **Descrizione campo**|
   |---|---|---|
   | clusterName | *mykustocluster* | Nome del cluster.|
   | SkuName (nome sku) | *Standard_D13_v2* | SKU usato per il cluster. |
   | Livello | *Standard* | Livello SKU. |
   | capacità | *Numero* | Numero di istanze del cluster. |
   | resourceGroupName | *testrg* | Il nome del gruppo di risorse in cui verrà creato il cluster. |

    > [!NOTE]
    > **Creare un cluster** è un'operazione a esecuzione prolungata, pertanto è consigliabile usare CreateOrUpdateAsync anziché CreateOrUpdate.Create a cluster is a long running operation, so's highly recommended to use CreateOrUpdateAsync, instead of CreateOrUpdate. 

1. Per verificare se il cluster è stato creato correttamente, eseguire il comando seguente:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Se il risultato contiene `ProvisioningState` con il valore `Succeeded`, il cluster è stato creato correttamente.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Creare il database nel cluster di Esplora dati di Azure

1. Creare il database usando il codice seguente:

    ```csharp
    var hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    var softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    var databaseName = "mykustodatabase";
    var database = new ReadWriteDatabase(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);

    await kustoManagementClient.Databases.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, database);
    ```

        [!NOTE]
        If you are using C# version 2.0.0 or below, use Database instead of ReadWriteDatabase.

   |**Impostazione** | **Valore consigliato** | **Descrizione campo**|
   |---|---|---|
   | clusterName | *mykustocluster* | Nome del cluster in cui verrà creato il database.|
   | databaseName | *mykustodatabase* | Nome del database.|
   | resourceGroupName | *testrg* | Il nome del gruppo di risorse in cui verrà creato il cluster. |
   | softDeletePeriod | *3650:00:00:00* | Periodo di tempo in cui i dati verranno mantenuti disponibili in modo che sia possibile eseguire una query. |
   | hotCachePeriod | *3650:00:00:00* | Periodo di tempo in cui i dati verranno conservati nella cache. |

2. Eseguire il comando seguente per vedere il database creato:

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName) as ReadWriteDatabase;
    ```

A questo punto sono disponibili un cluster e un database.

## <a name="clean-up-resources"></a>Pulire le risorse

* Se hai intenzione di seguire gli altri articoli, mantieni le risorse che hai creato.
* Per pulire le risorse, eliminare il cluster. Quando si elimina un cluster, vengono eliminati anche tutti i database al suo interno. Usare il comando seguente per eliminare il cluster:

    ```csharp
    kustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Inserire dati usando .NET Standard SDK di Esplora dati di Azure (anteprima)](net-standard-ingest-data.md)
