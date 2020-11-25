---
title: Creare un blocco di risorse per un database e un grafo Gremlin per Azure Cosmos DB
description: Creare un blocco di risorse per un database e un grafo Gremlin per Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 492246b5dfb19664ea54ce8b5462c7d77f8d951b
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94562713"
---
# <a name="create-a-resource-lock-for-azure-cosmos-gremlin-api-database-and-graph-using-azure-cli"></a>Creare un blocco di risorse per un database e un grafo dell'API Gremlin di Azure Cosmos tramite l'interfaccia della riga di comando di Azure
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede la versione 2.9.1 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

> [!IMPORTANT]
> I blocchi delle risorse non funzionano per le modifiche apportate dagli utenti che si connettono tramite Gremlin SDK o il portale di Azure, a meno che l'account Cosmos DB non venga inizialmente bloccato con la proprietà `disableKeyBasedMetadataWriteAccess` abilitata. Per altre informazioni su come abilitare questa proprietà, vedere [Impedire modifiche dagli SDK](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/lock.sh "Create a resource lock for an Azure Cosmos DB Gremlin API database and graph.")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az lock create](/cli/azure/lock#az-lock-create) | Crea un blocco. |
| [az lock list](/cli/azure/lock#az-lock-list) | Elenca le informazioni del blocco. |
| [az lock show](/cli/azure/lock#az-lock-show) | Mostra le proprietà di un blocco. |
| [az lock delete](/cli/azure/lock#az-lock-delete) | Elimina un blocco. |

## <a name="next-steps"></a>Passaggi successivi

- [Bloccare le risorse per impedire modifiche impreviste](../../../../azure-resource-manager/management/lock-resources.md)

- [Documentazione sull'interfaccia della riga di comando di Azure Cosmos DB](/cli/azure/cosmosdb).

- [Repository GitHub per l'interfaccia della riga di comando di Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
