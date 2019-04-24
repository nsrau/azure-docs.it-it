---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Ottenere i dettagli di un'istanza di Cache Redis di Azure | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Ottenere i dettagli di un'istanza di Cache Redis di Azure
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
tags: azure-service-management
ms.assetid: 155924e6-00d5-4a8c-ba99-5189f300464a
ms.service: cache
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: 8b4c0311f935fa141170206f51e01b90eb4b98ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60240305"
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
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | Recupera i dettagli di un'istanza di Cache Redis di Azure. |


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di Cache Redis di Azure sono disponibili nella [documentazione di Cache Redis di Azure](../cli-samples.md).
