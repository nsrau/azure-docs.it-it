---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Eliminare un'istanza di Cache Redis di Azure
description: Esempio di script dell'interfaccia della riga di comando di Azure - Eliminare un'istanza di Cache Redis di Azure
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: a2884fd326b6091680b8d81a905f3ee3320a2740
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121212"
---
# <a name="delete-an-azure-cache-for-redis"></a>Eliminare un’istanza di Cache Redis di Azure

In questo scenario viene illustrato come eliminare un'istanza di Cache Redis di Azure.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per eliminare un'istanza della Cache Redis di Azure. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [eliminazione di redis az](https://docs.microsoft.com/cli/azure/redis) | Eliminare un'istanza di Cache Redis di Azure. |


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di Cache Redis di Azure sono disponibili nella [documentazione di Cache Redis di Azure](../cli-samples.md).
