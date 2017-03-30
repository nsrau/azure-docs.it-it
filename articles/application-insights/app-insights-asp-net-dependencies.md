---
title: Monitoraggio delle dipendenze in Azure Application Insights | Microsoft Docs
description: "Analizzare l&quot;uso, la disponibilità e le prestazioni dell&quot;applicazione locale o Web di Microsoft Azure con Application Insights."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: 35817adde713995ec82eead033f058ee109bf900
ms.lasthandoff: 03/16/2017


---
# <a name="set-up-application-insights-dependency-tracking"></a>Impostare Application Insights: tenere traccia delle dipendenze
Una *dipendenza* è un componente esterno chiamato dall'app. In genere è un servizio chiamato con il protocollo HTTP, oppure un database o un file system. [Application Insights](app-insights-overview.md) misura per quanto tempo l'applicazione attende le dipendenze e la frequenza con la quale una chiamata alle dipendenze non riesce. È possibile esaminare chiamate specifiche e correlarle a richieste ed eccezioni.

![grafici di esempio](./media/app-insights-asp-net-dependencies/10-intro.png)

Il monitoraggio predefinito delle dipendenze attualmente segnala chiamate ai seguenti tipi di dipendenze:

* Server
  * Database SQL
  * Servizi Web ASP.NET e WCF che usano i binding basati su HTTP
  * Chiamate HTTP locali o remote
  * Azure DocumentDB, tabelle, archivio BLOB e coda
* Pagina Web
  * Chiamate AJAX

Il monitoraggio funziona tramite l'uso di [strumentazione con codice byte](https://msdn.microsoft.com/library/z9z62c29.aspx) basata su determinati metodo. L'overhead delle prestazioni è minimo.

È anche possibile scrivere chiamate SDK per monitorare altre dipendenze, sia nel codice client che nel codice server, usando l'[API TrackDependency](app-insights-api-custom-events-metrics.md#trackdependency).

## <a name="set-up-dependency-monitoring"></a>Configurare il monitoraggio delle dipendenze
Informazioni sulle dipendenze parziali vengono raccolte automaticamente da [Application Insights SDK](app-insights-asp-net.md). Per ottenere dati completi, installare l'agente appropriato per il server host.

| Piattaforma | Installa |
| --- | --- |
| Server IIS |[Installare Status Monitor nel server](app-insights-monitor-performance-live-website-now.md) oppure [aggiornare l'applicazione a .NET Framework 4.6 o versione successiva](http://go.microsoft.com/fwlink/?LinkId=528259) e installare [Application Insights SDK](app-insights-asp-net.md) nell'app. |
| App Web di Azure |[Aprire il pannello Application Insights nel pannello di controllo dell'app Web](app-insights-azure-web-apps.md) e scegliere Installa, se richiesto. |
| Servizio cloud di Azure |[Usare l'attività di avvio](app-insights-cloudservices.md) oppure [installare .NET Framework 4.6+](../cloud-services/cloud-services-dotnet-install-dotnet.md). |

## <a name="where-to-find-dependency-data"></a>Dove trovare i dati sulle dipendenze
* La [mappa delle applicazioni](#application-map) visualizza le dipendenze tra l'app e i componenti adiacenti.
* I [pannelli Prestazioni, Browser ed Errori](#performance-and-blades) visualizzano i dati sulle dipendenze del server.
* Il [pannello Browser](#ajax-calls) visualizza le chiamate AJAX dai browser degli utenti.
* [Fare clic sulle richieste lente o non riuscite](#diagnose-slow-requests) per controllare le chiamate alle dipendenze.
* L'[analisi](#analytics) può essere usata per effettuare una query dei dati sulle dipendenze.

## <a name="application-map"></a>Mappa delle applicazioni
La mappa delle applicazioni funge da strumento visivo per individuare le dipendenze tra i componenti dell'applicazione. Viene generata automaticamente dai dati di telemetria provenienti dall'app. Questo esempio illustra le chiamate AJAX dagli script del browser e le chiamate REST dall'app server a due servizi esterni.

![Mappa delle applicazioni](./media/app-insights-asp-net-dependencies/08.png)

* **Passare dalle caselle** alla dipendenza pertinente e ad altri grafici.
* **Aggiungere la mappa** al [dashboard](app-insights-dashboards.md), dove sarà completamente funzionale.

[Altre informazioni](app-insights-app-map.md)

## <a name="performance-and-failure-blades"></a>Pannelli Prestazioni ed Errori
Il pannello Prestazioni visualizza la durata delle chiamate alle dipendenze effettuate dall'app server. Sono presenti un grafico di riepilogo e una tabella segmentata per chiamata.

![Grafici delle dipendenze nel pannello Prestazioni](./media/app-insights-asp-net-dependencies/dependencies-in-performance-blade.png)

Fare clic sui grafici di riepilogo o sugli elementi della tabella per cercare le occorrenze non elaborate di queste chiamate.

![Istanze delle chiamate alle dipendenze](./media/app-insights-asp-net-dependencies/dependency-call-instance.png)

I **numeri di errori** sono visualizzati nel pannello **Errori**. Un errore è un codice restituito non compreso nell'intervallo 200-399 o sconosciuto.

> [!NOTE]
> Il **100% di errori** indica probabilmente che si stanno ricevendo solo dati parziali sulle dipendenze. È necessario [configurare il monitoraggio delle dipendenze appropriato per la piattaforma](#set-up-dependency-monitoring).
>
>

## <a name="ajax-calls"></a>Chiamate AJAX
Il pannello Browser visualizza la durata e la frequenza di errori delle chiamate AJAX da [JavaScript nelle pagine Web](app-insights-javascript.md). Sono visualizzate come dipendenze.

## <a name="diagnosis"></a> Diagnosticare le richieste lente
Ogni evento di richiesta è associato alle chiamate alle dipendenze, alle eccezioni e ad altri eventi registrati mentre l'app elabora la richiesta. Se quindi alcune richieste non vengono eseguite correttamente, è possibile capire se il problema è causato dalle risposte lente da una dipendenza.

Di seguito è illustrato un esempio.

### <a name="tracing-from-requests-to-dependencies"></a>Traccia dalle richieste alle dipendenze
Aprire il pannello Prestazioni ed esaminare la griglia delle richieste:

![Elenco di richieste con conteggi e medie](./media/app-insights-asp-net-dependencies/02-reqs.png)

Quella superiore impiega molto tempo. È necessario indagare per scoprire in che modo viene impiegato il tempo.

Fare clic su tale riga per visualizzare gli eventi di richiesta singola:

![Elenco di occorrenze di richiesta](./media/app-insights-asp-net-dependencies/03-instances.png)

Fare clic su un'istanza a esecuzione prolungata per esaminarla meglio e scorrere in basso fino alle chiamate alle dipendenze remote correlate a questa richiesta:

![Trovare le chiamate alle dipendenze remote, identificare una durata insolita](./media/app-insights-asp-net-dependencies/04-dependencies.png)

Sembra che gran parte del tempo dedicato a questa richiesta sia stato impiegato in una chiamata a un servizio locale.

Selezionare la riga per ottenere altre informazioni:

![Fare clic su tale dipendenza remota per identificare il motivo del problema](./media/app-insights-asp-net-dependencies/05-detail.png)

L'origine del problema è simile a questa. Dopo avere individuato il problema, rimane solo da capire perché la chiamata sta durando così tanto.

### <a name="request-timeline"></a>Sequenza temporale della richiesta
In un altro caso non ci sono chiamate a dipendenze particolarmente lunghe, ma, passando alla visualizzazione della sequenza temporale, è possibile vedere il punto in cui si è verificato il ritardo nell'elaborazione interna:

![Trovare le chiamate alle dipendenze remote, identificare una durata insolita](./media/app-insights-asp-net-dependencies/04-1.png)

Dopo la prima chiamata alla dipendenza si verifica una lunga pausa, quindi è opportuno esaminare il codice per capirne il motivo.

### <a name="profiling-your-live-site"></a>Profilatura del sito live

Se non si riesce a capire perché trascorre così tanto tempo, il profiler di Application Insights traccerà le chiamate HTTP al sito live e visualizzerà le funzioni del codice che richiedono più tempo. Il profiler è attualmente in anteprima limitata. È possibile [iscriversi per provarlo](https://aka.ms/AIProfilerPreview).

## <a name="failed-requests"></a>Richieste non riuscite
Le richieste non riuscite possono anche essere associate a chiamate non riuscite a dipendenze. Anche in questo caso è possibile fare clic per risalire al problema.

![Fare clic sul grafico delle richieste non riuscite](./media/app-insights-asp-net-dependencies/06-fail.png)

Fare clic su un'occorrenza di una richiesta non riuscita ed esaminare gli eventi associati.

![Fare clic su un tipo di richiesta, fare clic sull'istanza per ottenere una vista diversa della stessa istanza, fare clic su di essa per ottenere informazioni dettagliate sull'eccezione.](./media/app-insights-asp-net-dependencies/07-faildetail.png)

## <a name="analytics"></a>Analytics
È possibile tenere traccia delle dipendenze nel [linguaggio di query di Analytics](app-insights-analytics.md). Di seguito sono riportati alcuni esempi.

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

È possibile scrivere il codice che invia informazioni sulle dipendenze, mediante la stessa [API TrackDependency](app-insights-api-custom-events-metrics.md#trackdependency) usata dai moduli standard.

Ad esempio, se si compila il codice con un assembly non scritto personalmente, sarà possibile misurare il tempo necessario per tutte le chiamate all'assembly, per individuare il contributo dell'assembly ai tempi di risposta. Per visualizzare i dati nei grafici relativi alle dipendenze in Application Insights, inviarli mediante `TrackDependency`.

```C#

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
            }
```

Per disattivare il modulo standard per il rilevamento delle dipendenze, rimuovere il riferimento a DependencyTrackingTelemetryModule in [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

## <a name="troubleshooting"></a>Risoluzione dei problemi
*Il flag di operazione riuscita della dipendenza visualizza sempre true o false.*

*Query SQL no visualizzate completamente.*

* Eseguire l'aggiornamento alla versione più recente dell'SDK. Se la versione di .NET è precedente alla 4.6:
  * Host IIS: installare l'[agente di Application Insights](app-insights-monitor-performance-live-website-now.md) nei server host.
  * App Web di Azure: aprire la scheda Application Insights nel pannello di controllo dell'app Web e installare Application Insights.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passaggi successivi
* [Eccezioni](app-insights-asp-net-exceptions.md)
* [Dati utente e di pagina](app-insights-javascript.md)
* [Disponibilità](app-insights-monitor-web-app-availability.md)

