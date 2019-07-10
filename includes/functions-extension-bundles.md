---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: 8110d0a9d574c6691322df2162ca877b031cbc59
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442257"
---
Il modo più semplice per installare le estensioni di binding è consentire [aggregazioni di estensione](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Con le aggregazioni abilitate, viene installato automaticamente un set predefinito di estensioni per i pacchetti.

Per abilitare le aggregazioni di estensione, aprire il file *host.json* e aggiornare il contenuto in modo che corrisponda al codice seguente:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```