---
title: Eseguire query sui dati da un ambiente C# di anteprima utilizzando-Azure Time Series Insights | Microsoft Docs
description: Informazioni su come eseguire query sui dati da un ambiente di Azure Time Series Insights usando un'app C#scritta in.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: a1d32bf7ea296ed2c4ed9351fcefe400c03effa5
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861439"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Eseguire query sui dati dall'ambiente Azure Time Series Insights anteprima usandoC#

Questo C# esempio illustra come eseguire query sui dati dall'ambiente Azure Time Series Insights anteprima.

L'esempio mostra alcuni esempi di base relativi all'uso dell'API Query:

1. Come passaggio preliminare, è necessario acquisire il token di accesso tramite l'API di Azure Active Directory. Passare questo token nell'intestazione `Authorization` di ogni richiesta dell'API Query. Per la configurazione di applicazioni non interattive, vedere [autenticazione e autorizzazione](time-series-insights-authentication-and-authorization.md). Assicurarsi anche che tutte le costanti definite all'inizio dell'esempio siano impostate correttamente.
1. Viene fornito l'elenco degli ambienti a cui l'utente può accedere. Uno degli ambienti viene selezionato come ambiente d'interesse e vengono eseguite altre query sui dati per questo ambiente.
1. Come esempio della richiesta HTTPS, vengono richiesti dati di disponibilità per l'ambiente di interesse.
1. Viene fornito un esempio di supporto per la generazione automatica di SDK da [Azure autorest](https://github.com/Azure/AutoRest).

> [!NOTE]
> Il codice di esempio, nonché i passaggi per compilarlo ed eseguirlo, sono disponibili in [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample).

## <a name="c-example"></a>Esempio in C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> L'esempio di codice precedente può essere eseguito senza modificare i valori di ambiente predefiniti.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'esecuzione di query, vedere informazioni di [riferimento sull'API di query](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Leggere le informazioni su come [connettere un'app JavaScript usando l'SDK client](https://github.com/microsoft/tsiclient) per Time Series Insights.
