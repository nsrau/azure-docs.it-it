---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: 4ce048cafa4c20e3bbdbd8ecf1669748531ee122
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129090"
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

| Proprietà | DESCRIZIONE |
| -------- | ----------- |
| id | Spazio dei nomi per i bundle di estensioni di funzioni Microsoft Azure. |
| version | Versione del bundle da installare. Il runtime di funzioni sceglie sempre la versione massima consentita definita dall'intervallo o dall'intervallo di versioni. Il valore della versione precedente consente a tutte le versioni del bundle da 1.0.0 fino a, ma non incluso 2.0.0. Per ulteriori informazioni, vedere la [notazione intervallo per specificare gli intervalli di versione](/nuget/reference/package-versioning#version-ranges-and-wildcards). |

Le versioni del bundle vengono incrementate come pacchetti nella modifica del bundle. Le modifiche alla versione principale si verificano quando i pacchetti nel bundle vengono incrementati di una versione principale. Le modifiche alla versione principale del bundle coincidono in genere con una modifica nella versione principale del runtime di funzioni.  

Il set corrente di estensioni installate dal bundle predefinito viene enumerato in questo [file Extensions. JSON](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
