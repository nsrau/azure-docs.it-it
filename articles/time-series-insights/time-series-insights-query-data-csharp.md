---
title: Eseguire query sui dati da un ambiente Gen1 usando C# codice-Azure Time Series Insights Gen1 | Microsoft Docs
description: Informazioni su come eseguire query sui dati da un ambiente Gen1 Azure Time Series Insights usando un'app personalizzata scritta in C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: a7dd67387a541ff62bfa85a7afc2f1bbf31c6180
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91596432"
---
# <a name="query-data-from-the-azure-time-series-insights-gen1-environment-using-c-sharp"></a>Eseguire query sui dati dall'ambiente Azure Time Series Insights Gen1 con C Sharp

> [!CAUTION]
> È un articolo di Gen1.

Questo esempio C# illustra come usare le [API di query Gen1](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query) per eseguire query sui dati da Azure Time Series Insights ambienti Gen1.

> [!TIP]
> Vedere gli esempi di codice C# Gen1 in [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen1-sample/csharp-tsi-gen1-sample) .

## <a name="summary"></a>Summary

Il codice di esempio seguente illustra le funzionalità seguenti:

* Come acquisire un token di accesso da Azure Active Directory usando [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Come passare il token di accesso acquisito nell' `Authorization` intestazione delle richieste API di query successive.

* L'esempio chiama ogni API di query Gen1 che illustra il modo in cui vengono inviate le richieste HTTP a:
  * [Ottenere gli ambienti API](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environments-api) per restituire gli ambienti a cui l'utente può accedere
  * [Ottenere l'API di disponibilità dell'ambiente](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-availability-api)
  * [Ottenere l'API dei metadati dell'ambiente](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-metadata-api) per recuperare i metadati dell'ambiente
  * [Ottenere l'API degli eventi degli ambienti](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-events-api)
  * [Ottenere l'API aggregazioni dell'ambiente](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api)

* Come interagire con le API di query Gen1 usando WSS per messaggio:

  * [Ottenere l'API con flusso di eventi dell'ambiente](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-events-streamed-api)
  * [Ottenere le aggregazioni dell'ambiente con flusso API](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Prerequisiti e configurazione

Prima di compilare ed eseguire lo script di esempio, completare questa procedura:

1. Effettuare [il provisioning di un ambiente di Azure Time Series Insights Gen1](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started) .
1. Configurare l'ambiente di Azure Time Series Insights per Azure Active Directory come descritto in [Autenticazione e autorizzazione](time-series-insights-authentication-and-authorization.md).
1. Installare le dipendenze del progetto richieste.
1. Modificare il codice di esempio seguente sostituendo ogni **#DUMMY #** con l'identificatore di ambiente appropriato.
1. Eseguire il codice all'interno di Visual Studio.

## <a name="project-dependencies"></a>Dipendenze progetto

Si consiglia di usare la versione più recente di Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - Versione 16.4.2 o successive

Il codice di esempio ha due dipendenze obbligatorie:

* Pacchetto [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -3.13.9.
* [Newtonsoft.Jspacchetto di](https://www.nuget.org/packages/Newtonsoft.Json) 9.0.1.

Scaricare i pacchetti in Visual Studio 2019 selezionando l'opzione **Compila** > **Compila soluzione**.

In alternativa, aggiungere i pacchetti usando [NuGet 2.12 +](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>Codice di esempio C#

Per accedere al codice di esempio C#, vedere il repository [Azure Time Series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen1-sample/csharp-tsi-gen1-sample/Program.cs)].

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'esecuzione di query, vedere le [informazioni di riferimento sull'API di query](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api).

* Informazioni su come [connettere un'app JavaScript usando l'SDK client](https://github.com/microsoft/tsiclient) a Time Series Insights.
Azure-Samples/Azure-Time-Series-Insights/Gen1-Sample/CSharp-TSI-Gen1-Sample/Program. cs
