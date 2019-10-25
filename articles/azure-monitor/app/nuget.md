---
title: Azure Application Insights - raccolta automatica delle dipendenze | Microsoft Docs
description: Application Insights raccoglie e visualizza automaticamente le dipendenze
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: reference
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/16/2018
ms.openlocfilehash: 1d3259c32de7f937d4e5ac8e21c8cab60b9cc635
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819166"
---
# <a name="application-insights-nuget-packages"></a>Pacchetti NuGet per Application Insights

Di seguito è disponibile l'elenco corrente di pacchetti NuGet di versioni stabili per Application Insights.

## <a name="common-packages-for-aspnet"></a>Pacchetti comuni per ASP.NET

| Nome del pacchetto | Versione stabile | Description | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.8.0 | Fornisce le funzionalità principali per la trasmissione di tutti i tipi di Application Insights Telemetry ed è un pacchetto dipendente per tutti gli altri pacchetti di Application Insights | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | Abilita l'intercettazione delle chiamate ai metodi | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | Agente di raccolta di dipendenze di Application Insights per le applicazioni .NET. È un pacchetto dipendente per i pacchetti specifici della piattaforma Application Insights e consente la raccolta automatica dei dati di telemetria delle dipendenze. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | L'agente di raccolta di Contatori delle prestazioni di Application Insights consente di inviare i dati raccolti da Contatori delle prestazioni ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.Web | 2.8.0 | Application Insights per le applicazioni Web .NET | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Il pacchetto NuGet Application Insights Windows Server consente la raccolta automatica dei dati di telemetria di Application Insights per le applicazioni .NET. Può essere usato come pacchetto dipendente per i pacchetti specifici della piattaforma Application Insights o come pacchetto autonomo per le applicazioni .NET non coperte dai pacchetti specifici della piattaforma (ad esempio, per i ruoli di lavoro .NET). | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | Fornisce ad Application Insights Windows Server SDK un canale di telemetria che manterrà di dati di telemetria negli scenari offline. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>Pacchetti comuni per ASP.NET Core

| Nome del pacchetto | Versione stabile | Description | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Application Insights per applicazioni Web ASP.NET Core. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.8.0 | Questo pacchetto fornisce le funzionalità principali per la trasmissione di tutti i tipi di Application Insights Telemetry ed è un pacchetto dipendente per tutti gli altri pacchetti di Application Insights | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | Agente di raccolta di dipendenze di Application Insights per le applicazioni .NET. È un pacchetto dipendente per i pacchetti specifici della piattaforma Application Insights e consente la raccolta automatica dei dati di telemetria delle dipendenze. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | L'agente di raccolta di Contatori delle prestazioni di Application Insights consente di inviare i dati raccolti da Contatori delle prestazioni ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Il pacchetto NuGet Application Insights Windows Server consente la raccolta automatica dei dati di telemetria di Application Insights per le applicazioni .NET. Può essere usato come pacchetto dipendente per i pacchetti specifici della piattaforma Application Insights o come pacchetto autonomo per le applicazioni .NET non coperte dai pacchetti specifici della piattaforma (ad esempio, per i ruoli di lavoro .NET). | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | Fornisce ad Application Insights Windows Server SDK un canale di telemetria che manterrà di dati di telemetria negli scenari offline. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-python-using-opencensus"></a>Pacchetti comuni per Python con OpenCensus
| Nome del pacchetto | Versione stabile | Description | Download |
|-------------------------------|-----------------------|------------|----|
| opencensus-EXT-Azure | 1.0.0 | Application Insights per le applicazioni Python in monitoraggio di Azure tramite OpenCensus. | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-azure/) |
| opencensus-EXT-Django | 0.7.2 | Questo pacchetto fornisce l'integrazione con la libreria [Django](https://pypi.org/project/django/) di Python. | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-django/) |
| opencensus-EXT-Flask | 0.7.3 | Questo pacchetto fornisce l'integrazione con la libreria Python [Flask](https://pypi.org/project/flask/) . | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-flask/) |
| opencensus-EXT-HttpLib | 0.7.2 | Questo pacchetto fornisce l'integrazione con la libreria [http. client](https://docs.python.org/3/library/http.client.html) Python per Python3 e [HttpLib](https://docs.python.org/2/library/httplib.html) per python2. | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-httplib/) |
| opencensus-EXT-registrazione | 0.1.0 | Questo pacchetto arricchisce i record di log con i dati di traccia. | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-logging/) |
| opencensus-ext-mysql | 0.1.2 | Questo pacchetto fornisce l'integrazione con la libreria Python [MySQL-Connector](https://pypi.org/project/mysql-connector/) . | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-mysql/) |
| opencensus-EXT-PostgreSQL | 0.1.2 | Questo pacchetto fornisce l'integrazione con la libreria [psycopg2](https://pypi.org/project/psycopg2/) di Python. | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-postgresql/) |
| opencensus-EXT-pymongo | 0.7.1 | Questo pacchetto fornisce l'integrazione con la libreria [pymongo](https://pypi.org/project/pymongo/) di Python. | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-pymongo/) |
| opencensus-EXT-pymysql | 0.1.2 | Questo pacchetto fornisce l'integrazione con la libreria [PyMySQL](https://pypi.org/project/PyMySQL/) di Python. | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-pymysql/) |
| opencensus-EXT-Pyramid | 0.7.1 | Questo pacchetto fornisce l'integrazione con la libreria [piramidale](https://pypi.org/project/pyramid/) di Python. | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-pyramid/) |
| opencensus-EXT-richieste | 0.7.2 | Questo pacchetto fornisce l'integrazione con la libreria delle [richieste](https://pypi.org/project/requests/) di Python. | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-requests/) |
| opencensus-EXT-SQLAlchemy | 0.1.2 | Questo pacchetto fornisce l'integrazione con la libreria Python [SQLAlchemy](https://pypi.org/project/SQLAlchemy/) . | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-sqlalchemy/) |

## <a name="listenerscollectorsappenders"></a>Listener/agenti di raccolta/appender

| Nome del pacchetto | Versione stabile | Description | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  Consente di inoltrare gli eventi da DiagnosticSource ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | Application Insights EventSourceListener consente di inviare i dati dagli eventi di EventSource ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | Application Insights EtwCollector consente di inviare i dati da Event Tracing for Windows (ETW) ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | TraceListener personalizzato che consente di inviare i messaggi dei log di analisi ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | Appender personalizzato che consente di inviare i messaggi dei log di Log4Net ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  Destinazione personalizzata che consente di inviare i messaggi dei log di NLog ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | Monitora le eccezioni nell'applicazione e raccoglie automaticamente gli snapshot per l'analisi offline. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Nome del pacchetto | Versione stabile | Description | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | Questo pacchetto fornisce la personalizzazione automatica dei dati di telemetria con il contesto di Service Fabric in cui è in esecuzione l'applicazione. Non usare questo pacchetto NuGet per le applicazioni di Service Fabric native. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Native | 2.2.0 | Modulo di Application Insights per le applicazioni di Service Fabric. Usare questo pacchetto NuGet solo per le applicazioni di Service Fabric native. Per le applicazioni in esecuzione nei contenitori, usare il pacchetto Microsoft.ApplicationInsights.ServiceFabric. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Status Monitor

| Nome del pacchetto | Versione stabile | Description | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  Abilita la raccolta di dati di runtime per le applicazioni x64 | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  Abilita la raccolta di dati di runtime per le applicazioni x86. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Questi pacchetti costituiscono parte delle funzionalità principali del monitoraggio in fase di esecuzione in [Status Monitor](../../azure-monitor/app/monitor-performance-live-website-now.md). Non è necessario scaricare direttamente questi pacchetti. È sufficiente usare il programma di installazione di Status Monitor. Per altre informazioni sul funzionamento in background di questi pacchetti, vedere questo [post di blog](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/) di uno degli sviluppatori Microsoft.

## <a name="additional-packages"></a>Pacchetti aggiuntivi

| Nome del pacchetto | Versione stabile | Description | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebSites | 2.6.5 | Questa estensione consente il monitoraggio di Application Insights su un servizio app di Azure. SDK versione 2.6.1. Istruzioni: aggiungere le impostazioni applicazione "APPINSIGHTS_INSTRUMENTATIONKEY" con la chiave di strumentazione e riavviare l'app Web per renderle effettive.| [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injector | 2.6.7 | Questo pacchetto contiene i file necessari per l'inserimento di Application Insights senza codice | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Passaggi successivi

- Monitorare [ASP.NET Core](../../azure-monitor/app/asp-net-core.md).
- Profilare [app Web ASP.NET Core Linux di Azure](../../azure-monitor/app/profiler-aspnetcore-linux.md).
- Eseguire il debug di [snapshot](../../azure-monitor/app/snapshot-debugger.md) ASP.NET.