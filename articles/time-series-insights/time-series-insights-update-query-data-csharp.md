---
title: "Eseguire query sui dati da un ambiente di anteprima usando C ' : Informazioni dettagliate sulle serie temporali di Azure Documenti Microsoft"
description: Informazioni su come eseguire query sui dati da un ambiente Azure Time Series Insights usando un'app scritta in C.
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
ms.openlocfilehash: fbc2cbc29cb23a21e7d3713091fc22f01bb1b15a
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81379823"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Query data from the Azure Time Series Insights Preview environment using C #

In questo esempio in C'è illustrato come eseguire una query sui dati dalle API di accesso ai dati di anteprima in ambienti di anteprima di Azure Time Series Insights.This C'è demonstrates how to query data from the [Preview Data Access APIs](https://docs.microsoft.com/rest/api/time-series-insights/preview) in Azure Time Series Insights Preview preview environments.

> [!TIP]
> Visualizzare gli esempi [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)di codice di anteprima di C.

## <a name="summary"></a>Riepilogo

Il codice di esempio riportato di seguito illustra le funzionalità seguenti:The sample code below demonstrates the following features:

* Supporto per la generazione automatica di SDK da [Azure AutoRest](https://github.com/Azure/AutoRest).
* Come acquisire un token di accesso tramite Azure Active Directory utilizzando [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* Come passare il token di `Authorization` accesso acquisito nell'intestazione delle richieste API di accesso ai dati successive. 
* Nell'esempio viene fornita un'interfaccia della console che illustra come vengono effettuate le richieste HTTP per il:

    * [API Ambienti di anteprima](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [Ottenere l'API di disponibilità degli ambienti](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) e ottenere [l'API dello schema di eventiGet](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema) Environments Availability API and Get Event Schema API
    * [API query di anteprima](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [API Ottieni eventi](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents), [Ottieni API serie](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)e API Get Aggregate [Series](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [API del modello Time Series](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * [API Get Hierarchies](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get) e [API Batch gerarchieGet](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch) Hierarchies API and Hierarchies Batch API
        * [API Get Types](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get) e [batch di tipi API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch)
        * [API Get Instances](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get) e [API Batch di istanzeGet](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch) Instances API and Instances Batch API
* Funzionalità [avanzate di ricerca](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features) e [TSX.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

## <a name="prerequisites-and-setup"></a>Prerequisiti e configurazione

Prima di compilare ed eseguire lo script di esempio, completare questa procedura:

1. Effettuare il provisioning di un ambiente [Preview Azure Time Series Insights.Provision a Preview Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment) environment.
1. Configurare l'ambiente Azure Time Series Insights per Azure Active Directory come descritto in [Autenticazione e autorizzazione](time-series-insights-authentication-and-authorization.md). 
1. Eseguire [il metodo GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat) come specificato nel [Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md) per generare le dipendenze client time Series Insights Preview.
1. Aprire `TSIPreviewDataPlaneclient.sln` la soluzione `DataPlaneClientSampleApp` e impostarla come progetto predefinito in Visual Studio.Open the solution and set as the default project in Visual Studio.
1. Installare le dipendenze di progetto necessarie utilizzando la procedura `.exe` descritta di [seguito](#project-dependencies) e compilare l'esempio in un file eseguibile.
1. Eseguire `.exe` il file facendo doppio clic su di esso.

## <a name="project-dependencies"></a>Dipendenze progetto

Si consiglia di utilizzare la versione più recente di Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - Versione 16.4.2

Il codice di esempio ha diverse dipendenze obbligatorie che possono essere visualizzate nel file [packages.config.](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config)

Scaricare i pacchetti in Visual Studio 2019 selezionando l'opzione **Compila** > **soluzione** di compilazione. 

In alternativa, aggiungere ogni pacchetto utilizzando [NuGet 2.12 .](https://www.nuget.org/) Ad esempio:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>Codice di esempio C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * L'esempio di codice può essere eseguito senza modificare le variabili di ambiente predefinite.
> * L'esempio di codice verrà compilato in un'app console eseguibile .NET.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'esecuzione di query, leggere le informazioni di [riferimento sull'API Query](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Scopri come [connettere un'app JavaScript usando l'SDK del client](https://github.com/microsoft/tsiclient) a Time Series Insights.
