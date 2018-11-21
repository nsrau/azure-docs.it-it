---
title: Inviare ID contesto utente per abilitare le esperienze di utilizzo in Azure Application Insights | Microsoft Docs
description: Tenere traccia delle azioni degli utenti nel servizio assegnando a ognuno una stringa ID univoca persistente in Application Insights.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/02/2017
ms.reviewer: abgreg;mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 5a224d7a78e35776b36b162228f6ba4c72677069
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636204"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Invio degli ID del contesto utente per abilitare esperienze di utilizzo in Azure Application Insights

## <a name="tracking-users"></a>Monitorare gli utenti

Application Insights consente di monitorare e tenere traccia degli utenti tramite un set di strumenti relativi all'uso del prodotto:

- [Utenti, sessioni ed eventi](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
- [Grafici a imbuto](https://docs.microsoft.com/azure/application-insights/usage-funnels)
- Coorte [Conservazione](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention)
- [Cartelle di lavoro](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

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

Creare un inizializzatore della telemetria come descritto in dettaglio [qui](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer). Passare l'ID sessione tramite la telemetria delle richieste e impostare Context.User.Id e Context.Session.Id.

Questo esempio imposta l'ID utente su un identificatore che scade al termine della sessione. Se possibile, usare un ID utente che persiste tra le sessioni.

### <a name="telemetry-initializer"></a>Inizializzatore della telemetria

```csharp
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
        var ctx = HttpContext.Current;

        // If telemetry initializer is called as part of request execution and not from some async thread
        if (ctx != null)
        {
            var requestTelemetry = ctx.GetRequestTelemetry();
 
            // Set the user and session ids from requestTelemetry.Context.User.Id, which is populated in Application_PostAcquireRequestState in Global.asax.cs.
            if (requestTelemetry != null && !string.IsNullOrEmpty(requestTelemetry.Context.User.Id) &&
                (string.IsNullOrEmpty(telemetry.Context.User.Id) || string.IsNullOrEmpty(telemetry.Context.Session.Id)))
            {
                // Set the user id on the Application Insights telemetry item.
                telemetry.Context.User.Id = requestTelemetry.Context.User.Id;
 
                // Set the session id on the Application Insights telemetry item.
                telemetry.Context.Session.Id = requestTelemetry.Context.User.Id;
            }
        }
    }
  }
}
```

### <a name="globalasaxcs"></a>Global.asax.cs

```csharp
using System.Web;
using System.Web.Http;
using System.Web.Mvc;
using System.Web.Optimization;
using System.Web.Routing;

namespace MvcWebRole.Telemetry
{
    public class MvcApplication : HttpApplication
    {
        protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            GlobalConfiguration.Configure(WebApiConfig.Register);
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
        }
 
        protected void Application_PostAcquireRequestState()
        {
            var requestTelemetry = Context.GetRequestTelemetry();
 
            if (HttpContext.Current.Session != null && requestTelemetry != null && string.IsNullOrEmpty(requestTelemetry.Context.User.Id))
            {
                requestTelemetry.Context.User.Id = Session.SessionID;
            }
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

- Per abilitare le esperienze di utilizzo, iniziare a inviare [eventi personalizzati](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) o [visualizzazioni pagina](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se si inviano già eventi personalizzati o visualizzazioni pagina, è possibile esplorare gli strumenti relativi all'uso per scoprire come gli utenti usano il servizio.
    - [Panoramica sull'uso](app-insights-usage-overview.md)
    - [Utenti, Sessioni ed Eventi](app-insights-usage-segmentation.md)
    - [Grafici a imbuto](usage-funnels.md)
    - [Conservazione](app-insights-usage-retention.md)
    - [Cartelle di lavoro](app-insights-usage-workbooks.md)
