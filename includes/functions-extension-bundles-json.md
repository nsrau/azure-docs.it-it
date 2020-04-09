---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f64892193eb6cfcce8f948b54e5557b5fa3d90ab
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
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

Le seguenti proprietà `extensionBundle`sono disponibili in:

| Proprietà | Descrizione |
| -------- | ----------- |
| id | Lo spazio dei nomi per i pacchetti di estensione di Funzioni di Microsoft Azure.The namespace for Microsoft Azure Functions extension bundles. |
| version | Versione del pacchetto da installare. Il runtime di Funzioni seleziona sempre la versione massima consentita definita dall'intervallo o dall'intervallo di versione. Il valore di versione precedente consente tutte le versioni del bundle dalla 1.0.0 fino alla versione 2.0.0. Per ulteriori informazioni, vedere la [notazione di intervallo per la specifica degli intervalli di versione](/nuget/reference/package-versioning#version-ranges). |

Le versioni del bundle aumentano come pacchetti nel bundle cambiano. Le modifiche alla versione principale si verificano quando i pacchetti nell'incremento del bundle di una versione principale. Le modifiche principali apportate alla versione nel bundle coincidono in genere con una modifica nella versione principale del runtime di Funzioni.  

Il set corrente di estensioni installate dal pacchetto predefinito viene enumerato in questo [file extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/dev/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
