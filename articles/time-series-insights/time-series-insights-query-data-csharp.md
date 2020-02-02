---
title: Eseguire query sui dati da un ambiente C# GA usando il codice Azure Time Series Insights | Microsoft Docs
description: Informazioni su come eseguire query sui dati da un ambiente Azure Time Series Insights usando un'app personalizzata C#scritta in.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/31/2020
ms.custom: seodec18
ms.openlocfilehash: a5cb435b38a776ba652854592bdc7d3e833742d1
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935089"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Eseguire query sui dati dall'ambiente Azure Time Series Insights GA usandoC#

Questo C# esempio illustra come usare le [API di query GA](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) per eseguire query sui dati da ambienti Azure Time Series Insights GA.

## <a name="summary"></a>Riepilogo

Il codice di esempio seguente illustra le funzionalità seguenti:

* Come acquisire un token di accesso tramite Azure Active Directory usando [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Come passare il token di accesso acquisito nell'intestazione `Authorization` delle richieste API di query successive. 

* L'esempio chiama ogni API di query GA che illustra come vengono effettuate le richieste HTTP al:
    * [Ottenere gli ambienti API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api) per restituire gli ambienti a cui l'utente può accedere
    * [Ottenere l'API di disponibilità dell'ambiente](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [Ottenere l'API dei metadati dell'ambiente](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api) per recuperare i metadati dell'ambiente
    * [Ottenere l'API degli eventi degli ambienti](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [Ottenere l'API aggregazioni dell'ambiente](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* Come interagire con le API di query GA usando WSS per messaggio:

   * [Ottenere l'API con flusso di eventi dell'ambiente](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [Ottenere le aggregazioni dell'ambiente con flusso API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

> [!NOTE]
> Il codice di esempio è disponibile in [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="prerequisites-and-setup"></a>Prerequisiti e configurazione

Prima di compilare ed eseguire lo script di esempio, completare questa procedura:

1. Effettuare [il provisioning di un ambiente di Azure Time Series Insights GA](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started) .

1. Configurare l'ambiente di Azure Time Series Insights per Azure Active Directory, come descritto in [autenticazione e autorizzazione](time-series-insights-authentication-and-authorization.md). 

1. Installare le dipendenze di progetto obbligatorie.

1. Modificare il codice di esempio seguente sostituendo ogni **#DUMMY #** con l'identificatore di ambiente appropriato.

1. Eseguire il codice all'interno di Visual Studio.

> [!TIP]
> * Per altri esempi C# di codice ga, vedere [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="project-dependencies"></a>Dipendenze progetto

Si consiglia di usare la versione più recente di Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -versione 16.4.2 +

Il codice di esempio ha due dipendenze obbligatorie:

* Pacchetto [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -3.13.9.
* [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json) -pacchetto 9.0.1.

Aggiungere i pacchetti usando [NuGet 2.12 +](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>Codice di esempio C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'esecuzione di query, vedere informazioni di [riferimento sull'API di query](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Leggere le informazioni su come [connettere un'app JavaScript usando l'SDK client](https://github.com/microsoft/tsiclient) per Time Series Insights.
