---
title: Linguaggi supportati in Funzioni di Azure
description: Informazioni sui linguaggi supportati a livello generale, su quelli sperimentali e su quelli in anteprima.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 029ea753439dca3093bf214a5adfb6d58a1fe567
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74942257"
---
# <a name="supported-languages-in-azure-functions"></a>Linguaggi supportati in Funzioni di Azure

Questo articolo illustra i livelli di supporto offerti per i linguaggi disponibili in Funzioni di Azure.

## <a name="levels-of-support"></a>Livelli di supporto

Sono disponibili tre livelli di supporto:

* **Disponibile a livello generale** - Il linguaggio è completamente supportato e approvato per l'uso in produzione.
* **Anteprima** - Il linguaggio non è ancora supportato ma si prevede che in futuro diventi disponibile a livello generale.
* **Sperimentale** - Il linguaggio non è supportato e potrebbe venire abbandonato in futuro. Non viene data garanzia che diventi disponibile in anteprima o a livello generale.

## <a name="languages-by-runtime-version"></a>Lingue per versione runtime 

Sono disponibili [tre versioni del runtime di Funzioni di Azure.Three](functions-versions.md) versions of the Azure Functions runtime are available. Nella tabella seguente sono indicati i linguaggi supportati in ogni versione del runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Linguaggi sperimentali

I linguaggi sperimentali supportati nella versione 1.x non garantiscono una corretta scalabilità e non supportano tutte le associazioni.

Non usare le funzionalità sperimentali perché non è disponibile alcun supporto ufficiale. Non si possono aprire richieste di supporto per problemi con i linguaggi sperimentali. 

Le versioni di runtime successive non supportano i linguaggi sperimentali. Il supporto per nuovi linguaggi viene aggiunto solo quando il linguaggio può essere supportato nell'ambiente di produzione. 

### <a name="language-extensibility"></a>Estendibilità di un linguaggio

A partire dalla versione 2.x, il runtime è progettato per offrire [l'estendibilità](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility)della lingua. I linguaggi JavaScript e Java nel runtime 2.x sono costruiti con questa estendibilità.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come sviluppare funzioni nelle lingue supportate, vedere le risorse seguenti:To learn more about how to develop functions in the supported languages, see the following resources:

+ [Guida di riferimento per gli sviluppatori di librerie di classi C](functions-dotnet-class-library.md)
+ [Informazioni di riferimento per gli sviluppatori di script di C](functions-reference-csharp.md)
+ [Informazioni di riferimento per sviluppatori Java](functions-reference-java.md)
+ [Informazioni di riferimento per sviluppatori JavaScript](functions-reference-node.md)
+ [Informazioni di riferimento per gli sviluppatori di PowerShellPowerShell developer reference](functions-reference-powershell.md)
+ [Informazioni di riferimento per gli sviluppatori Python](functions-reference-python.md)
+ [Informazioni di riferimento per sviluppatori TypeScript](functions-reference-node.md#typescript)
