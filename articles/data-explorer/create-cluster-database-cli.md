---
title: Creare un database e un cluster di Esplora dati di Azure usando l'interfaccia della riga di comando di Azure
description: Informazioni su come creare un database e un cluster di Esplora dati di Azure tramite l'interfaccia della riga di comando di Azure
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: e771def95db00b5de8c27011641a628560952970
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494793"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-azure-cli"></a>Creare un database e un cluster di Esplora dati di Azure usando l'interfaccia della riga di comando di Azure

> [!div class="op_single_selector"]
> * [Portale](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>

Esplora dati di Azure è un servizio di analisi dei dati veloce e completamente gestito per l'analisi in tempo reale di volumi elevati di dati in streaming provenienti da applicazioni, siti Web, dispositivi IoT e altro ancora. Per usare Esplora dati di Azure, è necessario prima creare un cluster e quindi uno o più database al suo interno. Quindi si inseriscono (caricano) i dati in un database per poter eseguire query. In questo articolo, si crea un cluster e un database utilizzando CLI di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo articolo, necessaria una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare il comando di Azure in locale, questo articolo richiede la CLI di Azure versione 2.0.4 o versioni successive. Eseguire `az --version` per controllare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-the-cli-parameters"></a>Configurare i parametri dell'interfaccia della riga di comando

I passaggi seguenti non sono necessari se si eseguono i comandi in Azure Cloud Shell. Se si esegue l'interfaccia della riga di comando in locale, seguire questa procedura per accedere ad Azure e impostare la sottoscrizione corrente:

1. Eseguire questo comando per accedere ad Azure:

    ```azurecli-interactive
    az login
    ```

1. Impostare la sottoscrizione in cui si vuole creare il cluster. Sostituire `MyAzureSub` con il nome della sottoscrizione di Azure che si vuole usare:

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Creare il cluster di Esplora dati di Azure

1. Creare il cluster tramite il comando seguente:

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**Impostazione** | **Valore consigliato** | **Descrizione campo**|
   |---|---|---|
   | name | *azureclitest* | Nome del cluster.|
   | sku | *D13_v2* | SKU usato per il cluster. |
   | resource-group | *testrg* | Il nome del gruppo di risorse in cui verrà creato il cluster. |

    Sono disponibili altri parametri facoltativi che è possibile usare, ad esempio la capacità del cluster.

1. Per verificare se il cluster è stato creato correttamente, eseguire il comando seguente:

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Se il risultato contiene `provisioningState` con il valore `Succeeded`, il cluster è stato creato correttamente.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Creare il database nel cluster di Esplora dati di Azure

1. Creare il database tramite il comando seguente:

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period P365D --hot-cache-period P31D
    ```

   |**Impostazione** | **Valore consigliato** | **Descrizione campo**|
   |---|---|---|
   | cluster-name | *azureclitest* | Nome del cluster in cui verrà creato il database.|
   | name | *clidatabase* | Nome del database.|
   | resource-group | *testrg* | Il nome del gruppo di risorse in cui verrà creato il cluster. |
   | soft-delete-period | *P365D* | Indica il periodo di tempo in cui i dati verranno mantenuti disponibili in modo che sia possibile eseguire una query. Per altre informazioni, vedere [Criteri di conservazione](/azure/kusto/concepts/retentionpolicy). |
   | hot-cache-period | *P31D* | Indica il periodo di tempo in cui i dati verranno conservati nella cache. Per altre informazioni, vedere [Criteri della cache](/azure/kusto/concepts/cachepolicy). |

1. Eseguire il comando seguente per vedere il database creato:

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

A questo punto sono disponibili un cluster e un database.

## <a name="clean-up-resources"></a>Pulire le risorse

* Se si prevede di eseguire gli altri articoli, mantenere le risorse create.
* Per pulire le risorse, eliminare il cluster. Quando si elimina un cluster, vengono eliminati anche tutti i database al suo interno. Usare il comando seguente per eliminare il cluster:

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Inserire dati usando la libreria di Esplora dati di Azure per Python](python-ingest-data.md)
