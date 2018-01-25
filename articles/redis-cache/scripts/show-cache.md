---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Ottenere i dettagli di un'istanza di Cache Redis di Azure | Documentazione Microsoft
description: Esempio di script dell'interfaccia della riga di comando di Azure - Ottenere i dettagli di un'istanza di Cache Redis di Azure
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: 155924e6-00d5-4a8c-ba99-5189f300464a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 21db3c9d02c275ec8827062de5135631ed900da8
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="get-details-of-an-azure-redis-cache"></a>Ottiene i dettagli di una Cache Redis di Azure

In questo scenario, informazioni su come recuperare i dettagli di un'istanza di Cache Redis di Azure, incluso il relativo stato di provisioning.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Redis Cache")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per recuperare i dettagli di un'istanza di Cache Redis di Azure. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Recupera i dettagli di un'istanza di Cache Redis di Azure. |


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando della Cache Redis di Azure sono disponibili nella [documentazione della Cache Redis di Azure](../cli-samples.md).