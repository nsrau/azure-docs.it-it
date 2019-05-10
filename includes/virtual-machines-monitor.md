---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/27/2019
ms.author: cynthn
ms.openlocfilehash: ac400c86af8236ff5d67b8b6fbf99f6f4b1d36c9
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65404960"
---
È possibile sfruttare molte opportunità per il monitoraggio delle macchine virtuali tramite la raccolta, la visualizzazione e l'analisi dei dati di diagnostica e di log. Per eseguire semplici attività di [monitoraggio](../articles/azure-monitor/overview.md) della macchina virtuale, è possibile usare la schermata Panoramica per la macchina virtuale nel portale di Azure. È possibile usare [estensioni](../articles/virtual-machines/windows/extensions-features.md) per configurare la diagnostica nelle macchine virtuali e raccogliere metriche aggiuntive. È anche possibile usare opzioni di monitoraggio più avanzate, come [Application Insights](../articles/azure-monitor/app/app-insights-overview.md) e [Log Analytics](../articles/azure-monitor/log-query/log-query-overview.md).

## <a name="diagnostics-and-metrics"></a>Diagnostica e metriche 

È possibile configurare e monitorare la raccolta di [dati di diagnostica](https://docs.microsoft.com/cli/azure/vm/diagnostics) tramite [metriche](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) nel portale di Azure, nell'interfaccia della riga di comando di Azure, in Azure PowerShell e tramite la programmazione di API (Application Programming Interface). Ad esempio, è possibile:

- **Osservare le metriche di base per la macchina virtuale.** Nella schermata Panoramica del portale di Azure le metriche di base visualizzate includono in genere l'utilizzo della CPU, l'utilizzo della rete, i byte disco totali e le operazioni su disco al secondo.

- **Abilitare la raccolta dei dati di diagnostica di avvio e visualizzare i dati con il portale di Azure.** Quando si usa la propria immagine in Azure o anche se si avvia una delle immagini della piattaforma, esistono diversi motivi per cui una macchina virtuale può passare allo stato non avviabile. È possibile abilitare facilmente la diagnostica di avvio quando si crea una macchina virtuale facendo clic su **Abilitata** per Diagnostica di avvio nella sezione Monitoraggio della schermata Impostazioni.

    All'avvio delle macchine virtuali, l'agente di diagnostica di avvio acquisisce l'output di avvio e lo archivia in Archiviazione di Azure. Questi dati possono essere usati per risolvere i problemi di avvio della macchina virtuale. La diagnostica di avvio non viene abilitata automaticamente quando si crea una macchina virtuale da strumenti da riga di comando. Prima di abilitare la diagnostica di avvio, è necessario creare un account di archiviazione per l'archiviazione dei log di avvio. Se si abilita la diagnostica di avvio nel portale di Azure, viene automaticamente creato un account di archiviazione.

    Se la diagnostica di avvio non è stata abilitata durante la creazione della macchina virtuale, è sempre possibile abilitarla in seguito usando l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic) o un [modello di Azure Resource Manager](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Abilitare la raccolta dei dati di diagnostica del sistema operativo guest.** Quando si crea una macchina virtuale, nella schermata delle impostazioni è possibile abilitare la diagnostica del sistema operativo guest. Quando si abilita la raccolta dei dati di diagnostica, alla macchina virtuale viene aggiunta l'[estensione IaaSDiagnostics per Linux](../articles/virtual-machines/linux/diagnostic-extension.md) o l'[estensione IaaSDiagnostics per Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md), che permette di raccogliere dati aggiuntivi su disco, CPU e memoria.

    Usando i dati di diagnostica raccolti, è possibile configurare la scalabilità automatica per le macchine virtuali. È anche possibile configurare log per archiviare i dati e impostare avvisi per essere informati quando le prestazioni sono inadeguate.

## <a name="alerts"></a>Avvisi

È possibile creare [avvisi](../articles/azure-monitor/platform/alerts-overview.md) in base a metriche di prestazioni specifiche. I problemi che possono essere segnalati tramite avvisi, ad esempio, sono il superamento di una determinata soglia di utilizzo medio della CPU o la presenza di spazio su disco inferiore rispetto a una quantità specifica. Gli avvisi possono essere configurati nel [portale di Azure](../articles/azure-monitor/platform/alerts-classic-portal.md), tramite [Azure PowerShell](../articles/azure-monitor/platform/alerts-classic-portal.md#with-powershell) o nell'[interfaccia della riga di comando di Azure](../articles/azure-monitor/platform/alerts-classic-portal.md#with-azure-cli).

## <a name="azure-service-health"></a>Integrità dei servizi di Azure

[Integrità dei servizi di Azure](../articles/service-health/service-health-overview.md) offre indicazioni e modalità di supporto personalizzate in caso di problemi relativi ai servizi di Azure e permette di prepararsi per la manutenzione pianificata successiva. Integrità dei servizi di Azure invia avvisi all'utente e ai team tramite notifiche mirate e flessibili.

## <a name="azure-resource-health"></a>Integrità risorse di Azure

[Integrità risorse di Azure](../articles/service-health/resource-health-overview.md) aiuta a diagnosticare gli eventuali problemi di Azure che possono influire negativamente sulle risorse e a ottenere il supporto necessario. Informa sull'integrità corrente e passata delle risorse e consente di attenuare i problemi. Integrità risorse offre supporto tecnico quando è necessaria assistenza per problemi con i servizi di Azure.

## <a name="azure-activity-log"></a>Azure Activity Log

Il [log attività di Azure](../articles/azure-monitor/platform/activity-logs-overview.md) è un log delle sottoscrizioni che fornisce informazioni approfondite sugli eventi a livello di sottoscrizione che si sono verificati in Azure. Il log include una gamma di dati, dai dati operativi di Azure Resource Manager agli aggiornamenti sugli eventi di integrità dei servizi. È possibile fare clic su Log attività nel portale di Azure per visualizzare il log per la macchina virtuale.

Ecco alcune delle attività che è possibile eseguire con il log attività:

- Creare un [avviso per un evento del log attività](../articles/azure-monitor/platform/activity-logs-overview.md).
- [Trasmetterlo a un hub eventi](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) per l'inserimento da parte di un servizio di terze parti o di una soluzione di analisi personalizzata come Power BI.
- Analizzarlo in Power BI usando il [pacchetto di contenuto di Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Salvarlo in un account di archiviazione](../articles/azure-monitor/platform/archive-activity-log.md) per l'archiviazione o l'ispezione manuale. È possibile specificare il tempo di conservazione (in giorni) tramite il profilo di log.

È anche possibile accedere a dati del log attività usando [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/monitor) o le [API REST di monitoraggio](https://docs.microsoft.com/rest/api/monitor/).

I [log di diagnostica di Azure](../articles/azure-monitor/platform/diagnostic-logs-overview.md) sono log generati dalla macchina virtuale che forniscono dati completi e frequenti sul suo funzionamento. I log di diagnostica sono diversi dal log attività, perché forniscono analisi approfondite sulle operazioni eseguite nella macchina virtuale.

Ecco alcune delle attività che è possibile eseguire con i log di diagnostica:

- [Salvarli in un account di archiviazione](../articles/azure-monitor/platform/archive-diagnostic-logs.md) per il controllo o l'ispezione manuale. È possibile specificare il tempo di conservazione (in giorni) tramite le impostazioni di diagnostica delle risorse.
- [Trasmettere i log a Hub eventi](../articles/azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) per l'inserimento tramite un servizio di terze parti o una soluzione di analisi personalizzata come Power BI.
- Analizzarli con [Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Monitoraggio avanzato

- [Monitoraggio di Azure](../articles/azure-monitor/overview.md) è un servizio che effettua il monitoraggio degli ambienti cloud e locali per consentire la gestione della disponibilità e delle prestazioni. Offre una soluzione completa per la raccolta, l'analisi e l'esecuzione di operazioni sui dati di telemetria dal cloud e dagli ambienti locali. È utile per ottenere informazioni sulle prestazioni delle applicazioni e identificare in modo proattivo i problemi delle applicazioni e delle risorse da cui dipendono. In una [macchina virtuale Linux](../articles/virtual-machines/linux/extensions-oms.md) o in una [macchina virtuale Windows](../articles/virtual-machines/windows/extensions-oms.md) è possibile installare un'estensione che include l'agente di Log Analytics per raccogliere i dati di log e archiviarli in un'area di lavoro Log Analytics.

    Per le macchine virtuali Windows e Linux, il metodo consigliato per la raccolta di log consiste nell'installazione dell'agente di Log Analytics. Il modo più semplice per installare l'agente di Log Analytics in una macchina virtuale è tramite l'[estensione macchina virtuale di Log Analytics](../articles/log-analytics/log-analytics-azure-vm-extension.md). L'uso dell'estensione macchina virtuale consente di semplificare il processo di installazione e di configurare automaticamente l'agente per l'invio di dati all'area di lavoro Log Analytics specificata. L'agente viene anche aggiornato automaticamente in modo da garantire la presenza delle funzionalità e delle correzioni più recenti.

- [Network Watcher](../articles/network-watcher/network-watcher-monitoring-overview.md) permette di monitorare la macchina virtuale e le risorse associate in relazione alla rete in cui si trovano. È possibile installare l'estensione dell'agente Network Watcher in una [macchina virtuale Linux](../articles/virtual-machines/linux/extensions-nwa.md) o una [macchina virtuale Windows](../articles/virtual-machines/windows/extensions-nwa.md).

- [Monitoraggio di Azure per le macchine virtuali](../articles/azure-monitor/insights/vminsights-overview.md) monitora le macchine virtuali di Azure su larga scala analizzando le prestazioni e l'integrità delle macchine virtuali Windows e Linux, inclusi i relativi processi e le dipendenze interconnesse ad altre risorse e processi esterni. 

## <a name="next-steps"></a>Passaggi successivi
- Eseguire i passaggi inclusi in [Monitorare una macchina virtuale Windows con Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) o [Monitorare una macchina virtuale Linux con l'interfaccia della riga di comando di Azure](../articles/virtual-machines/linux/tutorial-monitoring.md).
- Leggere le procedure consigliate per [monitoraggio e diagnostica](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
