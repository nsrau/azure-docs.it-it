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
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 76e3ac85a6725976ebd14dac1805079613c94ec6
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76980988"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Eseguire query sui dati dall'ambiente Azure Time Series Insights anteprima usandoC#

Questo C# esempio illustra come eseguire query sui dati dalle [API di accesso ai dati di anteprima](https://docs.microsoft.com/rest/api/time-series-insights/preview) in Azure Time Series Insights ambienti di anteprima.

> [!TIP]
> Visualizzare gli C# esempi di codice di anteprima in [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample).

## <a name="summary"></a>Riepilogo

Il codice di esempio seguente illustra le funzionalità seguenti:

* Supporto per la generazione automatica di SDK da [Azure autorest](https://github.com/Azure/AutoRest).
* Come acquisire un token di accesso tramite Azure Active Directory usando [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* Come passare il token di accesso acquisito nell'intestazione `Authorization` delle successive richieste API di accesso ai dati. 
* L'esempio fornisce un'interfaccia console che illustra come vengono effettuate le richieste HTTP a:

    * [API per ambienti di anteprima](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [Ottenere gli ambienti API di disponibilità](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) e [ottenere l'API dello schema di eventi](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [API query di anteprima](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [Ottenere l'API degli eventi](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents), [ottenere l'API della serie](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)e ottenere l'API della serie di [aggregazioni](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [API del modello Time Series](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * API batch [Get gerarchie](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get) e [gerarchie](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch)
        * API batch [Get types](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get) e [tipi](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch) API
        * API batch [Get instances](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get) e [instance](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch)
* Funzionalità di [ricerca](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features) avanzate e [TSX](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) .

## <a name="prerequisites-and-setup"></a>Prerequisiti e configurazione

Prima di compilare ed eseguire lo script di esempio, completare questa procedura:

1. Eseguire [il provisioning di un ambiente di anteprima Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment) .
1. Configurare l'ambiente di Azure Time Series Insights per Azure Active Directory, come descritto in [autenticazione e autorizzazione](time-series-insights-authentication-and-authorization.md). 
1. Eseguire [GenerateCode. bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat) come specificato in [Readme.MD](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md) per generare le dipendenze del client di Time Series Insights Preview.
1. Aprire la soluzione `TSIPreviewDataPlaneclient.sln` e impostare `DataPlaneClientSampleApp` come progetto predefinito in Visual Studio.
1. Installare le dipendenze di progetto necessarie usando i passaggi descritti di [seguito](#project-dependencies) e compilare l'esempio in un file eseguibile `.exe`.
1. Eseguire il `.exe` file facendo doppio clic su di esso.

## <a name="project-dependencies"></a>Dipendenze progetto

Si consiglia di usare la versione più recente di Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -versione 16.4.2 +

Il codice di esempio presenta diverse dipendenze necessarie che possono essere visualizzate nel file [packages. config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config) .

Scaricare i pacchetti in Visual Studio 2019 selezionando l'opzione **compila** > **Compila soluzione** . 

In alternativa, aggiungere ogni pacchetto usando [NuGet 2.12 +](https://www.nuget.org/). Ad esempio:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>Codice di esempio C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * L'esempio di codice può essere eseguito senza modificare le variabili di ambiente predefinite.
> * L'esempio di codice verrà compilato in un'app console eseguibile .NET.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'esecuzione di query, vedere informazioni di [riferimento sull'API di query](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Leggere le informazioni su come [connettere un'app JavaScript usando l'SDK client](https://github.com/microsoft/tsiclient) per Time Series Insights.
