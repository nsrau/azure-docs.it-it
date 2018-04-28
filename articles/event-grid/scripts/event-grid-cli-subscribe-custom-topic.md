---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Sottoscrivere un argomento personalizzato | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Sottoscrivere un argomento personalizzato
services: event-grid
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2018
ms.author: tomfitz
ms.openlocfilehash: 0c0b4c77815efc4ae3c80a8602222122254f7d88
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="subscribe-to-events-for-a-custom-topic-with-azure-cli"></a>Sottoscrivere eventi per un argomento personalizzato con l'interfaccia della riga di comando di Azure

Lo script crea una sottoscrizione di Griglia di eventi per gli eventi relativi a un argomento personalizzato.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-custom-topic/subscribe-to-custom-topic.sh "Subscribe to custom topic")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa il comando seguente per creare una sottoscrizione a eventi. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | Creare una sottoscrizione di Griglia di eventi. |


## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle query sulle sottoscrizioni, vedere [Eseguire query sulle sottoscrizioni di Griglia di eventi](../query-event-subscriptions.md).
* Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).
