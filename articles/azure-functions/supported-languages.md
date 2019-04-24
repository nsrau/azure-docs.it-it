---
title: Linguaggi supportati in Funzioni di Azure
description: Informazioni sui linguaggi supportati a livello generale, su quelli sperimentali e su quelli in anteprima.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/02/2018
ms.author: glenga
ms.openlocfilehash: 5f55122b3bf4bb7160459d524b20dd1303cc0fd8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60325451"
---
# <a name="supported-languages-in-azure-functions"></a>Linguaggi supportati in Funzioni di Azure

Questo articolo illustra i livelli di supporto offerti per i linguaggi disponibili in Funzioni di Azure.

## <a name="levels-of-support"></a>Livelli di supporto

Sono disponibili tre livelli di supporto:

* **Disponibile a livello generale** - Il linguaggio è completamente supportato e approvato per l'uso in produzione.
* **Anteprima** - Il linguaggio non è ancora supportato ma si prevede che in futuro diventi disponibile a livello generale.
* **Sperimentale** - Il linguaggio non è supportato e potrebbe venire abbandonato in futuro. Non viene data garanzia che diventi disponibile in anteprima o a livello generale.

## <a name="languages-in-runtime-1x-and-2x"></a>Linguaggi nelle versioni 1.x e 2.x del runtime

Sono disponibili [due versioni del runtime di Funzioni di Azure](functions-versions.md). Nella tabella seguente sono indicati i linguaggi supportati in ogni versione del runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Linguaggi sperimentali

I linguaggi sperimentali supportati nella versione 1.x non garantiscono una corretta scalabilità e non supportano tutte le associazioni. Il supporto sperimentale per PowerShell nella versione 1.x, ad esempio, è disponibile solo per la versione 5.1 perché è la versione installata per impostazione predefinita nelle macchine virtuali in cui vengono eseguite le app per le funzioni. Se si vuole eseguire gli script PowerShell, prendere in considerazione [Automazione di Azure](https://azure.microsoft.com/services/automation/).

Non usare le funzionalità sperimentali perché non è disponibile alcun supporto ufficiale. Non si possono aprire richieste di supporto per problemi con i linguaggi sperimentali. 

La versione 2.x del runtime non supporta i linguaggi sperimentali. Il supporto per nuovi linguaggi viene aggiunto solo quando il linguaggio può essere supportato nell'ambiente di produzione. 

### <a name="language-extensibility"></a>Estendibilità di un linguaggio

La versione 2.x del runtime offre l'[estendibilità di un linguaggio](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). I linguaggi JavaScript e Java nel runtime 2.x sono costruiti con questa estendibilità.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso dei linguaggi disponibili a livello generale o in anteprima in Funzioni di Azure, vedere le risorse seguenti:

> [!div class="nextstepaction"]
> [C#](functions-reference-csharp.md)

> [!div class="nextstepaction"]
> [F#](functions-reference-fsharp.md)

> [!div class="nextstepaction"]
> [JavaScript](functions-reference-node.md)

> [!div class="nextstepaction"]
> [Java](functions-reference-java.md)

> [!div class="nextstepaction"]
> [Python](functions-reference-python.md)
