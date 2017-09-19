È possibile sfruttare molte opportunità per il monitoraggio delle macchine virtuali tramite la raccolta, la visualizzazione e l'analisi dei dati di diagnostica e di log. Per eseguire semplici attività di [monitoraggio](../articles/monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) della macchina virtuale, è possibile usare la schermata Panoramica per la macchina virtuale nel portale di Azure. È possibile usare [estensioni](../articles/virtual-machines/windows/extensions-features.md) per configurare la diagnostica nelle macchine virtuali e raccogliere metriche aggiuntive. È anche possibile usare opzioni di monitoraggio più avanzate, come [Application Insights](../articles/application-insights/app-insights-overview.md) e [Log Analytics](../articles/log-analytics/log-analytics-overview.md).

## <a name="diagnostics-and-metrics"></a>Diagnostica e metriche 

È possibile configurare e monitorare la raccolta di [dati di diagnostica](https://docs.microsoft.com/cli/azure/vm/diagnostics) tramite [metriche](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) nel portale di Azure, nell'interfaccia della riga di comando di Azure, in Azure PowerShell e tramite la programmazione di API (Application Programming Interface). Ad esempio, è possibile:

- **Osservare le metriche di base per la macchina virtuale.** Nella schermata Panoramica del portale di Azure le metriche di base visualizzate includono in genere l'utilizzo della CPU, l'utilizzo della rete, i byte disco totali e le operazioni su disco al secondo.

- **Abilitare la raccolta dei dati di diagnostica di avvio e visualizzare i dati con il portale di Azure.** Quando si usa la propria immagine in Azure o anche se si avvia una delle immagini della piattaforma, esistono diversi motivi per cui una macchina virtuale può passare allo stato non avviabile. È possibile abilitare facilmente la diagnostica di avvio quando si crea una macchina virtuale facendo clic su **Abilitata** per Diagnostica di avvio nella sezione Monitoraggio della schermata Impostazioni.

    All'avvio delle macchine virtuali, l'agente di diagnostica di avvio acquisisce l'output di avvio e lo archivia in Archiviazione di Azure. Questi dati possono essere usati per risolvere i problemi di avvio della macchina virtuale. La diagnostica di avvio non viene abilitata automaticamente quando si crea una macchina virtuale da strumenti da riga di comando. Prima di abilitare la diagnostica di avvio, è necessario creare un account di archiviazione per l'archiviazione dei log di avvio. Se si abilita la diagnostica di avvio nel portale di Azure, viene automaticamente creato un account di archiviazione.

    Se la diagnostica di avvio non è stata abilitata durante la creazione della macchina virtuale, è sempre possibile abilitarla in seguito usando l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmbootdiagnostics) o un [modello di Azure Resource Manager](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Abilitare la raccolta dei dati di diagnostica del sistema operativo guest.** Quando si crea una macchina virtuale, nella schermata delle impostazioni è possibile abilitare la diagnostica del sistema operativo guest. Quando si abilita la raccolta dei dati di diagnostica, alla macchina virtuale viene aggiunta l'[estensione IaaSDiagnostics per Linux](../articles/virtual-machines/linux/diagnostic-extension.md) o l'[estensione IaaSDiagnostics per Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md), che permette di raccogliere dati aggiuntivi su disco, CPU e memoria.

    Usando i dati di diagnostica raccolti, è possibile configurare la scalabilità automatica per le macchine virtuali. È anche possibile configurare log per archiviare i dati e impostare avvisi per essere informati quando le prestazioni sono inadeguate.

## <a name="alerts"></a>Avvisi

È possibile creare [avvisi](../articles/monitoring-and-diagnostics/monitoring-overview-alerts.md) in base a metriche di prestazioni specifiche. I problemi che possono essere segnalati tramite avvisi, ad esempio, sono il superamento di una determinata soglia di utilizzo medio della CPU o la presenza di spazio su disco inferiore rispetto a una quantità specifica. Gli avvisi possono essere configurati nel [portale di Azure](../articles/monitoring-and-diagnostics/insights-alerts-portal.md), tramite [Azure PowerShell](../articles/monitoring-and-diagnostics/insights-alerts-powershell.md) o nell'[interfaccia della riga di comando di Azure](../articles/monitoring-and-diagnostics/insights-alerts-command-line-interface.md).

## <a name="azure-service-health"></a>Integrità del servizio di Azure

[Integrità dei servizi di Azure](../articles/service-health/service-health-overview.md) offre indicazioni e modalità di supporto personalizzate in caso di problemi relativi ai servizi di Azure e permette di prepararsi per la manutenzione pianificata successiva. Integrità dei servizi di Azure invia avvisi all'utente e ai team tramite notifiche mirate e flessibili.

## <a name="azure-resource-health"></a>Integrità risorse di Azure

[Integrità risorse di Azure](../articles/service-health/resource-health-overview.md) aiuta a diagnosticare gli eventuali problemi di Azure che possono influire negativamente sulle risorse e a ottenere il supporto necessario. Informa sull'integrità corrente e passata delle risorse e consente di attenuare i problemi. Integrità risorse offre supporto tecnico quando è necessaria assistenza per problemi con i servizi di Azure.

## <a name="logs"></a>Log

Il [log attività di Azure](../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md) è un log delle sottoscrizioni che fornisce informazioni approfondite sugli eventi a livello di sottoscrizione che si sono verificati in Azure. Il log include una gamma di dati, dai dati operativi di Azure Resource Manager agli aggiornamenti sugli eventi di integrità dei servizi. È possibile fare clic su Log attività nel portale di Azure per visualizzare il log per la macchina virtuale.

Ecco alcune delle attività che è possibile eseguire con il log attività:

- Creare un [avviso per un evento del log attività](../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md).
- [Trasmetterlo a un hub eventi](../articles/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md) per l'inserimento da parte di un servizio di terze parti o di una soluzione di analisi personalizzata come Power BI.
- Analizzarlo in Power BI usando il [pacchetto di contenuto di Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Salvarlo in un account di archiviazione](../articles/monitoring-and-diagnostics/monitoring-archive-activity-log.md) per l'archiviazione o l'ispezione manuale. È possibile specificare il tempo di conservazione (in giorni) tramite il profilo di log.

È anche possibile accedere a dati del log attività usando [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/monitor) o le [API REST di monitoraggio](https://docs.microsoft.com/rest/api/monitor/).

I [log di diagnostica di Azure](../articles/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) sono log generati dalla macchina virtuale che forniscono dati completi e frequenti sul suo funzionamento. I log di diagnostica sono diversi dal log attività, perché forniscono analisi approfondite sulle operazioni eseguite nella macchina virtuale.

Ecco alcune delle attività che è possibile eseguire con i log di diagnostica:

- [Salvarli in un account di archiviazione](../articles/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md) per il controllo o l'ispezione manuale. È possibile specificare il tempo di conservazione (in giorni) tramite le impostazioni di diagnostica delle risorse.
- [Trasmettere i log a Hub eventi](../articles/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) per l'inserimento tramite un servizio di terze parti o una soluzione di analisi personalizzata come Power BI.
- Analizzarli con [Log Analytics di Operations Management Suite](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Monitoraggio avanzato

- [Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/) include funzionalità di monitoraggio, impostazione di avvisi e correzione tramite avvisi per asset cloud e locali. In una [macchina virtuale Linux](../articles/virtual-machines/linux/extensions-oms.md) o una [macchina virtuale Windows](../articles/virtual-machines/windows/extensions-oms.md) è possibile installare un'estensione che installa l'agente OMS e registra la macchina virtuale in un'area di lavoro OMS esistente.

- [Log Analytics](../articles/log-analytics/log-analytics-overview.md) è un servizio incluso in OMS che monitora gli ambienti cloud e locali per mantenerne la disponibilità e le prestazioni. Raccoglie i dati generati dalle risorse negli ambienti cloud e locali e da altri strumenti di monitoraggio per analizzare più origini.

    Per le macchine virtuali Windows e Linux, il metodo consigliato per la raccolta di log e metriche consiste nell'installazione dell'agente di Log Analytics. Il modo più semplice per installare l'agente di Log Analytics in una macchina virtuale è tramite l'[estensione macchina virtuale di Log Analytics](../articles/log-analytics/log-analytics-azure-vm-extension.md). L'uso dell'estensione macchina virtuale consente di semplificare il processo di installazione e di configurare automaticamente l'agente per l'invio di dati all'area di lavoro di Log Analytics specificata. L'agente viene anche aggiornato automaticamente in modo da garantire la presenza delle funzionalità e delle correzioni più recenti.

- [Network Watcher](../articles/network-watcher/network-watcher-monitoring-overview.md) permette di monitorare la macchina virtuale e le risorse associate in relazione alla rete in cui si trovano. È possibile installare l'estensione dell'agente Network Watcher in una [macchina virtuale Linux](../articles/virtual-machines/linux/extensions-nwa.md) o una [macchina virtuale Windows](../articles/virtual-machines/windows/extensions-nwa.md).

## <a name="next-steps"></a>Passaggi successivi
- Eseguire i passaggi inclusi in [Monitorare una macchina virtuale Windows con Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) o [Monitorare una macchina virtuale Linux con l'interfaccia della riga di comando di Azure](../articles/virtual-machines/linux/tutorial-monitoring.md).
- Leggere le procedure consigliate per [monitoraggio e diagnostica](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
