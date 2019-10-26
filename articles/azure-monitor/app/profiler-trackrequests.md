---
title: Scrivere codice per tenere traccia delle richieste con Azure Application Insights | Microsoft Docs
description: Scrivere codice per tenere traccia delle richieste con Application Insights in modo da ottenere i profili per le richieste.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 3f449c98ed44f13fb6b3849ef2457cd8fbd916de
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900019"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Scrivere codice per tenere traccia delle richieste con Application Insights

Per visualizzare i profili per l'applicazione nella pagina delle prestazioni, Azure Application Insights deve rilevare le richieste per l'applicazione. Application Insights può rilevare automaticamente le richieste per le applicazioni basate su framework già instrumentati. Due esempi sono ASP.NET e ASP.NET Core. 

Per altre applicazioni, ad esempio i ruoli di lavoro dei Servizi cloud di Azure e le API senza stato di Service Fabric, è necessario scrivere il codice in modo da indicare ad Application Insights dove iniziano e finiscono le richieste. Dopo avere scritto questo codice, i dati di telemetria delle richieste vengono inviati ad Application Insights. È possibile visualizzare i dati di telemetria nella pagina delle prestazioni; inoltre vengono raccolti i profili per tali richieste. 

Per tenere manualmente traccia delle richieste, seguire questa procedura:

  1. Aggiungere il codice seguente in un punto iniziale della durata dell'applicazione:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Per altre informazioni sulla configurazione della chiave di strumentazione globale, vedere [Using Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) (Usare Service Fabric con Application Insights).  

  1. Aggiungere un'istruzione **using** `StartOperation<RequestTelemetry>` intorno a ogni parte del codice che si vuole instrumentare, come nell'esempio seguente:

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

        La chiamata di `StartOperation<RequestTelemetry>` in un altro ambito di `StartOperation<RequestTelemetry>` non è supportata. È possibile invece usare `StartOperation<DependencyTelemetry>` nell'ambito nidificato. ad esempio:  
        
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
