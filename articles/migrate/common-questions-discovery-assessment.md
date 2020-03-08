---
title: 'Domande comuni: individuazione, valutazione e analisi delle dipendenze in Azure Migrate'
description: Risposte alle domande più comuni sull'individuazione, la valutazione e l'analisi delle dipendenze in Azure Migrate.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 7edc73742b61e4e5e94431c50d14d263bbbea641
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362028"
---
# <a name="common-questions-about-discovery-assessment-and-dependency-analysis"></a>Domande comuni sull'individuazione, la valutazione e l'analisi delle dipendenze

Questo articolo risponde alle domande più comuni sull'individuazione, la valutazione e l'analisi delle dipendenze in Azure Migrate. Per altre domande, vedere gli articoli seguenti:

- [Domande generali](resources-faq.md) su Azure migrate.
- [Domande](common-questions-appliance.md) sull'appliance Azure migrate.
- [Domande](common-questions-server-migration.md) sulla migrazione del server.
- Pubblicare domande nel [Forum di Azure migrate](https://aka.ms/AzureMigrateForum)



## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Quante VM è possibile individuare con un'appliance?

È possibile individuare fino a 10.000 VM VMware, fino a 5.000 macchine virtuali Hyper-V e fino a 250 server fisici con un'unica appliance. Se si dispone di più computer, vedere gli articoli relativi alla scalabilità di [Hyper-V](scale-hyper-v-assessment.md), [VMware](scale-vmware-assessment.md)e valutazione dei [server fisici](scale-physical-assessment.md) .



## <a name="vm-size-changed-can-i-run-an-assessment-again"></a>Dimensioni macchina virtuale modificate. È possibile eseguire nuovamente una valutazione?

Il dispositivo Azure Migrate raccoglie continuamente informazioni sui computer locali e una valutazione è uno snapshot temporizzato. Se si modificano le impostazioni in una macchina virtuale che si desidera valutare, utilizzare l'opzione Ricalcola per aggiornare la valutazione con le modifiche più recenti.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Ricerca per categorie individuare le macchine virtuali in un ambiente multi-tenant?

- **VMware**: se un ambiente viene condiviso tra i tenant e non si vuole individuare le macchine virtuali di un tenant nella sottoscrizione di un altro tenant, creare server vCenter credenziali che possono accedere solo alle macchine virtuali che si desidera individuare. Quindi, usare queste credenziali quando si avvia l'individuazione nell'appliance Azure Migrate.
- **Hyper-v**: l'individuazione usa le credenziali dell'host Hyper-v. Se le VM condividono lo stesso host Hyper-V, attualmente non è possibile separare l'individuazione.  


## <a name="do-i-need-vcenter-server"></a>È necessario server vCenter?

Sì, Azure Migrate necessario server vCenter per eseguire l'individuazione in un ambiente VMware. Non supporta l'individuazione di host ESXi che non sono gestiti da server vCenter.


## <a name="whats-are-the-sizing-options"></a>Quali sono le opzioni di ridimensionamento?

Con il ridimensionamento locale, Azure Migrate non considera i dati sulle prestazioni della macchina virtuale per la valutazione. Valuta le dimensioni delle macchine virtuali in base alla configurazione locale. Con il dimensionamento basato sulle prestazioni, il dimensionamento è basato sui dati di utilizzo.

- Ad esempio, se una macchina virtuale locale ha 4 core e 8 GB di memoria al 50% di utilizzo della CPU e il 50% di memoria:
    - Il dimensionamento locale consiglierà uno SKU di VM di Azure con quattro core e 8 GB di memoria.
    - Il dimensionamento basato sulle prestazioni consiglierà uno SKU di VM con due core e 4 GB di memoria, perché la percentuale di utilizzo è considerata.

- Analogamente, il dimensionamento del disco dipende dai criteri di ridimensionamento e dal tipo di archiviazione.
    - Se i criteri di ridimensionamento sono basati sulle prestazioni e il tipo di archiviazione è automatico, Azure Migrate prende in considerazione i valori di IOPS e velocità effettiva del disco quando identifica il tipo di disco di destinazione (standard o Premium).
    - Se i criteri di ridimensionamento sono basati sulle prestazioni e il tipo di archiviazione è Premium, Azure Migrate consiglia uno SKU del disco Premium, in base alle dimensioni del disco locale. La stessa logica viene applicata al dimensionamento del disco, quando il dimensionamento è locale e il tipo di archiviazione è standard o Premium.

## <a name="does-performance-historyutilization-impact-sizing"></a>La cronologia delle prestazioni e l'utilizzo hanno un effetto sul dimensionamento?

Queste proprietà sono applicabili solo per il dimensionamento basato sulle prestazioni.

- Azure Migrate raccoglie la cronologia delle prestazioni dei computer locali e la usa per consigliare le dimensioni della macchina virtuale e il tipo di disco in Azure.
- L'appliance continua a profilare l'ambiente locale, per raccogliere i dati di utilizzo in tempo reale ogni 20 secondi.
- Il dispositivo esegue il rollup degli esempi di 20 secondi e crea un singolo punto dati ogni 15 minuti.
- Per creare il punto dati, l'appliance seleziona il valore massimo da tutti gli esempi di 20 secondi.
- Il dispositivo invia questo punto dati ad Azure.

Quando si crea una valutazione in Azure, in base alla durata delle prestazioni e al valore percentile cronologia prestazioni, Azure Migrate calcola il valore di utilizzo effettivo e lo usa per il ridimensionamento.

- Se, ad esempio, si imposta la durata delle prestazioni su un giorno e il valore percentile su 95 percentile, Azure Migrate Ordina i punti di campionamento di 15 minuti inviati dall'agente di raccolta per il giorno precedente in ordine crescente e sceglie il valore 95 ° percentile come valido utilizzo.
- L'utilizzo del valore 95 ° percentile garantisce che gli outlier vengano ignorati. Gli outlier possono essere inclusi se si usa il 99 ° percentile. Se si desidera selezionare l'utilizzo di picco per il periodo, senza eventuali outlier, selezionare il 99 ° percentile.

## <a name="what-is-dependency-visualization"></a>Informazioni sulla visualizzazione delle dipendenze

Usare la visualizzazione delle dipendenze per valutare i gruppi di macchine virtuali per la migrazione con maggiore sicurezza. La visualizzazione delle dipendenze controlla le dipendenze tra computer prima di eseguire una valutazione. Consente di garantire che non venga lasciato nulla e di conseguenza consente di evitare interruzioni impreviste quando si esegue la migrazione ad Azure. Azure Migrate usa la soluzione Mapping dei servizi in Monitoraggio di Azure per abilitare la visualizzazione delle dipendenze. [altre informazioni](concepts-dependency-visualization.md).

> [!NOTE]
> La visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Qual è la differenza tra l'agente e l'agente?

Le differenze tra la visualizzazione senza agente e la visualizzazione basata su agenti sono riepilogate nella tabella.

**Requisito** | **Senza agenti** | **Basata su agenti**
--- | --- | ---
Supporto | Questa opzione è attualmente in anteprima ed è disponibile solo per le macchine virtuali VMware. [Esaminare](migrate-support-matrix-vmware.md#agentless-dependency-visualization) i sistemi operativi supportati. | In disponibilità generale (GA).
Agente | Non è necessario installare gli agenti nei computer che si vuole controllare in modo incrociato. | Agenti da installare in ogni computer locale che si vuole analizzare: [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)e [Dependency Agent](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Prerequisites | [Esaminare](concepts-dependency-visualization.md#agentless-visualization) i prerequisiti e i requisiti di distribuzione. | [Esaminare](concepts-dependency-visualization.md#agent-based-visualization) i prerequisiti e i requisiti di distribuzione.
Log Analytics | Non obbligatorio. | Azure Migrate usa la soluzione [mapping dei servizi](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) nei [log di monitoraggio di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) per la visualizzazione delle dipendenze. [Altre informazioni](concepts-dependency-visualization.md#agent-based-visualization).
Funzionamento | Acquisisce i dati di connessione TCP nei computer abilitati per la visualizzazione delle dipendenze. Dopo l'individuazione, raccoglie i dati a intervalli di cinque minuti. | Mapping dei servizi agenti installati in un computer raccolgono i dati relativi ai processi TCP e alle connessioni in ingresso/in uscita per ogni processo.
data | Nome del server del computer di origine, processo, nome dell'applicazione.<br/><br/> Nome del server del computer di destinazione, processo, nome dell'applicazione e porta. | Nome del server del computer di origine, processo, nome dell'applicazione.<br/><br/> Nome del server del computer di destinazione, processo, nome dell'applicazione e porta.<br/><br/> Il numero di connessioni, la latenza e le informazioni sul trasferimento dei dati sono raccolte e disponibili per Log Analytics query. 
Visualizzazione | La mappa delle dipendenze di un singolo server può essere visualizzata per una durata di un'ora a 30 giorni. | Mappa delle dipendenze di un singolo server.<br/><br/> La mappa può essere visualizzata solo in un'ora.<br/><br/> Mappa delle dipendenze di un gruppo di server.<br/><br/> Aggiungere e rimuovere i server in un gruppo dalla vista mappa.
Esportazione dati | Attualmente non è possibile scaricare il formato tabulare. | È possibile eseguire query sui dati con Log Analytics.




## <a name="do-i-pay-for-dependency-visualization"></a>Si paga per la visualizzazione delle dipendenze?
No. Vedere [altre informazioni](https://azure.microsoft.com/pricing/details/azure-migrate/) sui prezzi di Azure Migrate.

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>Cosa si installa per la visualizzazione delle dipendenze basate su agenti?

Per usare la visualizzazione delle dipendenze basata su agenti, è necessario scaricare e installare gli agenti in ogni computer locale che si vuole valutare.

Installare gli agenti seguenti in ogni computer:
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Se sono presenti computer senza connettività Internet, è necessario scaricare e installare Log Analytics gateway.

Questi agenti non sono necessari a meno che non si usi la visualizzazione delle dipendenze basate su agenti.

## <a name="can-i-use-an-existing-workspace"></a>È possibile usare un'area di lavoro esistente?

Sì, per la visualizzazione delle dipendenze basate su agenti, è possibile allegare un'area di lavoro esistente al progetto di migrazione e usarla per la visualizzazione delle dipendenze. 

## <a name="can-i-export-the-dependency-visualization-report"></a>È possibile esportare il report di visualizzazione delle dipendenze?

No, il report di visualizzazione delle dipendenze nella visualizzazione basata su agente non può essere esportato. Tuttavia, Azure Migrate USA Mapping dei servizi ed è possibile usare l' [API REST mapping dei servizi](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) per recuperare le dipendenze in formato JSON.

## <a name="can-i-automate-agent-installation"></a>È possibile automatizzare l'installazione dell'agente?
Per la visualizzazione delle dipendenze basate su agente, automatizzare come segue:

- Usare questo [script per installare Dependency Agent](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples).
- Per MMA, seguire queste [istruzioni](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) per usare la riga di comando o l'automazione oppure usare [questo script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Oltre agli script, è anche possibile usare strumenti di distribuzione come Microsoft endpoint Configuration Manager e [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) per distribuire gli agenti.


## <a name="what-operating-systems-does-mma-support"></a>Quali sistemi operativi sono supportati da MMA?

- Visualizzare l'elenco dei [sistemi operativi Windows supportati da MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Visualizzare l'elenco dei [sistemi operativi Linux supportati da MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

## <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>È possibile visualizzare le dipendenze per più di un'ora?
Per la visualizzazione basata su agenti, è possibile visualizzare le dipendenze per un massimo di un'ora. È possibile tornare a una data specifica nella cronologia, fino a un mese, ma la durata massima per la visualizzazione è di un'ora. Ad esempio, è possibile usare la durata dell'ora nella mappa delle dipendenze per visualizzare le dipendenze per ieri, ma è possibile visualizzare le dipendenze solo per una finestra di un'ora. È tuttavia possibile usare i log di monitoraggio di Azure per [eseguire query sui dati delle dipendenze](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) per un periodo di tempo più lungo.

Per la visualizzazione senza agenti, è possibile visualizzare la mappa delle dipendenze di un singolo server da una durata di un'ora a 30 giorni.


## <a name="can-visualize-dependencies-for-groups-of-more-than-10-vms"></a>È possibile visualizzare le dipendenze per gruppi di più di 10 VM?
È possibile [visualizzare le dipendenze](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) per i gruppi contenenti fino a 10 macchine virtuali. Se si dispone di un gruppo con più di 10 macchine virtuali, è consigliabile suddividere il gruppo in gruppi più piccoli, quindi visualizzare le dipendenze.




## <a name="next-steps"></a>Passaggi successivi
Leggere la [Panoramica di Azure migrate](migrate-services-overview.md).
