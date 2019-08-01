---
title: Eseguire query sui dati da un ambiente Azure Time Series Insights C# anteprima usando il codice | Microsoft Docs
description: Questo articolo descrive come eseguire query sui dati di un ambiente Azure Time Series Insights mediante la codifica di un'app personalizzata scritta nel linguaggio C# (C-sharp) .NET.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
reviewer: jasonwhowell, kfile, tsidocs
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/09/2019
ms.custom: seodec18
ms.openlocfilehash: 8fdfd39527ccd298b78ef2c4c895d1f667d8d33b
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68677606"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Eseguire query sui dati dall'ambiente Azure Time Series Insights anteprima usandoC#

Questo C# esempio illustra come eseguire query sui dati dall'ambiente Azure Time Series Insights anteprima.

L'esempio mostra alcuni esempi di base relativi all'uso dell'API Query:

1. Come passaggio preliminare, è necessario acquisire il token di accesso tramite l'API di Azure Active Directory. Passare questo token nell'intestazione `Authorization` di ogni richiesta dell'API Query. Per la configurazione di applicazioni non interattive, vedere [Autenticazione e autorizzazione](time-series-insights-authentication-and-authorization.md). Assicurarsi anche che tutte le costanti definite all'inizio dell'esempio siano impostate correttamente.
1. Viene fornito l'elenco degli ambienti a cui l'utente può accedere. Uno degli ambienti viene selezionato come ambiente d'interesse e vengono eseguite altre query sui dati per questo ambiente.
1. Come esempio della richiesta HTTPS, vengono richiesti dati di disponibilità per l'ambiente di interesse.
1. Come esempio della richiesta di Web Socket, vengono richiesti dati relativi alle aggregazioni di eventi per l'ambiente di interesse. Vengono richiesti dati per l'intero intervallo di tempo di disponibilità.

> [!NOTE]
> Questo codice di esempio è disponibile anche [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)in.

## <a name="c-example"></a>Esempio in C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> L'esempio di codice precedente può essere eseguito senza modificare i valori di ambiente predefiniti.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'esecuzione di query, vedere informazioni di [riferimento sull'API di query](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Informazioni su come [connettere un'app JavaScript a una singola pagina](tutorial-create-tsi-sample-spa.md) a Time Series Insights.