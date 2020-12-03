---
title: Ottenere i dettagli di un'istanza di Azure Cache for Redis - Interfaccia della riga di comando di Azure
description: Questo esempio di codice dell'interfaccia della riga di comando di Azure mostra come recuperare i dettagli di un'istanza di Azure Cache for Redis, incluso lo stato di provisioning.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8901847a1c214b257243c4da0a74897061409bee
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184247"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Ottenere i dettagli di un'istanza di Cache Redis di Azure

In questo scenario vengono fornite informazioni su come recuperare i dettagli di un'istanza di Cache Redis di Azure, incluso lo stato di provisioning.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per recuperare i dettagli di un'istanza di Cache Redis di Azure. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az redis show](/cli/azure/redis) | Recupera i dettagli di un'istanza di Cache Redis di Azure. |


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di Cache Redis di Azure sono disponibili nella [documentazione di Cache Redis di Azure](../cli-samples.md).