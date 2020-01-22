---
title: 'Domande comuni: individuazione, valutazione e analisi delle dipendenze in Azure Migrate'
description: Risposte alle domande più comuni sull'individuazione, la valutazione e l'analisi delle dipendenze in Azure Migrate.
ms.topic: conceptual
ms.date: 12/29/2019
ms.openlocfilehash: 5afda40f4953b8eb51a757034661eb5a3349cd10
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76292366"
---
# <a name="common-questions-about-discovery-assessment-and-dependency-analysis"></a>Domande comuni sull'individuazione, la valutazione e l'analisi delle dipendenze

Questo articolo risponde alle domande più comuni sull'individuazione, la valutazione e l'analisi delle dipendenze in Azure Migrate. Se si hanno altre query dopo aver letto questo articolo, pubblicarle nel [forum Azure migrate](https://aka.ms/AzureMigrateForum). Per altre domande, vedere gli articoli seguenti:

- [Domande generali](resources-faq.md) su Azure migrate.
- [Domande](common-questions-appliance.md) sull'appliance Azure migrate.


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Quante VM è possibile individuare con un'appliance?

È possibile individuare fino a 10.000 VM VMware, fino a 5.000 macchine virtuali Hyper-V e fino a 250 Server con una singola appliance. Se si dispone di più computer nell'ambiente locale, vedere la pagina relativa alla scalabilità di [Hyper-V](scale-hyper-v-assessment.md), [VMware](scale-vmware-assessment.md) e valutazione [fisica](scale-physical-assessment.md) .



## <a name="vm-size-changed-can-i-run-an-assessment-again"></a>Dimensioni macchina virtuale modificate. È possibile eseguire nuovamente una valutazione?

L'appliance Azure Migrate raccoglie continuamente informazioni sull'ambiente locale. Tuttavia, una valutazione è uno snapshot temporizzato delle macchine virtuali locali. Se si modificano le impostazioni in una macchina virtuale che si desidera valutare, utilizzare l'opzione Ricalcola per aggiornare la valutazione con le modifiche più recenti.

### <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Ricerca per categorie individuare le macchine virtuali in un ambiente multi-tenant?

- Per VMware, se l'ambiente è condiviso tra i tenant e non si vuole individuare le macchine virtuali di un tenant nella sottoscrizione di un altro tenant, creare server vCenter credenziali che possono accedere solo alle macchine virtuali che si desidera individuare. Quindi, usare queste credenziali quando si avvia l'individuazione nell'appliance Azure Migrate.
- Per Hyper-V, l'individuazione usa le credenziali dell'host Hyper-V. Se le VM condividono lo stesso host Hyper-V, attualmente non è possibile separare l'individuazione.  


### <a name="do-i-need-vcenter-server-for-vmware-vm-discovery"></a>È necessario server vCenter per l'individuazione di macchine virtuali VMWare?

Sì, Azure Migrate necessario server vCenter per eseguire l'individuazione in un ambiente VMware. Non supporta l'individuazione di host ESXi che non sono gestiti da server vCenter.


## <a name="whats-the-difference-sizing-options"></a>Quali sono le differenze tra le opzioni di ridimensionamento?

Con il ridimensionamento locale, Azure Migrate non considera i dati sulle prestazioni della macchina virtuale per la valutazione. Valuta le dimensioni delle macchine virtuali in base alla configurazione locale. Con il dimensionamento basato sulle prestazioni, il dimensionamento è basato sui dati di utilizzo.

- Se, ad esempio, una macchina virtuale locale ha 4 core e 8 GB di memoria al 50% di utilizzo della CPU e il 50% di utilizzo della memoria, si verificheranno le condizioni seguenti:
    - Il dimensionamento locale consiglierà uno SKU di VM di Azure con 4 core e 8 GB di memoria.
    - Il dimensionamento basato sulle prestazioni consiglierà uno SKU di VM con 2 core e 4 GB di memoria, perché la percentuale di utilizzo è considerata.

- Analogamente, il ridimensionamento del disco dipende da due proprietà di valutazione: criteri di ridimensionamento e tipo di archiviazione.
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

## <a name="do-i-pay-for-dependency-visualization"></a>Si paga per la visualizzazione delle dipendenze?
No. Vedere [altre informazioni](https://azure.microsoft.com/pricing/details/azure-migrate/) sui prezzi di Azure Migrate.

## <a name="what-do-i-install-for-dependency-visualization"></a>Cosa si installa per la visualizzazione delle dipendenze?

Per usare la visualizzazione delle dipendenze è necessario scaricare e installare gli agenti in ogni computer locale da valutare.

È necessario installare gli agenti seguenti in ogni computer:
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Se sono presenti computer senza connettività Internet, è necessario scaricare e installare Log Analytics gateway.

Questi agenti non sono necessari a meno che non si usi la visualizzazione delle dipendenze.

## <a name="can-i-use-an-existing-workspace"></a>È possibile usare un'area di lavoro esistente?

Sì, è possibile aggiungere un'area di lavoro esistente al progetto di migrazione e usarla per la visualizzazione delle dipendenze. [Altre informazioni](concepts-dependency-visualization.md#how-does-it-work)

## <a name="can-i-export-the-dependency-visualization-report"></a>È possibile esportare il report di visualizzazione delle dipendenze?

No, la visualizzazione delle dipendenze non può essere esportata. Tuttavia, Azure Migrate USA Mapping dei servizi ed è possibile usare l' [API REST mapping dei servizi](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) per recuperare le dipendenze in formato JSON.

## <a name="can-i-automate--mmadependency-agent-installation"></a>È possibile automatizzare l'installazione di MMA/Dependency Agent?

Usare questo [script per installare Dependency Agent](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples). Seguire queste [istruzioni per installare MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) usando la riga di comando o l'automazione. Per MMA, usare [questo script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Oltre agli script, è inoltre possibile utilizzare strumenti di distribuzione come System Center Configuration Manager e [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) per distribuire gli agenti.


## <a name="what-operating-systems-does-mma-support"></a>Quali sistemi operativi sono supportati da MMA?

- Visualizzare l'elenco dei [sistemi operativi Windows supportati da MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Visualizzare l'elenco dei [sistemi operativi Linux supportati da MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

## <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>È possibile visualizzare le dipendenze per più di un'ora?
No. È possibile visualizzare le dipendenze per un massimo di un'ora. È possibile tornare a una data specifica nella cronologia, fino a un mese, ma la durata massima per la visualizzazione è di un'ora. Ad esempio, è possibile usare la durata dell'ora nella mappa delle dipendenze per visualizzare le dipendenze per ieri, ma è possibile visualizzare le dipendenze solo per una finestra di un'ora. È tuttavia possibile usare i log di monitoraggio di Azure per [eseguire query sui dati delle dipendenze](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) per un periodo di tempo più lungo.

## <a name="can-visualize-dependencies-for-groups-of-more-than-10-vms"></a>È possibile visualizzare le dipendenze per gruppi di più di 10 VM?
È possibile [visualizzare le dipendenze](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) per i gruppi contenenti fino a 10 macchine virtuali. Se si dispone di un gruppo con più di 10 macchine virtuali, è consigliabile suddividere il gruppo in gruppi più piccoli, quindi visualizzare le dipendenze.




## <a name="next-steps"></a>Passaggi successivi
Leggere la [Panoramica di Azure migrate](migrate-services-overview.md).
