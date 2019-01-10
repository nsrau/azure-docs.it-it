---
title: Monitoraggio delle dipendenze in Azure Application Insights | Microsoft Docs
description: Analizzare l'uso, la disponibilità e le prestazioni dell'applicazione locale o Web di Microsoft Azure con Application Insights.
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
ms.openlocfilehash: 48e20df61844d83deb8ac992f783cf227e658aaf
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119986"
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
* I [pannelli Prestazioni, Browser ed Errori](#performance-and-blades) visualizzano i dati sulle dipendenze del server.
* Il [pannello Browser](#ajax-calls) visualizza le chiamate AJAX dai browser degli utenti.
* [Fare clic sulle richieste lente o non riuscite](#diagnose-slow-requests) per controllare le chiamate alle dipendenze.
* L'[analisi](#analytics) può essere usata per effettuare una query dei dati sulle dipendenze.

## <a name="application-map"></a>Mappa delle applicazioni
La mappa delle applicazioni funge da strumento visivo per individuare le dipendenze tra i componenti dell'applicazione. Viene generata automaticamente dai dati di telemetria provenienti dall'app. Questo esempio illustra le chiamate AJAX dagli script del browser e le chiamate REST dall'app server a due servizi esterni.

![Mappa delle applicazioni](./media/asp-net-dependencies/08.png)

* **Passare dalle caselle** alla dipendenza pertinente e ad altri grafici.
* **Aggiungere la mappa** al [dashboard](../../azure-monitor/app/app-insights-dashboards.md), dove sarà completamente funzionale.

[Altre informazioni](../../azure-monitor/app/app-map.md)

## <a name="performance-and-failure-blades"></a>Pannelli Prestazioni ed Errori
Il pannello Prestazioni visualizza la durata delle chiamate alle dipendenze effettuate dall'app server. Sono presenti un grafico di riepilogo e una tabella segmentata per chiamata.

![Grafici delle dipendenze nel pannello Prestazioni](./media/asp-net-dependencies/dependencies-in-performance-blade.png)

Fare clic sui grafici di riepilogo o sugli elementi della tabella per cercare le occorrenze non elaborate di queste chiamate.

![Istanze delle chiamate alle dipendenze](./media/asp-net-dependencies/dependency-call-instance.png)

I **numeri di errori** sono visualizzati nel pannello **Errori**. Un errore è un codice restituito non compreso nell'intervallo 200-399 o sconosciuto.

> [!NOTE]
> Il **100% di errori** indica probabilmente che si stanno ricevendo solo dati parziali sulle dipendenze. È necessario [configurare il monitoraggio delle dipendenze appropriato per la piattaforma](#set-up-dependency-monitoring).
>
>

## <a name="ajax-calls"></a>Chiamate AJAX
Il pannello Browser visualizza la durata e la frequenza di errori delle chiamate AJAX da [JavaScript nelle pagine Web](../../azure-monitor/app/javascript.md). Sono visualizzate come dipendenze.

## <a name="diagnosis"></a> Diagnosticare le richieste lente
Ogni evento di richiesta è associato alle chiamate alle dipendenze, alle eccezioni e ad altri eventi registrati mentre l'app elabora la richiesta. Se quindi alcune richieste non vengono eseguite correttamente, è possibile capire se il problema è causato dalle risposte lente da una dipendenza.

Di seguito è illustrato un esempio.

### <a name="tracing-from-requests-to-dependencies"></a>Traccia dalle richieste alle dipendenze
Aprire il pannello Prestazioni ed esaminare la griglia delle richieste:

![Elenco di richieste con conteggi e medie](./media/asp-net-dependencies/02-reqs.png)

Quella superiore impiega molto tempo. È necessario indagare per scoprire in che modo viene impiegato il tempo.

Fare clic su tale riga per visualizzare gli eventi di richiesta singola:

![Elenco di occorrenze di richiesta](./media/asp-net-dependencies/03-instances.png)

Fare clic su un'istanza a esecuzione prolungata per esaminarla meglio e scorrere in basso fino alle chiamate alle dipendenze remote correlate a questa richiesta:

![Trovare le chiamate alle dipendenze remote, identificare una durata insolita](./media/asp-net-dependencies/04-dependencies.png)

Sembra che gran parte del tempo dedicato a questa richiesta sia stato impiegato in una chiamata a un servizio locale.

Selezionare la riga per ottenere altre informazioni:

![Fare clic su tale dipendenza remota per identificare il motivo del problema](./media/asp-net-dependencies/05-detail.png)

L'origine del problema è simile a questa. Dopo avere individuato il problema, rimane solo da capire perché la chiamata sta durando così tanto.

### <a name="request-timeline"></a>Sequenza temporale della richiesta
In un altro caso non ci sono chiamate a dipendenze particolarmente lunghe, ma, passando alla visualizzazione della sequenza temporale, è possibile vedere il punto in cui si è verificato il ritardo nell'elaborazione interna:

![Trovare le chiamate alle dipendenze remote, identificare una durata insolita](./media/asp-net-dependencies/04-1.png)

Dopo la prima chiamata alla dipendenza si verifica una lunga pausa, quindi è opportuno esaminare il codice per capirne il motivo.

### <a name="profile-your-live-site"></a>Profilatura del sito live

Se non si riesce a capire perché trascorre così tanto tempo, il [profiler di Application Insights](../../azure-monitor/app/profiler.md) traccia le chiamate HTTP al sito live e mostra le funzioni del codice che richiedono più tempo.

## <a name="failed-requests"></a>Richieste non riuscite
Le richieste non riuscite possono anche essere associate a chiamate non riuscite a dipendenze. Anche in questo caso è possibile fare clic per risalire al problema.

![Fare clic sul grafico delle richieste non riuscite](./media/asp-net-dependencies/06-fail.png)

Fare clic su un'occorrenza di una richiesta non riuscita ed esaminare gli eventi associati.

![Fare clic su un tipo di richiesta, fare clic sull'istanza per ottenere una vista diversa della stessa istanza, fare clic su di essa per ottenere informazioni dettagliate sull'eccezione.](./media/asp-net-dependencies/07-faildetail.png)

## <a name="analytics"></a>Analytics
È possibile tenere traccia delle dipendenze nel [linguaggio di query di Log Analytics](https://aka.ms/LogAnalyticsLanguage). Di seguito sono riportati alcuni esempi.

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
*Il flag di operazione riuscita della dipendenza visualizza sempre true o false.*

*Query SQL no visualizzate completamente.*

Consultare la tabella seguente e assicurarsi di aver scelto la configurazione corretta per abilitare il monitoraggio della dipendenza per l'applicazione.

| Piattaforma | Installa |
| --- | --- |
| Server IIS |O [installare Status Monitor nel server](../../azure-monitor/app/monitor-performance-live-website-now.md). Oppure [Aggiornare l'applicazione a .NET Framework 4.6 o versione successiva](https://go.microsoft.com/fwlink/?LinkId=528259) e installare [Application Insights SDK](asp-net.md) nell'app. |
| IIS Express |Usare invece il server IIS. |
| App Web di Azure |[Aprire il pannello Application Insights nel pannello di controllo dell'app Web](../../azure-monitor/app/azure-web-apps.md) e scegliere Installa, se richiesto. |
| Servizio cloud di Azure |[Usare l'attività di avvio](../../azure-monitor/app/cloudservices.md) oppure [installare .NET Framework 4.6+](../../cloud-services/cloud-services-dotnet-install-dotnet.md). |

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passaggi successivi
* [Eccezioni](../../azure-monitor/app/asp-net-exceptions.md)
* [Dati utente e di pagina](../../azure-monitor/app/javascript.md)
* [Disponibilità](../../azure-monitor/app/monitor-web-app-availability.md)
