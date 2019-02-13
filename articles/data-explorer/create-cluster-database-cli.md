---
title: Creare un database e un cluster di Esplora dati di Azure tramite l'interfaccia della riga di comando
description: Questo articolo descrive come creare un database e un cluster di Esplora dati di Azure tramite l'interfaccia della riga di comando di Azure
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: howto
ms.date: 1/31/2019
ms.openlocfilehash: a4c9156ef80f05e247b1cfef0acd56b601a2db65
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812685"
---
# <a name="create-an-azure-data-explorer-cluster-and-a-database-using-cli"></a>Creare un database e un cluster di Esplora dati di Azure tramite l'interfaccia della riga di comando

Questo articolo descrive come creare un database e un cluster di Esplora dati di Azure tramite l'interfaccia della riga di comando di Azure.

## <a name="whats-the-difference-between-the-management-plane-and-data-plane-apis"></a>Differenze tra le API del piano di gestione e le API del piano dati

Esistono due diverse librerie di API, le API di gestione e quelle del piano dati.
Le API di gestione vengono usate per gestire le risorse, ad esempio creare un cluster, creare un database, eliminare una connessione dati, modificare il numero delle istanze e così via. Le API del piano dati vengono usate per interagire con i dati, eseguire query, inserire dati e così via.

## <a name="configure-the-cli-parameters"></a>Configurare i parametri dell'interfaccia della riga di comando

Accedere all'account

```Bash
az login
```

Impostare la sottoscrizione in cui si vuole creare il cluster.

```Bash
az account set --subscription "your_subscription"
```

## <a name="create-the-azure-data-explorer-cluster"></a>Creare il cluster di Esplora dati di Azure

Creare il cluster tramite il comando seguente.

```Bash
az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
```

Specificare i valori seguenti

   |**Impostazione** | **Valore consigliato** | **Descrizione campo**|
   |---|---|---|
   | name | *azureclitest* | Nome del cluster.|
   | sku | *D13_v2* | SKU usato per il cluster. |
   | resource-group | *testrg* | Nome del gruppo di risorse in cui verrà creato il cluster. |

Se necessario, è possibile usare altri parametri facoltativi, ad esempio la capacità del cluster e così via.

Per verificare se il cluster è stato creato correttamente, è possibile eseguire

```Bash
az kusto cluster show --name azureclitest --resource-group testrg
```

Se il risultato contiene "provisioningState" con valore "Succeeded", significa che il cluster è stato creato correttamente.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Creare il database nel cluster di Esplora dati di Azure

Creare il database tramite il comando seguente.

```Bash
az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
```

Specificare i valori seguenti

   |**Impostazione** | **Valore consigliato** | **Descrizione campo**|
   |---|---|---|
   | cluster-name | *azureclitest* | Nome del cluster in cui deve essere creato il database.|
   | name | *clidatabase* | Nome del database.|
   | resource-group | *testrg* | Nome del gruppo di risorse in cui verrà creato il cluster. |
   | soft-delete-period | *3650:00:00:00* | Periodo di tempo in cui i dati devono essere mantenuti, in modo che sia possibile eseguire una query. |
   | hot-cache-period | *3650:00:00:00* | Periodo di tempo in cui i dati devono essere conservati nella cache. |

È possibile visualizzare il database creato eseguendo

```Bash
az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
```

A questo punto sono ora disponibili un cluster e un database.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di seguire altre guide introduttive ed esercitazioni, non eliminare le risorse create.

Quando si elimina un cluster, vengono eliminati anche tutti i database al suo interno. Per eliminare il cluster, usare il comando seguente.

```Bash
az kusto cluster delete --name azureclitest --resource-group testrg
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Guida introduttiva: Inserire dati dall'hub eventi in Esplora dati di Azure](ingest-data-event-hub.md)
