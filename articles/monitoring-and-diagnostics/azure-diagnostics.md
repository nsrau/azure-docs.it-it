---
title: Panoramica di Diagnostica di Azure | Documentazione Microsoft
description: Usare Diagnostica di Azure per debug, valutazione delle prestazioni, monitoraggio e analisi del traffico dei servizi cloud, delle macchine virtuali e di Service Fabric.
services: multiple
documentationcenter: .net
author: rboucher
manager: 
editor: 
ms.assetid: baad40d8-c915-4f93-b486-8b160bf33463
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2017
ms.author: robb
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 0c6e4d9d2a3744f607b72364f3944c700acd070c
ms.contentlocale: it-it
ms.lasthandoff: 09/20/2017

---
# <a name="what-is-azure-diagnostics"></a>Informazioni sulla diagnostica di Azure
Diagnostica di Azure è la funzionalità all'interno di Azure che consente la raccolta di dati di diagnostica in un'applicazione distribuita. È possibile usare l'estensione di diagnostica da numerose origini diverse. Sono attualmente supportati i ruoli Web e di lavoro dei servizi cloud di Azure e le macchine virtuali di Azure che eseguono Microsoft Windows e Service Fabric. Altri servizi di Azure dispongono di propri strumenti di diagnostica separati.

## <a name="data-you-can-collect"></a>Dati che è possibile raccogliere
Diagnostica di Azure consente di raccogliere i tipi di dati seguenti:

| Origine dati | Descrizione |
| --- | --- |
| Contatori delle prestazioni |Contatori delle prestazioni del sistema operativo e personalizzati |
| Log applicazioni |Messaggi di traccia scritti dall'applicazione |
| Registri eventi di Windows |Informazioni inviate al sistema di registrazione eventi di Windows. |
| Origine dell'evento .NET |Eventi di scrittura di codice con la classe [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) di .NET |
| Log di IIS |Informazioni sui siti Web di IIS |
| ETW basato su manifesto |Traccia di eventi per eventi Windows generati da qualsiasi processo |
| Dump di arresto anomalo del sistema |Informazioni sullo stato del processo in caso di arresto anomalo dell'applicazione. |
| Log degli errori personalizzati |Log creati dall'applicazione o dal servizio. |
| Log dell'infrastruttura diagnostica di Azure |Informazioni su Diagnostica |

L'estensione di diagnostica di Azure consente di trasferire i dati in un account di archiviazione di Azure o di inviarli a servizi come [Application Insights](../application-insights/app-insights-cloudservices.md). È possibile usare i dati per il debug, la risoluzione dei problemi, la valutazione delle prestazioni, il monitoraggio dell'utilizzo delle risorse, l'analisi del traffico, la pianificazione della capacità e il controllo.

## <a name="versioning"></a>Controllo delle versioni
Vedere [Cronologia delle versioni di Diagnostica di Azure](azure-diagnostics-versioning-history.md).

## <a name="next-steps"></a>Passaggi successivi
Scegliere il servizio su cui si desidera raccogliere dati di diagnostica e usare i seguenti articoli per iniziare. Usare i collegamenti di diagnostica di Azure generali per riferimento per attività specifiche.

## <a name="web-apps"></a>App Web
Si noti che le app Web non usano Diagnostica di Azure. Trovare le informazioni equivalenti in [App Web](../app-service/web-sites-enable-diagnostic-log.md)

## <a name="cloud-services-using-azure-diagnostics"></a>Servizi cloud con Diagnostica di Azure
* Se si usa Visual Studio, per iniziare vedere l'articolo su come [usare Visual Studio per tracciare un'applicazione di Servizi cloud](../vs-azure-tools-debug-cloud-services-virtual-machines.md). In alternativa, vedere
* [Come monitorare i servizi cloud con Diagnostica di Azure](../cloud-services/cloud-services-how-to-monitor.md)
* [Configurare Diagnostica di Azure in un'applicazione di Servizi cloud](../cloud-services/cloud-services-dotnet-diagnostics.md)

Per argomenti più avanzati, vedere

* [Uso di Diagnostica di Azure con Application Insights per Servizi cloud](../application-insights/app-insights-cloudservices.md)
* [Tracciare il flusso di un'applicazione di Servizi cloud con Diagnostica di Azure](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Usare PowerShell per configurare la diagnostica in Servizi cloud](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines-using-azure-diagnostics"></a>Macchine virtuali con Diagnostica di Azure
* Se si usa Visual Studio, per iniziare vedere l'articolo su come [usare Visual Studio per tracciare le macchine virtuali di Azure](../vs-azure-tools-debug-cloud-services-virtual-machines.md). In alternativa, vedere
* [Configurare Diagnostica di Azure in una macchina virtuale di Azure](../virtual-machines-dotnet-diagnostics.md)

Per argomenti più avanzati, vedere

* [Usare PowerShell per abilitare Diagnostica di Azure nelle macchine virtuali di Azure](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Creare una macchina virtuale Windows con monitoraggio e diagnostica con un modello di Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-using-azure-diagnostics"></a>Service Fabric con Diagnostica di Azure
Per un'introduzione, vedere [Monitorare un'applicazione di Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Numerosi altri articoli sulla diagnostica di Service Fabric sono disponibili nella struttura di navigazione a sinistra.

## <a name="general-azure-diagnostics-articles"></a>Articoli su Diagnostica di Azure
* [Schemi di configurazione di diagnostica di Azure](https://msdn.microsoft.com/library/azure/mt634524.aspx) -Informazioni su come modificare il file di schema per raccogliere e inviare dati di diagnostica. Si noti che è anche possibile usare Visual Studio per modificare il file di schema.
* [Modalità di archiviazione dei dati di diagnostica in Archiviazione di Azure](../cloud-services/cloud-services-dotnet-diagnostics-storage.md) - Nomi delle tabelle e dei BLOB in cui vengono scritti i dati di diagnostica.
* Informazioni su come [usare i contatori delle prestazioni in Diagnostica di Azure](../cloud-services/cloud-services-dotnet-diagnostics-performance-counters.md).
* Informazioni su come [instradare le informazioni di diagnostica di Azure ad Application Insights](azure-diagnostics-configure-application-insights.md)
* In caso di problemi nell'avvio della diagnostica o nell'individuazione dei dati nelle tabelle di archiviazione di Azure, vedere [Risoluzione dei problemi di Diagnostica di Azure](azure-diagnostics-troubleshooting.md)

