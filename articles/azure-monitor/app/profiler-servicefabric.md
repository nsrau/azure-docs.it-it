---
title: Profilare le app di Azure Service Fabric attive con Application Insights
description: Abilitare Profiler per un'applicazione di Service Fabric
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 664d6eb377185613a1a5670daf6747b482c79d9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671614"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Profilare le applicazioni di Azure Service Fabric attive con Application Insights

È anche possibile distribuire Application Insights Profiler in questi servizi:
* [Servizio app di Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Servizi cloud di AzureAzure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Macchine virtuali di Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Configurare la definizione di distribuzione dell'ambiente

Application Insights Profiler è incluso in Diagnostica di Azure. È possibile installare l'estensione Diagnostica di Azure usando un modello di Azure Resource Manager per il cluster di Service Fabric. Ottenere un [modello di installazione di Diagnostica di Azure in un cluster di Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Per configurare l'ambiente, seguire questa procedura:

1. Profiler supporta .NET Framework e .Net Core. Se si utilizza .NET Framework, assicurarsi di utilizzare [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) o versione successiva. È sufficiente confermare che il sistema `Windows Server 2012 R2` operativo distribuito è o è successivo. Profiler supporta .NET Core 2.1 e applicazioni più recenti.

1. Cercare l'estensione di [Diagnostica di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) nel file del modello di distribuzione.

1. Aggiungere la sezione `SinksConfig` seguente come elemento figlio di `WadCfg`. sostituendo il valore della proprietà `ApplicationInsightsProfiler` con la chiave di strumentazione di Application Insights:  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      Per informazioni sull'aggiunta dell'estensione Diagnostica al modello di distribuzione, vedere [Usare monitoraggio e diagnostica con una macchina virtuale Windows e modelli di Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

1. Distribuire il cluster di Service Fabric usando il modello di Azure Resource Manager.  
  Se le impostazioni sono corrette, Application Insights Profiler verrà installato e abilitato all'installazione dell'estensione Diagnostica di Azure. 

1. Aggiungere Application Insights all'applicazione di Service Fabric.  
  For Profiler to collect profiles for your requests, your application must be tracking operations with Application Insights. Per le API senza stato, è possibile fare riferimento alle istruzioni per [tenere traccia delle richieste di profilatura](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json). Per altre informazioni sul rilevamento di operazioni personalizzate in altri tipi di app, vedere [Tenere traccia delle operazioni personalizzate con Application Insights .NET SDK.](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json)

1. Ridistribuire l'applicazione.


## <a name="next-steps"></a>Passaggi successivi

* Generare traffico verso l'applicazione (ad esempio, avviare un [test di disponibilità](monitor-web-app-availability.md)). Attendere quindi 10-15 minuti perché le tracce inizino ad essere inviate all'istanza di Application Insights.
* Vedere [Tracce Profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) nel portale di Azure.
* Per informazioni sulla risoluzione dei problemi di Profiler, vedere [Risoluzione dei problemi di Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
