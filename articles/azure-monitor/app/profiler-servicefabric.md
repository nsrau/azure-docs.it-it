---
title: Profilare le applicazioni di Azure Service Fabric attive con Application Insights | Microsoft Docs
description: Abilitare Profiler per un'applicazione di Service Fabric
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
ms.openlocfilehash: f816086293d663e141b2d6efe5791cc8e37eba36
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358593"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Profilare le applicazioni di Azure Service Fabric attive con Application Insights

È anche possibile distribuire Application Insights Profiler in questi servizi:
* [Servizio app di Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Servizi cloud di Azure](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Macchine virtuali di Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Configurare la definizione di distribuzione dell'ambiente

Application Insights Profiler è incluso in Diagnostica di Azure. È possibile installare l'estensione Diagnostica di Azure usando un modello di Azure Resource Manager per il cluster di Service Fabric. Ottenere un [modello di installazione di Diagnostica di Azure in un cluster di Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Per configurare l'ambiente, seguire questa procedura:

1. Per verificare che sia in uso [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) o versione successiva, è sufficiente controllare che il sistema operativo distribuito sia `Windows Server 2012 R2` o versione successiva.

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
  L'applicazione deve inviare dati di richiesta ad Application Insights in modo che il profiler possa raccogliere i profili per le richieste. Per altre informazioni, vedere la pagina [Application Insights SDK for Service Fabric projects (Application Insights SDK per i progetti di Service Fabric)](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

1. Ridistribuire l'applicazione.

> [SUGGERIMENTO] Per le macchine virtuali, come alternativa alla procedura precedente basata su JSON, passare al portale di Azure e in **Macchine virtuali** > **Impostazioni di diagnostica** > **Sink** > **impostare l'invio di dati di diagnostica ad Application Insights su Abilitato** e quindi selezionare un account di Application Insights o un ikey specifico.

## <a name="next-steps"></a>Passaggi successivi

* Generare traffico verso l'applicazione (ad esempio, avviare un [test di disponibilità](https://docs.microsoft.com/azure/application-insights/monitor-web-app-availability)). Attendere quindi 10-15 minuti perché le tracce inizino ad essere inviate all'istanza di Application Insights.
* Vedere [Tracce Profiler](https://docs.microsoft.com/azure/application-insights/profiler-overview?toc=/azure/azure-monitor/toc.json) nel portale di Azure.
* Per informazioni sulla risoluzione dei problemi del Profiler, vedere [Risoluzione dei problemi di Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
