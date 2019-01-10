---
title: Profilare i servizi cloud di Azure attivi con Application Insights | Microsoft Docs
description: Abilitare Application Insights Profiler per i servizi cloud.
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
ms.openlocfilehash: 6ae662c57c5196ff495edafeee0d6ba5f79e76d1
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54082368"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profilare i servizi cloud di Azure attivi con Application Insights

È anche possibile distribuire Application Insights Profiler in questi servizi:
* [Servizio app di Azure](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Applicazioni di Service Fabric](profiler-servicefabric.md ?toc=/azure/azure-monitor/toc.json)
* [Macchine virtuali](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler viene installato con l'estensione Diagnostica di Microsoft Azure. È sufficiente configurare Diagnostica di Microsoft Azure per installare il profiler e inviare i profili alla risorsa di Application Insights.

## <a name="enable-profiler-for-your-azure-cloud-service"></a>Abilitare il profiler per il servizio cloud di Azure
1. Verificare che [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) o versione successiva sia in uso.  È sufficiente confermare che i file *ServiceConfiguration.\*.cscfg* abbiano un valore di `osFamily` pari a "5" o maggiore.
1. Aggiungere [Application Insights SDK al servizio cloud](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).
1. Tenere traccia delle richieste con Application Insights:

    Per i ruoli Web ASP.Net, Application Insights può rilevare automaticamente le richieste.

    Per i ruoli di lavoro, [aggiungere codice per tenere traccia delle richieste](profiler-trackrequests.md ?toc=/azure/azure-monitor/toc.json).

    

1. Configurare l'estensione Diagnostica di Microsoft Azure per abilitare il profiler.



    1. Individuare il file di [Diagnostica di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* per il ruolo dell'applicazione, come illustrato di seguito:  

       ![Percorso del file di configurazione di diagnostica](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

        Se non si trova il file, per informazioni su come abilitare l'estensione Diagnostica nel progetto di Servizi cloud di Azure, vedere [Configurare la diagnostica per i servizi cloud e le macchine virtuali di Azure](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

    1. Aggiungere la sezione `SinksConfig` seguente come elemento figlio di `WadCfg`:  

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

    >   **NOTA:** Se il file diagnostics.wadcfgx* contiene anche un atro sink di tipo `ApplicationInsights`, tutte e tre le chiavi di strumentazione seguenti devono corrispondere:  
    >  * Chiave usata dall'applicazione.  
    >  * Chiave usata dal sink `ApplicationInsights`.  
    >  * Chiave usata dal sink `ApplicationInsightsProfiler`.  
    >
    > È possibile trovare il valore effettivo della chiave di strumentazione usata dal sink `ApplicationInsights` nei file *ServiceConfiguration.\*.cscfg*.  
    > In seguito al rilascio di Visual Studio 15.5 Azure SDK, solo le chiavi di strumentazione usate dall'applicazione e dal sink `ApplicationInsightsProfiler` devono corrispondere.
1. Distribuire il servizio con la nuova configurazione di diagnostica e Application Insights Profiler verrà configurato per l'esecuzione nel servizio.
 
## <a name="next-steps"></a>Passaggi successivi

- Generare traffico verso l'applicazione (ad esempio, avviare un [test di disponibilità](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Attendere quindi 10-15 minuti perché le tracce inizino ad essere inviate all'istanza di Application Insights.
- Vedere [Tracce Profiler](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) nel portale di Azure.
- Per informazioni su come risolvere i problemi relativi a Profiler, vedere [Risoluzione dei problemi del profiler](profiler-troubleshooting.md ?toc=/azure/azure-monitor/toc.json).