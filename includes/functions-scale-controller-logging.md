---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 49818cf59da2d63cef4bb0bdca38d38a2feafca5
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86169910"
---
| | |
|--|--|
|**`<DESTINATION>`**| Destinazione a cui vengono inviati i log. I valori validi sono `AppInsights` e `Blob`.<br/>Quando si usa `AppInsights` , assicurarsi che [Application Insights sia abilitato nell'app per le funzioni](../articles/azure-functions/functions-monitoring.md#enable-application-insights-integration).<br/>Quando si imposta la destinazione su `Blob` , i log vengono creati in un contenitore BLOB denominato `azure-functions-scale-controller` nell'account di archiviazione predefinito impostato nell' `AzureWebJobsStorage` impostazione dell'applicazione. |
|**`<VERBOSITY>`** | Specifica il livello di registrazione. I valori supportati sono `None`, `Warning` e `Verbose`.<br/>Quando è impostato su `Verbose` , il controller di scalabilità registra un motivo per ogni modifica nel conteggio dei thread di lavoro, nonché informazioni sui trigger che determinano tali decisioni. I log dettagliati includono gli avvisi dei trigger e gli hash usati dai trigger prima e dopo l'esecuzione del controller di scalabilità. |

> [!CAUTION]
> Non lasciare abilitata la registrazione del controller di scalabilità. Abilitare la registrazione fino a quando non sono stati raccolti dati sufficienti per comprendere il comportamento del controller di scalabilità e quindi disabilitarlo.