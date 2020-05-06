---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f64892193eb6cfcce8f948b54e5557b5fa3d90ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878247"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

In `extensionBundle`sono disponibili le proprietà seguenti:

| Proprietà | Descrizione |
| -------- | ----------- |
| id | Spazio dei nomi per i bundle di estensioni di funzioni Microsoft Azure. |
| version | Versione del bundle da installare. Il runtime di funzioni sceglie sempre la versione massima consentita definita dall'intervallo o dall'intervallo di versioni. Il valore della versione precedente consente a tutte le versioni del bundle da 1.0.0 fino a, ma non incluso 2.0.0. Per ulteriori informazioni, vedere la [notazione intervallo per specificare gli intervalli di versione](/nuget/reference/package-versioning#version-ranges). |

Le versioni del bundle vengono incrementate come pacchetti nella modifica del bundle. Le modifiche alla versione principale si verificano quando i pacchetti nel bundle vengono incrementati di una versione principale. Le modifiche alla versione principale del bundle coincidono in genere con una modifica nella versione principale del runtime di funzioni.  

Il set corrente di estensioni installate dal bundle predefinito viene enumerato in questo [file Extensions. JSON](https://github.com/Azure/azure-functions-extension-bundles/blob/dev/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
