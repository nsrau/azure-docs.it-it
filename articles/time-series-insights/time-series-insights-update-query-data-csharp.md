---
title: Eseguire query sui dati da un ambiente Gen2 usando C#-Azure Time Series Insights | Microsoft Docs
description: Informazioni su come eseguire query sui dati da un ambiente Azure Time Series Insights Gen2 usando un'app scritta in C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/14/2020
ms.custom: seodec18
ms.openlocfilehash: fcb38f761a72bf2c6e5dee8bb993cec9143e7241
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998952"
---
# <a name="query-data-from-the-azure-time-series-insights-gen2-environment-using-c-sharp"></a>Eseguire query sui dati dall'ambiente Azure Time Series Insights Gen2 con C Sharp

Questo esempio in C# illustra come eseguire query sui dati dalle [API di accesso ai dati di Gen2](https://docs.microsoft.com/rest/api/time-series-insights/reference-data-access-overview) in Azure Time Series Insights ambienti Gen2.

> [!TIP]
> Vedere gli esempi di codice C# Gen2 in [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen2-sample/csharp-tsi-gen2-sample) .

## <a name="summary"></a>Summary

Il codice di esempio seguente illustra le funzionalità seguenti:

* Supporto per la generazione automatica dell'SDK da [Azure AutoRest](https://github.com/Azure/AutoRest).
* Come acquisire un token di accesso da Azure Active Directory usando [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* Come passare il token di accesso acquisito nell'intestazione `Authorization` delle successive richieste API di accesso ai dati.
* L'esempio fornisce un'interfaccia console che illustra come le richieste HTTP vengono apportate agli elementi seguenti:
  * [API per ambienti Gen2](https://docs.microsoft.com/rest/api/time-series-insights/reference-environments-apis)
    * [API GET di disponibilità degli ambienti](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/getavailability) e [API GET dello schema di eventi](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/geteventschema)
  * [API di query Gen2](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis)
    * [API GET degli eventi](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents), [API GET delle serie](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries) e [API GET delle serie di aggregazione](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
  * [API modello della serie temporale](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
    * [API GET delle gerarchie](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies) e [API del batch di gerarchie](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    * [API GET dei tipi](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes) e [API del batch di tipi](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    * [API GET delle istanze](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances) e [API del batch di istanze](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)

* Funzionalità avanzate di [ricerca](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#search-features) e [TSX](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax).

## <a name="prerequisites-and-setup"></a>Prerequisiti e configurazione

Prima di compilare ed eseguire lo script di esempio, completare questa procedura:

1. Effettuare [il provisioning di un ambiente di Azure Time Series Insights Gen2](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment) .
1. Configurare l'ambiente di Azure Time Series Insights per Azure Active Directory come descritto in [Autenticazione e autorizzazione](time-series-insights-authentication-and-authorization.md).
1. Eseguire il [GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/GenerateCode.bat) come specificato in [Readme.MD](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/Readme.md) per generare le dipendenze del client di Azure Time Series Insights Gen2.
1. Aprire la soluzione `TSIPreviewDataPlaneclient.sln` e impostare `DataPlaneClientSampleApp` come progetto predefinito in Visual Studio.
1. Installare le dipendenze di progetto necessarie usando i passaggi descritti [di seguito](#project-dependencies) e compilare l'esempio in un file eseguibile `.exe`.
1. Eseguire il file `.exe` selezionandolo facendo doppio clic.

## <a name="project-dependencies"></a>Dipendenze progetto

Si consiglia di usare la versione più recente di Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - Versione 16.4.2 o successive

Il codice di esempio presenta diverse dipendenze necessarie che possono essere visualizzate nel file [packages.config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/packages.config).

Scaricare i pacchetti in Visual Studio 2019 selezionando l'opzione **Compila** > **Compila soluzione**.

In alternativa, aggiungere ogni pacchetto usando [NuGet 2.12 o versioni successive](https://www.nuget.org/). Ad esempio:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>Codice di esempio C#

Per accedere al codice di esempio C#, vedere il repository [Azure Time Series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen2-sample/csharp-tsi-gen2-sample) . 

> [!NOTE]
>
> * L'esempio di codice può essere eseguito senza modificare le variabili di ambiente predefinite.
> * L'esempio di codice verrà compilato in un'app console eseguibile .NET.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'esecuzione di query, vedere le [informazioni di riferimento sull'API di query](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis).

* Leggere le informazioni su come [connettere un'app JavaScript usando l'SDK client](https://github.com/microsoft/tsiclient) per Azure Time Series Insights.
