---
title: "Guida introduttiva: Creare un database e un cluster di Esplora dati di Azure usando l'interfaccia della riga di comando di Azure"
description: Informazioni su come creare un database e un cluster di Esplora dati di Azure tramite l'interfaccia della riga di comando di Azure
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/25/2019
ms.openlocfilehash: b1cc7d2966572da23a64e4555a0e94b440efa005
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59043973"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-azure-cli"></a>Creare un database e un cluster di Esplora dati di Azure usando l'interfaccia della riga di comando di Azure

> [!div class="op_single_selector"]
> * [Portale](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>

Esplora dati di Azure è un servizio di analisi dei dati veloce e completamente gestito per l'analisi in tempo reale di volumi elevati di dati in streaming provenienti da applicazioni, siti Web, dispositivi IoT e altro ancora. Per usare Esplora dati di Azure, è necessario prima creare un cluster e quindi uno o più database al suo interno. Quindi si inseriscono (caricano) i dati in un database per poter eseguire query. In questo argomento di avvio rapido vengono creati un cluster e un database usando l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva è necessaria una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando di Azure in locale, per questa guida introduttiva è necessaria l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per controllare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

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
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
    ```

   |**Impostazione** | **Valore consigliato** | **Descrizione campo**|
   |---|---|---|
   | cluster-name | *azureclitest* | Nome del cluster in cui verrà creato il database.|
   | name | *clidatabase* | Nome del database.|
   | resource-group | *testrg* | Il nome del gruppo di risorse in cui verrà creato il cluster. |
   | soft-delete-period | *3650:00:00:00* | Periodo di tempo in cui i dati verranno mantenuti disponibili in modo che sia possibile eseguire una query. |
   | hot-cache-period | *3650:00:00:00* | Periodo di tempo in cui i dati verranno conservati nella cache. |

1. Eseguire il comando seguente per vedere il database creato:

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

A questo punto sono disponibili un cluster e un database.

## <a name="clean-up-resources"></a>Pulire le risorse

* Se si prevede di seguire altre guide introduttive ed esercitazioni, non eliminare le risorse create.
* Per pulire le risorse, eliminare il cluster. Quando si elimina un cluster, vengono eliminati anche tutti i database al suo interno. Usare il comando seguente per eliminare il cluster:

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Guida introduttiva: Inserire dati usando la libreria di Esplora dati di Azure per Python](python-ingest-data.md)
