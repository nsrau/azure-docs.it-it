---
title: Linguaggi supportati in Funzioni di Azure
description: Informazioni sui linguaggi supportati a livello generale, su quelli sperimentali e su quelli in anteprima.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: tdykstra
ms.openlocfilehash: 5786a206b258cfe7c48f52ead9b5a4cceb64cd5f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2017
---
# <a name="supported-languages-in-azure-functions"></a>Linguaggi supportati in Funzioni di Azure

Questo articolo illustra i livelli di supporto offerti per i linguaggi disponibili in Funzioni di Azure.

## <a name="levels-of-support"></a>Livelli di supporto

Sono disponibili tre livelli di supporto:

* **Disponibile a livello generale** - Il linguaggio è completamente supportato e approvato per l'uso in produzione.
* **Anteprima** - Il linguaggio non è ancora supportato ma si prevede che in futuro diventi disponibile a livello generale.
* **Sperimentale** - Il linguaggio non è supportato e potrebbe venire abbandonato in futuro. Non viene data garanzia che diventi disponibile in anteprima o a livello generale.

## <a name="languages-in-runtime-1x-and-2x"></a>Linguaggi nelle versioni 1.x e 2.x del runtime

Sono disponibili [due versioni del runtime di Funzioni di Azure](functions-versions.md). La versione 1.x del runtime è disponibile a livello generale. È l'unica versione del runtime approvata per le applicazioni di produzione. La versione 2.x è attualmente in anteprima, pertanto anche i linguaggi che supporta sono in anteprima. Nella tabella seguente sono indicati i linguaggi supportati in ogni versione del runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Linguaggi sperimentali

I linguaggi supportati nella versione 1.x non garantiscono una corretta scalabilità e non supportano tutte le associazioni. Ad esempio, Python è lento perché il runtime di Funzioni esegue *python.exe* ogni volta che chiama una funzione. Python supporta le associazioni HTTP, ma non può accedere all'oggetto della richiesta.

Il supporto sperimentale per PowerShell è disponibile solo per la versione 4.0 perché è la versione installata sulle macchine virtuali in cui vengono eseguite le applicazioni di Funzioni. Se si vuole eseguire gli script PowerShell, prendere in considerazione [Automazione di Azure](https://azure.microsoft.com/services/automation/).

La versione 2.x del runtime non supporta i linguaggi sperimentali. Nella versione 2.x verrà aggiunto il supporto per un linguaggio solo quando garantisce un'ottima scalabilità e supporta i trigger avanzati.

Se si vuole usare uno dei linguaggi disponibili solo nella versione 1.x, è consigliabile continuare a usare il runtime 1.x. Non usare i linguaggi sperimentali perché non è disponibile alcun supporto ufficiale. È possibile richiedere assistenza tramite [GitHub issues](https://github.com/Azure/azure-webjobs-sdk-script/issues) (Problemi di GitHub), ma non si possono aprire richieste di supporto per problemi con i linguaggi sperimentali. 

### <a name="language-extensibility"></a>Estendibilità di un linguaggio

La versione 2.x del runtime offre l'[estendibilità di un linguaggio](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Java è uno dei primi linguaggi basati su questo modello di estendibilità ed è disponibile in anteprima nella versione 2.x.

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