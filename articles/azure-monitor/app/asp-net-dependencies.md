---
title: Monitoraggio delle dipendenze in Azure Application Insights | Microsoft Docs
description: Analizzare l'utilizzo, disponibilità e prestazioni dell'applicazione web di Microsoft Azure con Application Insights o in locale.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbullwin
ms.openlocfilehash: c77b5810164aef7508f717a0f75d90cf6cba2089
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59273108"
---
# <a name="set-up-application-insights-dependency-tracking"></a>Impostare Application Insights: rilevamento delle dipendenze
Una *dipendenza* è un componente esterno chiamato dall'app. In genere è un servizio chiamato con il protocollo HTTP, oppure un database o un file system. [Application Insights](../../azure-monitor/app/app-insights-overview.md) misura per quanto tempo l'applicazione attende le dipendenze e la frequenza con la quale una chiamata alle dipendenze non riesce. È possibile esaminare chiamate specifiche e correlarle a richieste ed eccezioni.

Il monitoraggio predefinito delle dipendenze attualmente segnala chiamate ai seguenti tipi di dipendenze:

* Server
  * Database SQL
  * Servizi Web ASP.NET e WCF che usano i binding basati su HTTP
  * Chiamate HTTP locali o remote
  * Azure Cosmos DB, tabelle, archiviazione BLOB e coda 
* Pagina Web
  * Chiamate AJAX

Il monitoraggio funziona tramite l'uso di [strumentazione con codice byte](https://msdn.microsoft.com/library/z9z62c29.aspx) basata su determinati metodi o basata su callback DiagnosticSource da .NET Framework (negli ultimi SDK di .NET). L'overhead delle prestazioni è minimo.

È anche possibile scrivere chiamate SDK per monitorare altre dipendenze, sia nel codice client che nel codice server, usando l'[API TrackDependency](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

> [!NOTE]
> viene tenuta automaticamente traccia di Azure Cosmos DB solo se è usato [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking). La modalità TCP non verrà acquisita da Application Insights.

## <a name="set-up-dependency-monitoring"></a>Configurare il monitoraggio delle dipendenze
Informazioni sulle dipendenze parziali vengono raccolte automaticamente da [Application Insights SDK](asp-net.md). Per ottenere dati completi, installare l'agente appropriato per il server host.

| Piattaforma | Installa |
| --- | --- |
| Server IIS |[Installare Status Monitor nel server](../../azure-monitor/app/monitor-performance-live-website-now.md) oppure [aggiornare l'applicazione a .NET Framework 4.6 o versione successiva](https://go.microsoft.com/fwlink/?LinkId=528259) e installare [Application Insights SDK](asp-net.md) nell'app. |
| App Web di Azure |[Aprire il pannello Application Insights nel pannello di controllo dell'app Web](../../azure-monitor/app/azure-web-apps.md) e scegliere Installa, se richiesto. |
| Servizio cloud di Azure |[Usare l'attività di avvio](../../azure-monitor/app/cloudservices.md) oppure [installare .NET Framework 4.6+](../../cloud-services/cloud-services-dotnet-install-dotnet.md). |

## <a name="where-to-find-dependency-data"></a>Dove trovare i dati sulle dipendenze
* La [mappa delle applicazioni](#application-map) visualizza le dipendenze tra l'app e i componenti adiacenti.
* I [pannelli Prestazioni, Browser ed Errori](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-performance) visualizzano i dati sulle dipendenze del server.
* Il [pannello Browser](#ajax-calls) visualizza le chiamate AJAX dai browser degli utenti.
* Fare clic sulle richieste lente o non riuscite per controllare le chiamate alle dipendenze.
* L'[analisi](#analytics) può essere usata per effettuare una query dei dati sulle dipendenze.

## <a name="application-map"></a>Mappa delle applicazioni
La mappa delle applicazioni funge da strumento visivo per individuare le dipendenze tra i componenti dell'applicazione. Viene generata automaticamente dai dati di telemetria provenienti dall'app. Questo esempio illustra le chiamate AJAX dagli script del browser e le chiamate REST dall'app server a due servizi esterni.

![Mappa delle applicazioni](./media/asp-net-dependencies/cloud-rolename.png)

* **Passare dalle caselle** alla dipendenza pertinente e ad altri grafici.
* **Aggiungere la mappa** al [dashboard](../../azure-monitor/app/app-insights-dashboards.md), dove sarà completamente funzionale.

[Altre informazioni](../../azure-monitor/app/app-map.md)

## <a name="performance-and-failure-blades"></a>Pannelli Prestazioni ed Errori
Il pannello Prestazioni visualizza la durata delle chiamate alle dipendenze effettuate dall'app server.

I **numeri di errori** sono visualizzati nel pannello **Errori**. Un errore è un codice restituito non compreso nell'intervallo 200-399 o sconosciuto.

> [!NOTE]
> **100% di errori** indica probabilmente che si stanno ricevendo solo dati parziali sulle dipendenze. È necessario [configurare il monitoraggio delle dipendenze appropriato per la piattaforma](#set-up-dependency-monitoring).
>
>

## <a name="ajax-calls"></a>Chiamate AJAX
Il pannello Browser visualizza la durata e la frequenza di errori delle chiamate AJAX da [JavaScript nelle pagine Web](../../azure-monitor/app/javascript.md). Sono visualizzate come dipendenze.

## <a name="diagnosis"></a> Diagnosticare le richieste lente
Ogni evento di richiesta associato con le chiamate di dipendenza, eccezioni e altri eventi registrati mentre l'app elabora la richiesta. Se quindi alcune richieste non vengono eseguite correttamente, è possibile capire se il problema è causato dalle risposte lente da una dipendenza.

### <a name="profile-your-live-site"></a>Profilatura del sito live

Se non si riesce a capire perché trascorre così tanto tempo, Il [di Application Insights profiler](../../azure-monitor/app/profiler.md) tracce HTTP chiama al sito live e Mostra le funzioni nel codice che richiedono più tempo.

## <a name="analytics"></a>Analytics
È possibile tenere traccia delle dipendenze nel [linguaggio di query Kusto](/azure/kusto/query/). Di seguito sono riportati alcuni esempi.

* Trovare eventuali chiamate alle dipendenze non riuscite:

```

    dependencies | where success != "True" | take 10
```

* Trovare le chiamate AJAX:

```

    dependencies | where client_Type == "Browser" | take 10
```

* Trovare le chiamate alle dipendenze associate alle richieste:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Trovare le chiamate AJAX associate alle visualizzazioni di pagina:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```



## <a name="custom-dependency-tracking"></a>Rilevamento personalizzato delle dipendenze
Il modulo standard per il rilevamento delle dipendenze rileva automaticamente le dipendenze esterne, ad esempio database e API REST. È tuttavia possibile che si vogliano gestire allo stesso modo altri componenti.

È possibile scrivere il codice che invia informazioni sulle dipendenze, mediante la stessa [API TrackDependency](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency) usata dai moduli standard.

Ad esempio, se si compila il codice con un assembly non scritto personalmente, sarà possibile misurare il tempo necessario per tutte le chiamate all'assembly, per individuare il contributo dell'assembly ai tempi di risposta. Per visualizzare i dati nei grafici relativi alle dipendenze in Application Insights, inviarli mediante `TrackDependency`.

```csharp

            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
                // The call above has been made obsolete in the latest SDK. The updated call follows this format:
                // TrackDependency (string dependencyTypeName, string dependencyName, string data, DateTimeOffset startTime, TimeSpan duration, bool success);
            }
```

Per disattivare il modulo standard per il rilevamento delle dipendenze, rimuovere il riferimento a DependencyTrackingTelemetryModule in [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

## <a name="troubleshooting"></a>risoluzione dei problemi
*Flag di operazione riuscita dipendenza sempre Mostra true o false.*

*Query SQL No visualizzate completamente.*

Consultare la tabella seguente e assicurarsi di aver scelto la configurazione corretta per abilitare il monitoraggio della dipendenza per l'applicazione.

| Piattaforma | Installa |
| --- | --- |
| Server IIS |O [installare Status Monitor nel server](../../azure-monitor/app/monitor-performance-live-website-now.md). Oppure [Aggiornare l'applicazione a .NET Framework 4.6 o versione successiva](https://go.microsoft.com/fwlink/?LinkId=528259) e installare [Application Insights SDK](asp-net.md) nell'app. |
| IIS Express |Usare invece il server IIS. |
| App Web di Azure |[Aprire il pannello Application Insights nel pannello di controllo dell'app Web](../../azure-monitor/app/azure-web-apps.md) e scegliere Installa, se richiesto. |
| Servizio cloud di Azure |[Usare l'attività di avvio](../../azure-monitor/app/cloudservices.md) oppure [installare .NET Framework 4.6+](../../cloud-services/cloud-services-dotnet-install-dotnet.md). |

## <a name="next-steps"></a>Passaggi successivi
* [Eccezioni](../../azure-monitor/app/asp-net-exceptions.md)
* [Dati utente e di pagina](../../azure-monitor/app/javascript.md)
* [Disponibilità](../../azure-monitor/app/monitor-web-app-availability.md)
