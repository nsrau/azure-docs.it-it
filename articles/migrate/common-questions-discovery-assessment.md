---
title: Domande sull'individuazione, la valutazione e l'analisi delle dipendenze in Azure Migrate
description: Risposte alle domande comuni sull'individuazione, la valutazione e l'analisi delle dipendenze in Azure Migrate.Get answers to common questions about discovery, assessment, and dependency analysis in Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: b4b2a50bc88768d46c82f6bce73447dc901e5dfd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681910"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Analisi di individuazione, valutazione e dipendenza - Domande comuni

Questo articolo risponde alle domande comuni sull'individuazione, la valutazione e l'analisi delle dipendenze in Azure Migrate.This article answers common questions about discovery, assessment, and dependency analysis in Azure Migrate. Se hai altre domande, controlla queste risorse:

- [Domande generali](resources-faq.md) su Azure Migrate
- Domande [sull'appliance Azure Migrate](common-questions-appliance.md)
- Domande sulla [migrazione dei server](common-questions-server-migration.md)
- Ottenere risposte alle domande nel forum di [Azure Migrate](https://aka.ms/AzureMigrateForum)


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Quali aree geografiche sono supportate per l'individuazione e la valutazione con Azure Migrate?

Esaminare le aree geografiche supportate per i cloud [pubblici](migrate-support-matrix.md#supported-geographies-public-cloud) e [governativi.](migrate-support-matrix.md#supported-geographies-azure-government)


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Quante macchine virtuali è possibile individuare con un'appliance?

È possibile individuare fino a 10.000 macchine virtuali VMware, fino a 5.000 macchine virtuali Hyper-V e fino a 250 server fisici utilizzando una singola appliance. Se si dispone di più computer, vedere [Scalabilità di una valutazione Hyper-V](scale-hyper-v-assessment.md), ridimensionamento di [una valutazione VMware](scale-vmware-assessment.md)o ridimensionamento di [una valutazione del server fisico.](scale-physical-assessment.md)

## <a name="i-cant-see-some-vm-types-in-azure-government"></a>Non è possibile visualizzare alcuni tipi di macchine virtuali in Azure per enti pubblici

I tipi di VM supportati per la valutazione e la migrazione dipendono dalla disponibilità nella posizione di Azure per enti pubblici. È possibile [esaminare e confrontare](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) i tipi di macchine virtuali in Azure per enti pubblici.


## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>Le dimensioni della macchina virtuale sono cambiate. Posso eseguire di nuovo una valutazione?

L'appliance di Azure Migrate raccoglie continuamente informazioni sull'ambiente locale.  Una valutazione è uno snapshot temporizzato delle macchine virtuali locali. Se si modificano le impostazioni di una macchina virtuale che si vuole valutare, usare l'opzione Di ricalcola per aggiornare la valutazione con le modifiche più recenti.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Come si individuano le macchine virtuali in un ambiente multi-tenant?

- **VMware:** se un ambiente è condiviso tra tenant e non si desidera individuare le macchine virtuali di un tenant nella sottoscrizione di un altro tenant, creare le credenziali di VMware vCenter Server che possono accedere solo alle macchine virtuali che si desidera individuare. Quindi, usare tali credenziali quando si avvia l'individuazione nell'appliance di Azure Migrate.Then, use those credentials when you start discovery in the Azure Migrate appliance.
- **Hyper-V:** l'individuazione utilizza le credenziali dell'host Hyper-V. Se le macchine virtuali condividono lo stesso host Hyper-V, attualmente non è possibile separare l'individuazione.  

## <a name="do-i-need-vcenter-server"></a>È necessario vCenter Server?

Sì, Azure Migrate richiede vCenter Server in un ambiente VMware per eseguire l'individuazione. Azure Migrate non supporta l'individuazione di host ESXi non gestiti da vCenter Server.

## <a name="what-are-the-sizing-options"></a>Quali sono le opzioni di dimensionamento?

Con il dimensionamento locale, Azure Migrate non considera i dati sulle prestazioni delle macchine virtuali per la valutazione. Azure Migrate valuta le dimensioni delle macchine virtuali in base alla configurazione locale. Con il dimensionamento basato sulle prestazioni, il dimensionamento si basa sui dati di utilizzo.

Ad esempio, se una macchina virtuale locale dispone di quattro core e 8 GB di memoria al 50% di utilizzo della CPU e al 50% di utilizzo della memoria:For example, if an on-premises VM has four cores and 8 GB of memory at 50% CPU utilization and 50% memory utilization:
- Il dimensionamento locale consiglierà uno SKU di vm di Azure con quattro core e 8 GB di memoria.
- Il dimensionamento basato sulle prestazioni consiglia uno SKU della macchina virtuale con due core e 4 GB di memoria perché viene considerata la percentuale di utilizzo.

Analogamente, il dimensionamento del disco dipende dai criteri di dimensionamento e dal tipo di archiviazione:
- Se i criteri di ridimensionamento sono basati sulle prestazioni e il tipo di archiviazione è automatico, Azure Migrate prende in considerazione i valori di IOPS e velocità effettiva del disco quando identifica il tipo di disco di destinazione (Standard o Premium).
- Se i criteri di ridimensionamento sono basati sulle prestazioni e il tipo di archiviazione è Premium, Azure Migrate consiglia uno SKU del disco Premium in base alle dimensioni del disco locale. The same logic is applied to disk sizing when the sizing is as-on-premises and the storage type is Standard or Premium.

## <a name="does-performance-history-and-utilization-affect-sizing"></a>La cronologia delle prestazioni e l'utilizzo influiscono sul dimensionamento?

Sì, la cronologia delle prestazioni e l'utilizzo influiscono sul dimensionamento in Azure Migrate.Yes, performance history and utilization affect sizing in Azure Migrate.

### <a name="performance-history"></a>Cronologia delle prestazioni

Solo per il dimensionamento basato sulle prestazioni, Azure Migrate raccoglie la cronologia delle prestazioni dei computer locali e quindi la usa per consigliare le dimensioni della macchina virtuale e il tipo di disco in Azure:For performance-based sizing only, Azure Migrate collects the performance history of on-premises machines, and then uses it to recommend the VM size and disk type in Azure:

1. L'appliance profila continuamente l'ambiente locale per raccogliere dati sull'utilizzo in tempo reale ogni 20 secondi.
1. L'appliance esegue il rollup dei campioni raccolti di 20 secondi e li utilizza per creare un singolo punto dati ogni 15 minuti.
1. Per creare il punto dati, l'appliance seleziona il valore di picco da tutti i campioni di 20 secondi.
1. The appliance sends the data point to Azure.

### <a name="utilization"></a>Utilizzo

Quando si crea una valutazione in Azure, a seconda della durata delle prestazioni e del valore percentile della cronologia delle prestazioni impostato, Azure Migrate calcola il valore di utilizzo effettivo e quindi lo usa per il ridimensionamento.

Ad esempio, se si imposta la durata delle prestazioni su un giorno e il valore percentile su 95esimo percentile, Azure Migrate ordina i punti campione di 15 minuti inviati dall'agente di raccolta per il giorno precedente in ordine crescente. Sceglie il valore del 95o percentile come utilizzo effettivo.

L'utilizzo del 95esimo valore percentile garantisce che gli outlier vengano ignorati. Gli outlier potrebbero essere inclusi se Azure Migrate usa il 99esimo percentile. Per scegliere l'utilizzo massimo per il periodo senza perdere alcun outlier, impostare Azure Migrate per l'uso del 99o percentile.

## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>Quali sono le valutazioni basate sull'importazione diverse dalle valutazioni con origine di individuazione come appliance?

Le valutazioni basate sull'importazione sono valutazioni create con computer importati in Azure Migrate usando un file CSV. Solo quattro campi sono obbligatori per l'importazione: nome del server, core, memoria e sistema operativo. Ecco alcune cose da notare: 
 - I criteri di conformità sono meno rigorosi nelle valutazioni basate sull'importazione nel parametro del tipo di avvio. Se il tipo di avvio non viene fornito, si presuppone che il computer abbia il tipo di avvio del BIOS e che il computer non sia contrassegnato come **Pronto condizionalmente**. Nelle valutazioni con origine di individuazione come appliance, la conformità viene contrassegnata come **Condizionecondizionale pronta** se il tipo di avvio non è presente. Questa differenza nel calcolo della conformità è dovuto al fatto che gli utenti potrebbero non disporre di tutte le informazioni sui computer nelle prime fasi della pianificazione della migrazione quando vengono eseguite le valutazioni basate sull'importazione. 
 - Le valutazioni di importazione basate sulle prestazioni utilizzano il valore di utilizzo fornito dall'utente per il ridimensionamento corretto dei calcoli. Poiché il valore di utilizzo viene fornito dall'utente, le opzioni **Cronologia prestazioni** e **Utilizzo Percentile** sono disabilitate nelle proprietà della valutazione. Nelle valutazioni con origine di individuazione come appliance, il valore percentile scelto viene prelevato dai dati sulle prestazioni raccolti dall'appliance.

## <a name="what-is-dependency-visualization"></a>Informazioni sulla visualizzazione delle dipendenze

La visualizzazione delle dipendenze consente di valutare i gruppi di macchine virtuali per la migrazione con maggiore sicurezza. La visualizzazione delle dipendenze controlla trasversalmente le dipendenze dei computer prima di eseguire una valutazione. Garantisce che non venga lasciato nulla e consente di evitare interruzioni impreviste durante la migrazione ad Azure.It helps ensure that no is left behind, and it helps avoid unexpected outages when you migrate to Azure. Azure Migrate usa la soluzione Mapping dei servizi in Monitoraggio di Azure per abilitare la visualizzazione delle dipendenze. [Altre informazioni](concepts-dependency-visualization.md)

> [!NOTE]
> L'analisi delle dipendenze basata su agente non è disponibile in Azure per enti pubblici. È possibile utilizzare l'analisi delle dipendenze senza agenti

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Qual è la differenza tra agent-based e agentless?

Le differenze tra la visualizzazione senza agente e la visualizzazione basata su agente sono riepilogate nella tabella.

**Requisito** | **Senza agente** | **Basato su agente**
--- | --- | ---
Supporto | Questa opzione è attualmente in anteprima ed è disponibile solo per le macchine virtuali VMware.This option is currently in preview, and is only available for VMware VMs. [Esaminare i](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) sistemi operativi supportati. | In generale la disponibilità (GA).
Agente | Non è necessario installare agenti su computer che si desidera eseguire il controllo incrociato. | Agenti da installare in ogni computer locale che si desidera analizzare: [l'agente di monitoraggio Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)e l'agente di [dipendenza](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Prerequisiti | [Esaminare](concepts-dependency-visualization.md#agentless-analysis) i prerequisiti e i requisiti di distribuzione. | [Esaminare](concepts-dependency-visualization.md#agent-based-analysis) i prerequisiti e i requisiti di distribuzione.
Log Analytics | Non obbligatorio. | Azure Migrate usa la soluzione [Mappa dei servizi](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) nei log di Monitoraggio di Azure per [la](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) visualizzazione delle dipendenze. [Altre informazioni](concepts-dependency-visualization.md#agent-based-analysis)
Funzionamento | Acquisisce i dati di connessione TCP nei computer abilitati per la visualizzazione delle dipendenze. Dopo l'individuazione, raccoglie i dati a intervalli di cinque minuti. | Gli agenti della mappa dei servizi installati in un computer raccolgono dati sui processi TCP e sulle connessioni in ingresso/in uscita per ogni processo.
Data | Nome del server del computer di origine, processo e nome dell'applicazione.<br/><br/> Nome del server del computer di destinazione, processo, nome dell'applicazione e porta. | Nome del server del computer di origine, processo e nome dell'applicazione.<br/><br/> Nome del server del computer di destinazione, processo, nome dell'applicazione e porta.<br/><br/> Il numero di connessioni, la latenza e le informazioni sul trasferimento dei dati vengono raccolte e disponibili per le query di Log Analytics. 
Visualizzazione | La mappa delle dipendenze di un singolo server può essere visualizzata per una durata da un'ora a 30 giorni. | Mappa delle dipendenze di un singolo server.<br/><br/> La mappa può essere visualizzata solo su un'ora.<br/><br/> Mappa delle dipendenze di un gruppo di server.<br/><br/> Aggiungere e rimuovere server in un gruppo dalla vista mappa.
Esportazione dati | Al momento non è possibile scaricarlo in formato tabulare. | È possibile eseguire query sui dati con Log Analytics.Data can be queryed with Log Analytics.

## <a name="do-i-pay-for-dependency-visualization"></a>Devo pagare per la visualizzazione delle dipendenze?

No. Altre informazioni sui prezzi di Azure Migrate .Learn more about [Azure Migrate pricing](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>Cosa si installa per la visualizzazione delle dipendenze basata su agenti?

Per utilizzare la visualizzazione delle dipendenze basata su agente, scaricare e installare gli agenti in ogni computer locale che si desidera valutare:

- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)
- [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent)
- Se si dispone di computer che non dispongono di connettività Internet, scaricare e installare il gateway di Log Analytics su di essi.

Questi agenti sono necessari solo se si utilizza la visualizzazione delle dipendenze basata su agente.

## <a name="can-i-use-an-existing-workspace"></a>È possibile usare un'area di lavoro esistente?

Sì, per la visualizzazione delle dipendenze basata su agente è possibile collegare un'area di lavoro esistente al progetto di migrazione e utilizzarla per la visualizzazione delle dipendenze. 

## <a name="can-i-export-the-dependency-visualization-report"></a>È possibile esportare il report di visualizzazione delle dipendenze?

No, il report di visualizzazione delle dipendenze nella visualizzazione basata su agente non può essere esportato. Tuttavia, Azure Migrate usa la mappa del servizio ed è possibile usare [l'API REST della mappa del servizio](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) per recuperare le dipendenze in formato JSON.

## <a name="can-i-automate-agent-installation"></a>È possibile automatizzare l'installazione dell'agente?

Per la visualizzazione delle dipendenze basata su agente:For agent-based dependency visualization:

- Utilizzare uno [script per installare l'agente di dipendenza](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples).
- Per MMA, [utilizzare la riga di comando o l'automazione oppure](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration)uno [script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Oltre agli script, è possibile usare strumenti di distribuzione come Microsoft Endpoint Configuration Manager e [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) per distribuire gli agenti.

## <a name="what-operating-systems-does-mma-support"></a>Quali sistemi operativi supporta MMA?

- Visualizzare l'elenco dei [sistemi operativi Windows supportati da MMA.](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)
- Visualizza l'elenco dei [sistemi operativi Linux supportati da MMA.](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>È possibile visualizzare le dipendenze per più di un'ora?

Per la visualizzazione basata su agenti, è possibile visualizzare le dipendenze per un massimo di un'ora. È possibile tornare fino a un mese a una data specifica della cronologia, ma la durata massima per la visualizzazione è un'ora. Ad esempio, è possibile utilizzare la durata del tempo nella mappa delle dipendenze per visualizzare le dipendenze per ieri, ma è possibile visualizzare le dipendenze solo per un intervallo di un'ora. Tuttavia, è possibile usare i log di Monitoraggio di Azure per eseguire query sui [dati delle dipendenze](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) per una durata più lunga.

Per la visualizzazione senza agente, è possibile visualizzare la mappa delle dipendenze di un singolo server da una durata compresa tra un'ora e 30 giorni.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>È possibile visualizzare le dipendenze per gruppi di più di 10 macchine virtuali?

È possibile [visualizzare le dipendenze](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) per i gruppi con un massimo di 10 macchine virtuali. Se si dispone di un gruppo con più di 10 macchine virtuali, è consigliabile suddividere il gruppo in gruppi più piccoli e quindi visualizzare le dipendenze.

## <a name="next-steps"></a>Passaggi successivi

Leggere la [panoramica di Azure Migrate](migrate-services-overview.md).
