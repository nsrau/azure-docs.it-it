---
title: Scrivere codice per tenere traccia delle richieste con Azure Application Insights | Microsoft Docs
description: Scrivere codice per tenere traccia delle richieste con Application Insights in modo da ottenere i profili per le richieste
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 20f408d9dd32c3fd7a0e319e4051483e3aa54dd9
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2018
ms.locfileid: "52722123"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Scrivere codice per tenere traccia delle richieste con Application Insights

Per visualizzare i profili per l'applicazione nella pagina delle prestazioni, Application Insights deve rilevare le richieste per l'applicazione. Application Insights può rilevare automaticamente le richieste per le applicazioni compilate su framework già instrumentati, ad esempio ASP.Net e ASP.Net Core. Tuttavia per le altre applicazioni, ad esempio i ruoli di lavoro del servizio cloud di Azure e le API senza stato di Service Fabric, è necessario scrivere il codice in modo da indicare ad Application Insights dove iniziano e finiscono le richieste. Dopo aver scritto questo codice, i dati di telemetria delle richieste verranno inviati ad Application Insights e visualizzati nella pagina Prestazioni e i profili verranno raccolti per queste richieste. 

Ecco i passaggi da seguire per tenere traccia delle richieste manualmente:


  1. Aggiungere il codice seguente in un punto iniziale della durata dell'applicazione:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Per altre informazioni sulla configurazione della chiave di strumentazione globale, vedere [Using Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) (Usare Service Fabric con Application Insights).  

  1. Aggiungere un'istruzione `StartOperation<RequestTelemetry>` **USING** intorno a ogni parte del codice che si vuole instrumentare, come nell'esempio seguente:

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        La chiamata di `StartOperation<RequestTelemetry>` in un altro ambito di `StartOperation<RequestTelemetry>` non è supportata. È possibile invece usare `StartOperation<DependencyTelemetry>` nell'ambito nidificato. Ad esempio:   
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```
