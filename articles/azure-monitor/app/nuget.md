---
title: Azure Application Insights - raccolta automatica delle dipendenze | Microsoft Docs
description: Application Insights raccoglie e visualizza automaticamente le dipendenze
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/16/2018
ms.author: mbullwin
ms.openlocfilehash: 3ad2f4788a765366066023724772f5432d0d56eb
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54109426"
---
# <a name="application-insights-nuget-packages"></a>Pacchetti NuGet per Application Insights

Di seguito è disponibile l'elenco corrente di pacchetti NuGet di versioni stabili per Application Insights.

## <a name="common-packages-for-aspnet"></a>Pacchetti comuni per ASP.NET

| Nome del pacchetto | Versione stabile | DESCRIZIONE | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.8.0 | Fornisce le funzionalità principali per la trasmissione di tutti i tipi di Application Insights Telemetry ed è un pacchetto dipendente per tutti gli altri pacchetti di Application Insights | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | Abilita l'intercettazione delle chiamate ai metodi | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | Agente di raccolta di dipendenze di Application Insights per le applicazioni .NET. È un pacchetto dipendente per i pacchetti specifici della piattaforma Application Insights e consente la raccolta automatica dei dati di telemetria delle dipendenze. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | L'agente di raccolta di Contatori delle prestazioni di Application Insights consente di inviare i dati raccolti da Contatori delle prestazioni ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.Web | 2.8.0 | Application Insights per le applicazioni Web .NET | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Il pacchetto NuGet Application Insights Windows Server consente la raccolta automatica dei dati di telemetria di Application Insights per le applicazioni .NET. Può essere usato come pacchetto dipendente per i pacchetti specifici della piattaforma Application Insights o come pacchetto autonomo per le applicazioni .NET non coperte dai pacchetti specifici della piattaforma (ad esempio, per i ruoli di lavoro .NET). | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | Fornisce ad Application Insights Windows Server SDK un canale di telemetria che manterrà di dati di telemetria negli scenari offline. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>Pacchetti comuni per ASP.NET Core

| Nome del pacchetto | Versione stabile | DESCRIZIONE | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Application Insights per applicazioni Web ASP.NET Core. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.8.0 | Questo pacchetto fornisce le funzionalità principali per la trasmissione di tutti i tipi di Application Insights Telemetry ed è un pacchetto dipendente per tutti gli altri pacchetti di Application Insights | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | Agente di raccolta di dipendenze di Application Insights per le applicazioni .NET. È un pacchetto dipendente per i pacchetti specifici della piattaforma Application Insights e consente la raccolta automatica dei dati di telemetria delle dipendenze. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | L'agente di raccolta di Contatori delle prestazioni di Application Insights consente di inviare i dati raccolti da Contatori delle prestazioni ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Il pacchetto NuGet Application Insights Windows Server consente la raccolta automatica dei dati di telemetria di Application Insights per le applicazioni .NET. Può essere usato come pacchetto dipendente per i pacchetti specifici della piattaforma Application Insights o come pacchetto autonomo per le applicazioni .NET non coperte dai pacchetti specifici della piattaforma (ad esempio, per i ruoli di lavoro .NET). | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | Fornisce ad Application Insights Windows Server SDK un canale di telemetria che manterrà di dati di telemetria negli scenari offline. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="listenerscollectorsappenders"></a>Listener/agenti di raccolta/appender

| Nome del pacchetto | Versione stabile | DESCRIZIONE | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  Consente di inoltrare gli eventi da DiagnosticSource ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | Application Insights EventSourceListener consente di inviare i dati dagli eventi di EventSource ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | Application Insights EtwCollector consente di inviare i dati da Event Tracing for Windows (ETW) ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | TraceListener personalizzato che consente di inviare i messaggi dei log di analisi ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | Appender personalizzato che consente di inviare i messaggi dei log di Log4Net ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  Destinazione personalizzata che consente di inviare i messaggi dei log di NLog ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | Monitora le eccezioni nell'applicazione e raccoglie automaticamente gli snapshot per l'analisi offline. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Nome del pacchetto | Versione stabile | DESCRIZIONE | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | Questo pacchetto fornisce la personalizzazione automatica dei dati di telemetria con il contesto di Service Fabric in cui è in esecuzione l'applicazione. Non usare questo pacchetto NuGet per le applicazioni di Service Fabric native. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Native | 2.2.0 | Modulo di Application Insights per le applicazioni di Service Fabric. Usare questo pacchetto NuGet solo per le applicazioni di Service Fabric native. Per le applicazioni in esecuzione nei contenitori, usare il pacchetto Microsoft.ApplicationInsights.ServiceFabric. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Status Monitor

| Nome del pacchetto | Versione stabile | DESCRIZIONE | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  Abilita la raccolta di dati di runtime per le applicazioni x64 | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  Abilita la raccolta di dati di runtime per le applicazioni x86. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Questi pacchetti costituiscono parte delle funzionalità principali del monitoraggio in fase di esecuzione in [Status Monitor](../../azure-monitor/app/monitor-performance-live-website-now.md). Non è necessario scaricare direttamente questi pacchetti. È sufficiente usare il programma di installazione di Status Monitor. Per altre informazioni sul funzionamento in background di questi pacchetti, vedere questo [post di blog](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/) di uno degli sviluppatori Microsoft.

## <a name="additional-packages"></a>Pacchetti aggiuntivi

| Nome del pacchetto | Versione stabile | DESCRIZIONE | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebSites | 2.6.5 | Questa estensione consente il monitoraggio di Application Insights su un servizio app di Azure. SDK versione 2.6.1. Istruzioni: aggiungere le impostazioni applicazione "APPINSIGHTS_INSTRUMENTATIONKEY" con la chiave di strumentazione (ikey) e riavviare l'app Web per renderle effettive.| [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injector | 2.6.7 | Questo pacchetto contiene i file necessari per l'inserimento di Application Insights senza codice | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Passaggi successivi

- Monitorare [ASP.NET Core](../../azure-monitor/app/asp-net-core.md).
- Profilare [app Web ASP.NET Core Linux di Azure](../../azure-monitor/app/profiler-aspnetcore-linux.md).
- Eseguire il debug di [snapshot](../../azure-monitor/app/snapshot-debugger.md) ASP.NET.