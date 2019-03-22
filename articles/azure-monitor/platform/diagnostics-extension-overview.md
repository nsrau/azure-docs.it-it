---
title: Panoramica dell'estensione Diagnostica di Azure
description: Usare Diagnostica di Azure per debug, valutazione delle prestazioni, monitoraggio e analisi del traffico dei servizi cloud, delle macchine virtuali e di Service Fabric.
author: rboucher
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: 8a287f118c126967d2cf8cad77a434cfecc098eb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078540"
---
# <a name="what-is-azure-diagnostics-extension"></a>Cos'è l'estensione Diagnostica di Azure
L'estensione Diagnostica di Azure è un agente di Azure che consente la raccolta di dati di diagnostica in un'applicazione distribuita. È possibile usare l'estensione di diagnostica da numerose origini diverse. Sono attualmente supportati ruoli Web e di lavoro (classici) del servizio cloud di Azure, macchine virtuali, set di scalabilità di macchine virtuali e Service Fabric. Altri servizi di Azure hanno altri metodi di diagnostica. Vedere la [panoramica sul monitoraggio in Azure](../../azure-monitor/overview.md).

## <a name="linux-agent"></a>Agente Linux
Una [versione Linux dell'estensione](../../virtual-machines/extensions/diagnostics-linux.md) è disponibile per le macchine virtuali che eseguono Linux. Le statistiche raccolte e il comportamento sono diversi dalla versione di Windows.

## <a name="data-you-can-collect"></a>Dati che è possibile raccogliere
L'estensione Diagnostica di Azure può raccogliere i tipi di dati seguenti:

| origine dati | DESCRIZIONE |
| --- | --- |
| Metriche dei contatori delle prestazioni |Contatori delle prestazioni del sistema operativo e personalizzati |
| Log applicazioni |Messaggi di traccia scritti dall'applicazione |
| Registri eventi di Windows |Informazioni inviate al sistema di registrazione eventi di Windows. |
| Log EventSource .NET |Eventi di scrittura di codice con la classe [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) di .NET |
| log di IIS |Informazioni sui siti Web di IIS |
| [Log ETW basati su manifesto](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Traccia di eventi per eventi Windows generati da qualsiasi processo.(1) |
| Dump di arresto anomalo (log) |Informazioni sullo stato del processo in caso di arresto anomalo di un'applicazione |
| Log degli errori personalizzati |Log creati dall'applicazione o dal servizio. |
| Log dell'infrastruttura diagnostica di Azure |Informazioni su Diagnostica di Azure |

(1) Per ottenere un elenco di provider ETW, eseguire `c:\Windows\System32\logman.exe query providers` nella finestra della console nel computer da cui si desidera raccogliere informazioni.

## <a name="data-storage"></a>Archiviazione dei dati
L'estensione archivia i dati in un [account di archiviazione di Azure](diagnostics-extension-to-storage.md) specificato dall'utente.

È anche possibile inviarli ad [Application Insights](../../azure-monitor/app/cloudservices.md). 

Oppure è possibile trasmetterli all'[hub eventi](../../event-hubs/event-hubs-about.md) che consente quindi di inviarli a servizi di monitoraggio non di Azure.

È anche possibile scegliere di inviare dati al database delle serie temporali delle metriche di Monitoraggio di Azure. A questo punto, il sink è applicabile solo ai contatori delle prestazioni. Consente di inviare i contatori delle prestazioni come metriche personalizzate. Questa funzionalità è in anteprima. Il sink di Monitoraggio di Azure supporta:
* Il recupero di tutti i contatori delle prestazioni inviati a Monitoraggio di Azure tramite le [API di metrica di Monitoraggio di Azure.](https://docs.microsoft.com/rest/api/monitor/)
* La visualizzazione di avvisi su tutti i contatori delle prestazioni inviati a Monitoraggio di Azure tramite gli [avvisi delle metriche](../../azure-monitor/platform/alerts-overview.md) in Monitoraggio di Azure
* La considerazione dell'operatore carattere jolly nei contatori delle prestazioni come la dimensione "Istanza" per la metrica.  Ad esempio se è stato raccolto il contatore "LogicalDisk(\*)/DiskWrites/sec" sarà possibile filtrare e suddividere la dimensione "Istanza" del tracciato o avviso su Disk Writes/sec per ogni disco logico nella macchina virtuale (ad esempio, C:)

Per altre informazioni su come configurare questo sink, consultare la [documentazione dello schema di diagnostica di Azure.](diagnostics-extension-schema-1dot3.md)

## <a name="costs"></a>Costi
Ognuna delle opzioni elencate in precedenza potrebbe incorrere in costi. Assicurarsi di cercare in modo da evitare effetti imprevisti.  Application Insights, hub eventi, e archiviazione di Azure sono separati costi associati all'inserimento e l'ora archiviati. In particolare, archiviazione di Azure manterrà tutti i dati per sempre in modo che è possibile eliminare i dati meno recenti dopo un certo periodo di tempo per tenere i costi del controllo.    

## <a name="versioning-and-configuration-schema"></a>Controllo delle versioni e schema di configurazione
Vedere [Versioni e cronologia degli schemi di configurazione dell'estensione di Diagnostica di Azure](diagnostics-extension-schema.md).


## <a name="next-steps"></a>Passaggi successivi
Scegliere il servizio per cui si vuole tentare la raccolta di dati di diagnostica e usare gli articoli seguenti per iniziare. Usare i collegamenti di diagnostica di Azure generali per riferimento per attività specifiche.

## <a name="cloud-services-using-azure-diagnostics"></a>Servizi cloud con Diagnostica di Azure
* Se si usa Visual Studio, per iniziare vedere l'articolo su come [usare Visual Studio per tracciare un'applicazione di Servizi cloud](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines). In alternativa, vedere
* [Come monitorare i servizi cloud con Diagnostica di Azure](../../cloud-services/cloud-services-how-to-monitor.md)
* [Configurare Diagnostica di Azure in un'applicazione di Servizi cloud](../../cloud-services/cloud-services-dotnet-diagnostics.md)

Per argomenti più avanzati, vedere

* [Uso di Diagnostica di Azure con Application Insights per Servizi cloud](../../azure-monitor/app/cloudservices.md)
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

