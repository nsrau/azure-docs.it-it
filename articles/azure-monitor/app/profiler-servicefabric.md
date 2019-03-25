---
title: Profilare le applicazioni di Azure Service Fabric attive con Application Insights | Microsoft Docs
description: Abilitare Profiler per un'applicazione di Service Fabric
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 0b1a06d181fc4d2a44d389d47d1f9480c2fdcb40
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401096"
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
  Per Profiler raccogliere i profili per le richieste, l'applicazione deve essere verifica delle operazioni con Application Insights. Per le API senza state, è possibile fare riferimento alle istruzioni per la [rilevamento di richieste per la profilatura](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json). Per altre informazioni sulla verifica delle operazioni personalizzate in altri tipi di App, fare riferimento a [tenere traccia delle operazioni personalizzate con Application Insights .NET SDK](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Ridistribuire l'applicazione.

> [SUGGERIMENTO] Per le macchine virtuali, come alternativa alla procedura precedente basata su JSON, passare al portale di Azure e in **Macchine virtuali** > **Impostazioni di diagnostica** > **Sink** > **impostare l'invio di dati di diagnostica ad Application Insights su Abilitato** e quindi selezionare un account di Application Insights o un ikey specifico.

## <a name="next-steps"></a>Passaggi successivi

* Generare traffico verso l'applicazione (ad esempio, avviare un [test di disponibilità](monitor-web-app-availability.md)). Attendere quindi 10-15 minuti perché le tracce inizino ad essere inviate all'istanza di Application Insights.
* Vedere [Tracce Profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) nel portale di Azure.
* Per informazioni sulla risoluzione dei problemi di Profiler, vedere [Risoluzione dei problemi di Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
