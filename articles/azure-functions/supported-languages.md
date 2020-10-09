---
title: Linguaggi supportati in Funzioni di Azure
description: Informazioni sulle lingue supportate (GA) e sulle quali sono disponibili in anteprima e sui modi per estendere lo sviluppo di funzioni ad altre lingue.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 91a6ea886c3828678771b24d69bb7987af1fb105
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83994902"
---
# <a name="supported-languages-in-azure-functions"></a>Linguaggi supportati in Funzioni di Azure

Questo articolo illustra i livelli di supporto offerti per i linguaggi disponibili in Funzioni di Azure. Vengono inoltre descritte le strategie per la creazione di funzioni utilizzando linguaggi non supportati in modo nativo.

## <a name="levels-of-support"></a>Livelli di supporto

Sono disponibili due livelli di supporto:

* **Disponibile a livello generale** - Il linguaggio è completamente supportato e approvato per l'uso in produzione.
* **Anteprima** - Il linguaggio non è ancora supportato ma si prevede che in futuro diventi disponibile a livello generale.

## <a name="languages-by-runtime-version"></a>Linguaggi per versione runtime 

Sono disponibili [tre versioni del runtime di funzioni di Azure](functions-versions.md) . Nella tabella seguente sono indicati i linguaggi supportati in ogni versione del runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

## <a name="custom-handlers-preview"></a>Gestori personalizzati (anteprima) 

I gestori personalizzati sono server Web leggeri che ricevono eventi dall'host di funzioni di Azure. Qualsiasi linguaggio che supporta le primitive HTTP può implementare un gestore personalizzato. Ciò significa che i gestori personalizzati possono essere usati per creare funzioni in linguaggi che non sono ufficialmente supportati. Per altre informazioni, vedere [gestori personalizzati di funzioni di Azure (anteprima)](functions-custom-handlers.md).

## <a name="language-extensibility"></a>Estendibilità di un linguaggio

A partire dalla versione 2. x, il runtime è progettato per offrire l' [estendibilità del linguaggio](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). I linguaggi JavaScript e Java nel runtime 2.x sono costruiti con questa estendibilità.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sullo sviluppo di funzioni nelle lingue supportate, vedere le risorse seguenti:

+ [Riferimenti per sviluppatori della libreria di classi C#](functions-dotnet-class-library.md)
+ [Riferimenti per sviluppatori di script C#](functions-reference-csharp.md)
+ [Riferimenti per sviluppatori Java](functions-reference-java.md)
+ [Riferimenti per sviluppatori JavaScript](functions-reference-node.md)
+ [Guida di riferimento per gli sviluppatori di PowerShell](functions-reference-powershell.md)
+ [Guida di riferimento per sviluppatori Python](functions-reference-python.md)
+ [Informazioni di riferimento per gli sviluppatori TypeScript](functions-reference-node.md#typescript)
