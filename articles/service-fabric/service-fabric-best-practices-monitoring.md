---
title: Procedure consigliate per il monitoraggio di Azure Service Fabric | Microsoft Docs
description: Procedure consigliate per il monitoraggio di cluster e applicazioni di Service Fabric.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: d90daaf18e5161053e00671b7667d05ec8e5db76
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60533810"
---
# <a name="monitoring-and-diagnostics"></a>Monitoraggio e diagnostica

Il [monitoraggio e la diagnostica](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) sono essenziali per lo sviluppo, il test e la distribuzione di carichi di lavoro in qualsiasi ambiente cloud. Ad esempio, è possibile monitorare le modalità di utilizzo delle applicazioni, le azioni eseguite dalla piattaforma Service Fabric, l'utilizzo delle risorse con i contatori delle prestazioni e l'integrità generale del cluster. È possibile usare queste informazioni per diagnosticare e correggere eventuali problemi e per evitare che si verifichino in futuro.

## <a name="application-monitoring"></a>Monitoraggio delle applicazioni

Il monitoraggio delle applicazioni tiene traccia del modo in cui vengono usati le funzionalità e i componenti dell'applicazione. Consente, ad esempio, di identificare i problemi che possono incidere sugli utenti. La responsabilità del monitoraggio di un'applicazione è dell'utente che l'ha sviluppata insieme ai relativi servizi, in quanto è esclusivo della logica di business dell'applicazione. Si consiglia di configurare il monitoraggio delle applicazioni con [Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-monitoring-aspnet), lo strumento di monitoraggio delle applicazioni di Azure.

## <a name="cluster-monitoring"></a>Monitoraggio del cluster

Uno dei principali obiettivi di Service Fabric è mantenere le applicazioni resilienti agli errori hardware. Questo obiettivo viene conseguito grazie alla capacità dei servizi di sistema della piattaforma di rilevare eventuali problemi dell'infrastruttura ed eseguire rapidamente il failover dei carichi di lavoro su altri nodi del cluster. Cosa accade, tuttavia, se sono presenti problemi anche nei servizi di sistema? O se, nel tentativo di distribuire o spostare un carico di lavoro, vengono violate le regole relative al posizionamento dei servizi? Service Fabric offre funzionalità di diagnostica per questi e altri problemi per garantire che l'utente sia sempre informato sulle modalità con cui la piattaforma Service Fabric interagisce con le applicazioni, i servizi, i contenitori e i nodi.

Per i cluster di Windows, è consigliabile configurare il monitoraggio del cluster con [agente di diagnostica](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) e [monitoraggio di Azure registra](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-setup).

Per i cluster Linux, i log di monitoraggio di Azure è anche lo strumento consigliato per la piattaforma Azure e monitoraggio dell'infrastruttura. La diagnostica di una piattaforma Linux richiede invece una configurazione diversa, come specificato in [Eventi cluster Linux di Service Fabric in Syslog](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-syslog).

## <a name="infrastructure-monitoring"></a>Monitoraggio dell'infrastruttura

[Log di monitoraggio di Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-agent) è consigliato per il monitoraggio degli eventi a livello di cluster. Dopo aver configurato l'agente di Log Analytics nell'area di lavoro come descritto nel collegamento precedente, sarà possibile raccogliere metriche delle prestazioni, come l'utilizzo della CPU, contatori delle prestazioni .NET, come l'utilizzo della CPU a livello di processo, contatori delle prestazioni di Service Fabric, come il numero di eccezioni in un servizio Reliable Services, e metriche dei contenitori, come l'utilizzo della CPU.  È necessario scrivere i log dei contenitori in stdout o stderr, in modo che siano disponibili nei log di monitoraggio di Azure.

## <a name="watchdogs"></a>Watchdog

In genere, un watchdog è un servizio separato che controlla l'integrità e il carico tra servizi, esegue il ping degli endpoint e crea report di eventi di integrità imprevisti nel cluster. Questo consente di evitare errori che non verrebbero rilevati visualizzando le prestazioni di un singolo servizio. I watchdog possono essere usati anche per ospitare codice che esegue azioni correttive senza l'intervento dell'utente, ad esempio la cancellazione dei file di log nell'archiviazione a specifici intervalli di tempo. È possibile trovare un'implementazione di esempio del servizio watchdog in [Eventi cluster Linux di Service Fabric in Syslog](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alla strumentazione delle applicazioni: [Generazione di eventi e log a livello di applicazione](service-fabric-diagnostics-event-generation-app.md).
* Completare i passaggi per configurare Application Insights per l'applicazione in [Monitorare e diagnosticare un'applicazione ASP.NET Core in Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Per altre informazioni sul monitoraggio della piattaforma e gli eventi messi a disposizione da Service Fabric: [Generazione di eventi e log a livello piattaforma](service-fabric-diagnostics-event-generation-infra.md).
* Configurare l'integrazione dei log di monitoraggio di Azure con Service Fabric: [Configurare i log di monitoraggio di Azure per un cluster](service-fabric-diagnostics-oms-setup.md)
* Informazioni su come configurare i log di monitoraggio di Azure per monitorare i contenitori: [Monitoraggio e diagnostica per i contenitori di Windows in Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Per esempi di problemi di diagnostica e soluzioni con Service Fabric: [Scenari comuni di diagnosi](service-fabric-diagnostics-common-scenarios.md)
* Per suggerimenti generali sul monitoraggio delle risorse di Azure: [Procedure consigliate: monitoraggio e diagnostica](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).