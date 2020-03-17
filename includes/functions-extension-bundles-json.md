---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f47a543143c949715fe2a49adccf074759a346fa
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79381985"
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

In `extensionBundle` sono disponibili le proprietà seguenti:

| Proprietà | Descrizione |
| -------- | ----------- |
| id | Spazio dei nomi per i bundle di estensioni di Funzioni di Azure Microsoft. |
| version | Versione del bundle da installare. Il runtime di Funzioni sceglie sempre la versione massima consentita definita dall'intervallo o dall'intervallo di versioni. Il valore version precedente consente tutte le versioni del bundle a partire dalla 1.0.0 fino alla 2.0.0 esclusa. Per altre informazioni, vedere la [notazione degli intervalli per specificare gli intervalli di versione](/nuget/reference/package-versioning#version-ranges). |

Le versioni del bundle vengono incrementate come pacchetti nella modifica del bundle. Si parla di modifiche alla versione principale quando i pacchetti nel bundle vengono incrementati di una versione principale. Le modifiche alla versione principale del bundle coincidono in genere con una modifica nella versione principale del runtime di Funzioni.  

Il set corrente di estensioni installate dal bundle predefinito viene enumerato in questo [file extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
