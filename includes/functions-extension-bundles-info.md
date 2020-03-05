---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 1fc37c6f93fba34944caa7a91c2a89ce5dcdc398
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201022"
---
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
> [!TIP]
> Durante l'avvio, l'host scarica e installa l'[estensione di binding di archiviazione](../articles/azure-functions/functions-bindings-storage-queue.md#functions-2x-and-higher) e altre estensioni di binding Microsoft. Questa installazione si verifica perché le estensioni di binding vengono abilitate per impostazione predefinita nel file *host.json* con le proprietà seguenti:
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> Se si verificano errori relativi alle estensioni di binding, controllare che le proprietà sopra indicate siano presenti in *host.json*.
::: zone-end  