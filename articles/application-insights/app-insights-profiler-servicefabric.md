---
title: Profilare le applicazioni di Azure Service Fabric attive con Application Insights | Microsoft Docs
description: Abilitare il profiler per un'applicazione di Service Fabric
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 071d1acb592220f7da83a47fd7dffa757ef81a95
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142865"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Profilare le applicazioni di Azure Service Fabric attive con Application Insights

È anche possibile distribuire Application Insights Profiler in questi servizi:
* [App Web di Azure](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Servizi cloud](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Macchine virtuali](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


## <a name="set-up-the-environment-deployment-definition"></a>Configurare la definizione di distribuzione dell'ambiente

Application Insights Profiler è incluso nell'estensione Diagnostica di Microsoft Azure. L'estensione Diagnostica di Microsoft Azure può essere installata usando un modello di Azure Resource Manager per il cluster di Service Fabric. Ecco un modello di esempio: [**Modello di installazione di Diagnostica di Microsoft Azure in un cluster di Service Fabric**](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Per configurare l'ambiente, seguire questa procedura:
1. Per verificare che sia in uso [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) o versione successiva, è sufficiente controllare che il sistema operativo distribuito sia `Windows Server 2012 R2` o versione successiva.

1. Cercare l'estensione [Diagnostica di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) nel file del modello di distribuzione e quindi aggiungere la sezione `SinksConfig` seguente come elemento figlio di `WadCfg`. sostituendo il valore della proprietà `ApplicationInsightsProfiler` con la chiave di strumentazione di Application Insights:  

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
1. Distribuire il cluster di Service Fabric usando il modello di Azure Resource Manager. Se le impostazioni sono corrette, Application Insights Profiler verrà installato e abilitato all'installazione dell'estensione Diagnostica di Microsoft Azure. 
1. Aggiungere Application Insights all'applicazione di Service Fabric. L'applicazione deve inviare dati di richiesta ad Application Insights in modo che il profiler possa raccogliere i profili per le richieste. In [questo](https://github.com/Microsoft/ApplicationInsights-ServiceFabric) articolo sono disponibili istruzioni.
1. Ridistribuire l'applicazione.

> [SUGGERIMENTO] Per le macchine virtuali, come alternativa alla procedura precedente basata su JSON, passare al portale di Azure e in **Macchine virtuali** > **Impostazioni di diagnostica** > **Sink** impostare l'invio di dati di diagnostica ad Application Insights su **Abilitato** e quindi selezionare un account di Application Insights o un ikey specifico.

## <a name="next-steps"></a>Passaggi successivi

- Generare traffico verso l'applicazione (ad esempio, avviare un [test di disponibilità](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Attendere quindi 10-15 minuti perché le tracce inizino ad essere inviate all'istanza di Application Insights.
- Vedere [Tracce Profiler](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) nel portale di Azure.
- Per informazioni su come risolvere i problemi relativi a Profiler, vedere [Risoluzione dei problemi del profiler](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).