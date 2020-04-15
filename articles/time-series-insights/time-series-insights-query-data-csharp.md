---
title: Eseguire query sui dati da un ambiente GA usando il codice C Documenti Microsoft
description: Informazioni su come eseguire query sui dati da un ambiente Azure Time Series Insights usando un'app personalizzata scritta in C.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: 754d1b80236d138693987cccee7a218ccd96b16b
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383876"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Query data from the Azure Time Series Insights GA environment using C #

In questo esempio in Cè viene illustrato come usare le [API di query GA](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) per eseguire query sui dati da ambienti GA di Azure Time Series Insights.

> [!TIP]
> Visualizzare gli esempi di [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)codice GA c'è in .

## <a name="summary"></a>Riepilogo

Il codice di esempio riportato di seguito illustra le funzionalità seguenti:The sample code below demonstrates the following features:

* Come acquisire un token di accesso tramite Azure Active Directory utilizzando [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Come passare il token di `Authorization` accesso acquisito nell'intestazione delle richieste API Query successive. 

* L'esempio chiama ciascuna delle API di query GA che dimostrano come vengono effettuate le richieste HTTP per:
    * [Api Ambienti](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api) per restituire gli ambienti a cui l'utente ha accesso
    * [Get Environment Availability API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [Ottenere l'API dei metadati dell'ambiente](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api) per recuperare i metadati dell'ambienteGet Environment Metadata API to retrieve environment metadata
    * [API Eventi Get Environments](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [Get Environment Aggregates API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* Come interagire con le API di query GA utilizzando WSS per inviare un messaggio:

   * [API ottenere gli eventi di ambiente in streamingGet Environment Events Streamed API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [Get Environment Aggregates Streamed API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Prerequisiti e configurazione

Prima di compilare ed eseguire lo script di esempio, completare questa procedura:

1. Effettuare il provisioning di un ambiente [GA Azure Time Series Insights.Provision](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started) a GA Azure Time Series Insights environment.
1. Configurare l'ambiente Azure Time Series Insights per Azure Active Directory come descritto in [Autenticazione e autorizzazione](time-series-insights-authentication-and-authorization.md). 
1. Installare le dipendenze di progetto necessarie.
1. Modificare il codice di esempio riportato di seguito sostituendo ogni **#DUMMY** con l'identificatore di ambiente appropriato.
1. Eseguire il codice all'interno di Visual Studio.Execute the code inside Visual Studio.

## <a name="project-dependencies"></a>Dipendenze progetto

Si consiglia di utilizzare la versione più recente di Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - Versione 16.4.2

Il codice di esempio ha due dipendenze obbligatorie:The sample code has two required dependencies:

* [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) - pacchetto 3.13.9.
* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) - 9.0.1 pacchetto.

Scaricare i pacchetti in Visual Studio 2019 selezionando l'opzione **Compila** > **soluzione** di compilazione.

In alternativa, aggiungere i pacchetti utilizzando [NuGet 2.12](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>Codice di esempio C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'esecuzione di query, leggere le informazioni di [riferimento sull'API Query](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Scopri come [connettere un'app JavaScript usando l'SDK del client](https://github.com/microsoft/tsiclient) a Time Series Insights.
