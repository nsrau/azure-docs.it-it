---
title: Recuperare il nome host, le porte e le chiavi - Azure Cache for Redis - Interfaccia della riga di comando di Azure
description: Questo esempio di codice dell'interfaccia della riga di comando di Azure descrive come recuperare il nome host, le porte e le chiavi per un'istanza di Azure Cache for Redis.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: d3e8e359a97c091e025049ac8a978e1beca1d759
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184221"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Recuperare il nome host, le porte e le chiavi per Cache Redis di Azure

In questo scenario si apprenderà come recuperare il nome host, le porte e le chiavi usati per connettersi a un'istanza di Cache Redis di Azure.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per recuperare il nome host, le chiavi e le porte di un'istanza di Cache Redis di Azure. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az redis show](/cli/azure/redis) | Recupera i dettagli di un'istanza di Cache Redis di Azure. |
| [az redis list-keys](/cli/azure/redis) | Recupera le chiavi di accesso per un'istanza di Cache Redis di Azure. |


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di Cache Redis di Azure sono disponibili nella [documentazione di Cache Redis di Azure](../cli-samples.md).