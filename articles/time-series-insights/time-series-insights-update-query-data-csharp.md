---
title: Eseguire query sui dati da un ambiente di versione di anteprima di Azure ora Series Insights usando C# codice | Microsoft Docs
description: Questo articolo descrive come eseguire query sui dati di un ambiente Azure Time Series Insights mediante la codifica di un'app personalizzata scritta nel linguaggio C# (C-sharp) .NET.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
reviewer: jasonwhowell, kfile, tsidocs
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/09/2019
ms.custom: seodec18
ms.openlocfilehash: ebd5cb92b510da56446ca9e559b03a56cb2af7cf
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65515280"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Eseguire query sui dati dall'ambiente versione di anteprima di Azure ora Series Insights tramiteC#

In questo C# riportato di seguito viene illustrato come eseguire query sui dati dall'ambiente di versione di anteprima di Azure ora Series Insights.

L'esempio mostra alcuni esempi di base relativi all'uso dell'API Query:

1. Come passaggio preliminare, è necessario acquisire il token di accesso tramite l'API di Azure Active Directory. Passare questo token nell'intestazione `Authorization` di ogni richiesta dell'API Query. Per la configurazione di applicazioni non interattive, vedere [Autenticazione e autorizzazione](time-series-insights-authentication-and-authorization.md). Assicurarsi anche che tutte le costanti definite all'inizio dell'esempio siano impostate correttamente.
1. Viene fornito l'elenco degli ambienti a cui l'utente può accedere. Uno degli ambienti viene selezionato come ambiente d'interesse e vengono eseguite altre query sui dati per questo ambiente.
1. Come esempio della richiesta HTTPS, vengono richiesti dati di disponibilità per l'ambiente di interesse.
1. Come esempio della richiesta di Web Socket, vengono richiesti dati relativi alle aggregazioni di eventi per l'ambiente di interesse. Vengono richiesti dati per l'intero intervallo di tempo di disponibilità.

> [!NOTE]
> Questo esempio di codice è anche disponibile all'indirizzo [ https://github.com/Azure-Samples/Azure-Time-Series-Insights ](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample).

## <a name="c-example"></a>Esempio in C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> Senza modificare i valori di ambiente predefinite, è possibile eseguire l'esempio di codice riportato sopra.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'esecuzione di query, vedere la [riferimento all'API di Query](/rest/api/time-series-insights/preview-query).

- Leggere la procedura per [connettere un'app a singola pagina JavaScript](tutorial-create-tsi-sample-spa.md) di Time Series Insights.