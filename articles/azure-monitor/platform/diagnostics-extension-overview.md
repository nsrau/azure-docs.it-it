---
title: Panoramica dell'estensione Diagnostica di Azure
description: Usare Diagnostica di Azure per debug, valutazione delle prestazioni, monitoraggio e analisi del traffico dei servizi cloud, delle macchine virtuali e di Service Fabric.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 3b7b06b7ab9076b992e6e3349763d633f9b255ef
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338159"
---
# <a name="what-is-azure-diagnostics-extension"></a>Cos'è l'estensione Diagnostica di Azure
L'estensione Diagnostica di Azure è un agente di Azure che consente la raccolta di dati di diagnostica in un'applicazione distribuita. È possibile usare l'estensione di diagnostica da numerose origini diverse. Sono attualmente supportati ruoli Web e di lavoro (classici) del servizio cloud di Azure, macchine virtuali, set di scalabilità di macchine virtuali e Service Fabric. Altri servizi di Azure hanno altri metodi di diagnostica. Vedere la [panoramica sul monitoraggio in Azure](../../azure-monitor/overview.md).

## <a name="linux-agent"></a>Agente Linux
Una [versione Linux dell'estensione](../../virtual-machines/extensions/diagnostics-linux.md) è disponibile per le macchine virtuali che eseguono Linux. Le statistiche raccolte e il comportamento sono diversi dalla versione di Windows.

## <a name="data-you-can-collect"></a>Dati che è possibile raccogliere
L'estensione Diagnostica di Azure può raccogliere i tipi di dati seguenti:

| origine dati | DESCRIZIONE |
| --- | --- |
| Contatori delle prestazioni |Contatori delle prestazioni del sistema operativo e personalizzati |
| Log applicazioni |Messaggi di traccia scritti dall'applicazione |
| Registri eventi di Windows |Informazioni inviate al sistema di registrazione eventi di Windows. |
| Origine dell'evento .NET |Eventi di scrittura di codice con la classe [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) di .NET |
| log di IIS |Informazioni sui siti Web di IIS |
| ETW basato su manifesto |Traccia di eventi per eventi Windows generati da qualsiasi processo.(1) |
| Dump di arresto anomalo del sistema |Informazioni sullo stato del processo in caso di arresto anomalo dell'applicazione. |
| Log degli errori personalizzati |Log creati dall'applicazione o dal servizio. |
| Log dell'infrastruttura diagnostica di Azure |Informazioni su Diagnostica |

(1) Per ottenere un elenco di provider ETW, eseguire `c:\Windows\System32\logman.exe query providers` nella finestra della console nel computer da cui si desidera raccogliere informazioni.

## <a name="data-storage"></a>Archiviazione dei dati
L'estensione archivia i dati in un [account di archiviazione di Azure](diagnostics-extension-to-storage.md) specificato dall'utente.

È anche possibile inviarli ad [Application Insights](../../application-insights/app-insights-cloudservices.md). Oppure è possibile trasmetterli all'[hub eventi](../../event-hubs/event-hubs-about.md) che consente di inviarli a servizi di monitoraggio non di Azure.

### <a name="azure-monitor"></a>Monitoraggio di Azure
È anche possibile scegliere di inviare i dati a Monitoraggio di Azure. A questo punto, il sink è applicabile solo ai contatori delle prestazioni. Consente di inviare i contatori delle prestazioni raccolti nella macchina virtuale, VMSS o servizio cloud a Monitoraggio di Azure come metriche personalizzate. Il sink di Monitoraggio di Azure supporta:
* Il recupero di tutti i contatori delle prestazioni inviati a Monitoraggio di Azure tramite le [API di metrica di Monitoraggio di Azure.](https://docs.microsoft.com/rest/api/monitor/)
* La visualizzazione di avvisi su tutti i contatori delle prestazioni inviati a Monitoraggio di Azure tramite la nuova [esperienza di avvisi unificati](../../azure-monitor/platform/alerts-overview.md) in Monitoraggio di Azure
* La considerazione dell'operatore carattere jolly nei contatori delle prestazioni come la dimensione "Istanza" per la metrica.  Ad esempio se è stato raccolto il contatore "LogicalDisk(\*)/DiskWrites/sec" sarà possibile filtrare e suddividere la dimensione "Istanza" del tracciato o avviso su Disk Writes/sec per ogni disco logico nella macchina virtuale (C:, D:, e così via)

Per altre informazioni su come configurare questo sink, consultare la [documentazione dello schema di diagnostica di Azure.](diagnostics-extension-schema-1dot3.md)

## <a name="versioning-and-configuration-schema"></a>Controllo delle versioni e schema di configurazione
Vedere [Versioni e cronologia degli schemi di configurazione dell'estensione di Diagnostica di Azure](diagnostics-extension-schema.md).


## <a name="next-steps"></a>Passaggi successivi
Scegliere il servizio su cui si desidera raccogliere dati di diagnostica e usare i seguenti articoli per iniziare. Usare i collegamenti di diagnostica di Azure generali per riferimento per attività specifiche.

## <a name="cloud-services-using-azure-diagnostics"></a>Servizi cloud con Diagnostica di Azure
* Se si usa Visual Studio, per iniziare vedere l'articolo su come [usare Visual Studio per tracciare un'applicazione di Servizi cloud](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines). In alternativa, vedere
* [Come monitorare i servizi cloud con Diagnostica di Azure](../../cloud-services/cloud-services-how-to-monitor.md)
* [Configurare Diagnostica di Azure in un'applicazione di Servizi cloud](../../cloud-services/cloud-services-dotnet-diagnostics.md)

Per argomenti più avanzati, vedere

* [Uso di Diagnostica di Azure con Application Insights per Servizi cloud](../../application-insights/app-insights-cloudservices.md)
* [Tracciare il flusso di un'applicazione di Servizi cloud con Diagnostica di Azure](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Usare PowerShell per configurare la diagnostica in Servizi cloud](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Macchine virtuali
* Se si usa Visual Studio, per iniziare vedere l'articolo su come [usare Visual Studio per tracciare le macchine virtuali di Azure](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines). In alternativa, vedere
* [Configurare Diagnostica di Azure in una macchina virtuale di Azure](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

Per argomenti più avanzati, vedere

* [Usare PowerShell per abilitare Diagnostica di Azure nelle macchine virtuali di Azure](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Creare una macchina virtuale Windows con monitoraggio e diagnostica con un modello di Azure Resource Manager](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
Per un'introduzione, vedere [Monitorare un'applicazione di Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Numerosi altri articoli sulla diagnostica di Service Fabric sono disponibili nella struttura di navigazione a sinistra.

## <a name="general-articles"></a>Articoli generali
* Informazioni su come [usare i contatori delle prestazioni in Diagnostica di Azure](../../cloud-services/diagnostics-performance-counters.md).
* In caso di problemi nell'avvio della diagnostica o nell'individuazione dei dati nelle tabelle di archiviazione di Azure, vedere [Risoluzione dei problemi di Diagnostica di Azure](diagnostics-extension-troubleshooting.md)
