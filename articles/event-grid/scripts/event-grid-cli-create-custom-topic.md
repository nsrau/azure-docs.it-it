---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare un argomento personalizzato | Microsoft Docs
description: Questo articolo include un esempio di script dell'interfaccia della riga di comando di Azure che illustra come creare un argomento personalizzato di Griglia di eventi.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: 951008a2bf3919f69429ca15382334a9618b912f
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171313"
---
# <a name="create-event-grid-custom-topic-with-azure-cli"></a>Creare un argomento personalizzato di Griglia di eventi con l'interfaccia della riga di comando di Azure

Questo script crea un argomento personalizzato di Griglia di eventi.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/event-grid/create-custom-topic/create-custom-topic.sh "Create custom topic")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa il comando seguente per creare l'argomento personalizzato. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az eventgrid topic create](https://docs.microsoft.com/cli/azure/eventgrid/topic#az-eventgrid-topic-create) | Creare un argomento personalizzato di Griglia di eventi. |


## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle query sulle sottoscrizioni, vedere [Eseguire query sulle sottoscrizioni di Griglia di eventi](../query-event-subscriptions.md).
* Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).
