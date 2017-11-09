---
title: Inviare ID contesto utente per abilitare le esperienze di utilizzo in Azure Application Insights | Microsoft Docs
description: Tenere traccia delle azioni degli utenti nel servizio assegnando a ognuno una stringa ID univoca persistente in Application Insights.
services: application-insights
documentationcenter: 
author: abgreg
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: csharp
ms.topic: article
ms.date: 08/02/2017
ms.author: mbullwin
ms.openlocfilehash: e872062eddd4ae74f6148673a8f0b27751e37ca4
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2017
---
#  <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Invio degli ID del contesto utente per abilitare esperienze di utilizzo in Azure Application Insights

## <a name="tracking-users"></a>Monitorare gli utenti

Application Insights consente di monitorare e tenere traccia degli utenti tramite un set di strumenti relativi all'uso del prodotto: 
* [Utenti, sessioni ed eventi](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
* [Grafici a imbuto](https://docs.microsoft.com/azure/application-insights/usage-funnels)
* [Conservazione](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention)
* Coorte
* [Cartelle di lavoro](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

Per tenere traccia delle operazioni eseguite da un utente nel corso del tempo, Application Insights deve usare un ID per ogni utente o sessione. Includere gli ID seguenti in ogni evento o visualizzazione pagina personalizzati.
- Utenti, grafici a imbuto, conservazione e coorti: includere l'ID utente.
- Sessioni: includere l'ID di sessione.

Se l'app è integrata con [JavaScript SDK](https://docs.microsoft.com/azure/application-insights/app-insights-javascript#set-up-application-insights-for-your-web-page), viene tenuta automaticamente traccia dell'ID utente.

## <a name="choosing-user-ids"></a>Scelta degli ID utente

Gli ID utente devono essere resi persistenti tra le sessioni utente per tenere traccia del comportamento degli utenti nel tempo. Esistono vari approcci per rendere persistenti gli ID.
- Una definizione di un utente già presente nel servizio.
- Se il servizio ha accesso a un browser, può passare un cookie contenente un ID al browser. L'ID sarà persistente fino a quando il cookie rimane nel browser dell'utente.
- Se necessario, è possibile usare un nuovo ID per ogni sessione, ma i risultati relativi agli utenti saranno limitati. Ad esempio, non sarà possibile vedere come il comportamento di un utente cambia nel tempo.

L'ID deve essere un GUID o un'altra stringa abbastanza complessa da identificare in modo univoco ogni utente. Potrebbe essere, ad esempio, un numero lungo casuale.

Se l'ID contiene informazioni personali sull'utente, non è un valore appropriato da inviare ad Application Insights come ID utente. È possibile inviare un ID di questo tipo come [ID utente autenticato](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users), ma non soddisfa il requisito dell'ID utente per gli scenari di utilizzo.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>App ASP.NET: Impostare il contesto utente in un ITelemetryInitializer

Creare un inizializzatore di telemetria, come descritto in dettaglio [qui](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer) e impostare Context.User.Id e Context.Session.Id.

Questo esempio imposta l'ID utente su un identificatore che scade al termine della sessione. Se possibile, usare un ID utente che persiste tra le sessioni.

```C#

    using System;
    using System.Web;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that sets the user ID.
       *
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            // For a full experience, track each user across sessions. For an incomplete view of user 
            // behavior within a session, store user ID on the HttpContext Session.
            // Set the user ID if we haven't done so yet.
            if (HttpContext.Current.Session["UserId"] == null)
            {
                HttpContext.Current.Session["UserId"] = Guid.NewGuid();
            }

            // Set the user id on the Application Insights telemetry item.
            telemetry.Context.User.Id = (string)HttpContext.Current.Session["UserId"];

            // Set the session id on the Application Insights telemetry item.
            telemetry.Context.Session.Id = HttpContext.Current.Session.SessionID;
        }
      }
    }
```

## <a name="next-steps"></a>Passaggi successivi
- Per abilitare le esperienze di utilizzo, iniziare a inviare [eventi personalizzati](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) o [visualizzazioni pagina](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se si inviano già eventi personalizzati o visualizzazioni pagina, è possibile esplorare gli strumenti relativi all'uso per scoprire come gli utenti usano il servizio.
    * [Panoramica sull'uso](app-insights-usage-overview.md)
    * [Utenti, Sessioni ed Eventi](app-insights-usage-segmentation.md)
    * [Grafici a imbuto](usage-funnels.md)
    * [Conservazione](app-insights-usage-retention.md)
    * [Cartelle di lavoro](app-insights-usage-workbooks.md)
