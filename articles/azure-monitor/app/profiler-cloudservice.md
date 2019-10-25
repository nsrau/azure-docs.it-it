---
title: Profilare i servizi cloud di Azure attivi con Application Insights | Microsoft Docs
description: Abilitare Application Insights Profiler per i servizi cloud.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 682711d7681e3646ae14686b01542bc5d7432179
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820506"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profilare i servizi cloud di Azure attivi con Application Insights

È anche possibile distribuire Application Insights Profiler in questi servizi:
* [Informazioni sul servizio app di Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Applicazioni Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Macchine virtuali di Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler viene installato con l'estensione Diagnostica di Microsoft Azure. È sufficiente configurare Diagnostica di Azure per installare Profiler e inviare i profili alla risorsa di Application Insights.

## <a name="enable-profiler-for-azure-cloud-services"></a>Abilitare Profiler per il servizio cloud di Azure
1. Assicurarsi di usare [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) o versione successiva. Se si usa la famiglia di sistemi operativi 4, è necessario installare .NET Framework 4.6.1 o versione successiva con un' [attività di avvio](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-install-dotnet). La famiglia di sistemi operativi 5 include una versione compatibile di .NET Framework per impostazione predefinita. 

1. Aggiungere [Application Insights SDK ai servizi cloud di Azure](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).

    **Il bug nel profiler fornito in WAD per i servizi cloud è stato risolto.** La versione più recente di WAD (1.12.2.0) per i servizi cloud funziona con tutte le versioni recenti di App Insights SDK. Gli host del servizio cloud eseguiranno l'aggiornamento automatico di WAD, ma non è immediato. Per forzare un aggiornamento, è possibile ridistribuire il servizio o riavviare il nodo.

1. Tenere traccia delle richieste con Application Insights:

    * Per i ruoli Web ASP.Net, Application Insights può rilevare automaticamente le richieste.

    * Per i ruoli di lavoro, [aggiungere codice per tenere traccia delle richieste](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Configurare l'estensione Diagnostica di Azure per abilitare Profiler:

    a. Individuare il file di [Diagnostica di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* per il ruolo dell'applicazione, come illustrato di seguito:  

      ![Percorso del file di configurazione di diagnostica](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Se il file non si trova, vedere [Configurare la diagnostica per i servizi cloud e le macchine virtuali di Azure](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

    b. Aggiungere la sezione `SinksConfig` seguente come elemento figlio di `WadCfg`:  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

    > [!NOTE]
    > Se il file *diagnostics.wadcfgx* contiene anche un altro sink di tipo ApplicationInsights, tutte e tre le chiavi di strumentazione seguenti devono corrispondere:  
    > * Chiave usata dall'applicazione. 
    > * Chiave che viene usata dal sink di ApplicationInsights. 
    > * Chiave che viene usata dal sink di ApplicationInsightsProfiler. 
    >
    > È possibile trovare il valore effettivo della chiave di strumentazione usata dal sink `ApplicationInsights` nei file *ServiceConfiguration.\*.cscfg*. 
    > In seguito al rilascio dell'SDK di Visual Studio 15.5 Azure, solo le chiavi di strumentazione usate dall'applicazione e dal sink ApplicationInsightProfiler devono corrispondere.

1. Distribuire il servizio con la nuova configurazione di diagnostica e Application Insights Profiler verrà configurato per l'esecuzione nel servizio.
 
## <a name="next-steps"></a>Passaggi successivi

* Generare traffico verso l'applicazione (ad esempio, avviare un [test di disponibilità](monitor-web-app-availability.md)). Attendere quindi 10-15 minuti perché le tracce inizino ad essere inviate all'istanza di Application Insights.
* Vedere [Tracce Profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) nel portale di Azure.
* Per informazioni sulla risoluzione dei problemi del Profiler, vedere [Risoluzione dei problemi di Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
