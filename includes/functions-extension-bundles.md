---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639107"
---
Il modo più semplice per installare le estensioni di binding è consentire [aggregazioni di estensione](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Quando si abilitano i bundle, viene installato automaticamente un set predefinito di pacchetti di estensioni.

Per abilitare i bundle di estensioni, aprire il file host.json e aggiornarne il contenuto in modo che corrisponda al codice seguente:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```