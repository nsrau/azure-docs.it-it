---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare una cache Redis di Azure Premium con clustering | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Creare una cache Redis di Azure Premium con clustering
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 07bcceae-2521-4fe3-b88f-ed833104ddd2
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 45b2a500751830f6ca19fe8450a7781cb0fcb618
ms.contentlocale: it-it
ms.lasthandoff: 09/09/2017

---

# <a name="create-a-premium-azure-redis-cache-with-clustering"></a>Creare una cache Redis di Azure Premium con clustering

In questo scenario viene illustrato come creare una cache Redis di Azure di livello Premium 6 GB con il clustering abilitato e due partizioni.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Cache Redis di Azure")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse e una cache Redis di livello Premium con il clustering abilitato. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az redis create](https://docs.microsoft.com/cli/azure/redis#az_redis_create) | Creare un'istanza di Cache Redis. |


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando della Cache Redis di Azure sono disponibili nella [documentazione della Cache Redis di Azure](../cli-samples.md).
