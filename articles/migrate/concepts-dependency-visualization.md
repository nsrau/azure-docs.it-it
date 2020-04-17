---
title: Analisi delle dipendenze nella valutazione del server di migrazione di AzureDependency analysis in Azure Migrate Server Assessment
description: Viene descritto come usare l'analisi delle dipendenze per la valutazione usando Valutazione server di Azure migrate.Describes how to use dependency analysis for assessment using Azure Migrate Server Assessment.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: b11796f2c5d7c1d87f383e6780444e572352eff5
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537747"
---
# <a name="dependency-analysis"></a>Analisi delle dipendenze

Questo articolo descrive l'analisi delle dipendenze in Azure Migrate:Server Assessment.This article describes dependency analysis in Azure Migrate:Server Assessment.

## <a name="overview"></a>Panoramica

L'analisi delle dipendenze consente di identificare le dipendenze tra i computer locali che si desidera valutare ed eseguire la migrazione in Azure.Dependency analysis helps you to identify dependencies between on-premises machines that you want to assess and migrate to Azure. 

- In Azure Migrate:Server Assessment è possibile raccogliere le macchine in un gruppo e quindi valutare il gruppo. L'analisi delle dipendenze consente di raggruppare le macchine in modo più accurato, con un'elevata sicurezza per la valutazione.
- L'analisi delle dipendenze consente di identificare i computer di cui è necessario eseguire la migrazione insieme. È possibile identificare se i computer sono in uso o se è possibile rimuovere le loro commissioni anziché eseguirne la migrazione.
- L'analisi delle dipendenze garantisce che non venga lasciato nulla indietro ed evita interruzioni a sorpresa durante la migrazione.
- L'analisi è particolarmente utile se non si è certi che i computer facciano parte di una distribuzione di app di cui si vuole eseguire la migrazione in Azure.Analysis is especially useful if you're not sure whether machines are part of an app deployment that you want to migrate to Azure.
- [Esaminare le](common-questions-discovery-assessment.md#what-is-dependency-visualization) domande comuni sull'analisi delle dipendenze.

Sono disponibili due opzioni per la distribuzione dell'analisi delle dipendenze

- **Basato**su agente: l'analisi delle dipendenze basata su agenti richiede l'installazione di agenti in ogni computer locale che si desidera analizzare.
- **Agentless**: Con l'analisi senza agenti, non è necessario installare agenti su computer che si desidera eseguire il controllo incrociato. Questa opzione è attualmente in anteprima ed è disponibile solo per le macchine virtuali VMware.This option is currently in preview, and is only available for VMware VMs.

> [!NOTE]
> L'analisi delle dipendenze basata su agente non è disponibile in Azure per enti pubblici. È possibile utilizzare l'analisi delle dipendenze senza agente.

## <a name="agentless-analysis"></a>Analisi senza agenti

L'analisi delle dipendenze senza agente funziona catturando i dati di connessione TCP dai computer per i quali è abilitato. Nessun agente installato nei computer che si desidera analizzare.

### <a name="collected-data"></a>Dati raccolti

Dopo l'avvio dell'individuazione delle dipendenze, l'appliance esegue il polling dei dati dai computer ogni cinque minuti per raccogliere i dati. Questi dati vengono raccolti dalle macchine virtuali guest tramite vCenter Server, usando le API vSphere.This data is collected from guest VMs via vCenter Server, using vSphere APIs. I dati raccolti vengono elaborati nell'appliance Azure Migrate, per dedurre le informazioni sull'identità e inviati ad Azure Migrate ogni sei ore.

Il polling raccoglie questi dati dalle macchine:Polling gathers this data from machines: 
- Nome dei processi con connessioni attive.
- Nome dell'applicazione che esegue processi con connessioni attive.
- Porta di destinazione sulle connessioni attive.

## <a name="agent-based-analysis"></a>Analisi basata su agenti

Per l'analisi basata su agenti, la valutazione del server usa la [soluzione Mappa dei](../azure-monitor/insights/service-map.md) servizi in Monitoraggio di Azure per abilitare la visualizzazione e l'analisi delle dipendenze. [L'agente Microsoft Monitoring Agent/Log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent) e l'agente di [dipendenza](../azure-monitor/platform/agents-overview.md#dependency-agent)devono essere installati in ogni computer che si desidera analizzare.

### <a name="collected-data"></a>Dati raccolti

Per la visualizzazione basata su agenti, vengono raccolti i dati seguenti:For agent-based visualization, the following data is collected:

- Nome del server del computer di origine, processo e nome dell'applicazione.
- Nome del server del computer di destinazione, processo, nome dell'applicazione e porta.
- Il numero di connessioni, la latenza e le informazioni sul trasferimento dei dati vengono raccolte e disponibili per le query di Log Analytics. 


## <a name="compare-agentless-and-agent-based"></a>Confrontare agentless e basato su agente

Le differenze tra la visualizzazione senza agente e la visualizzazione basata su agente sono riepilogate nella tabella.

**Requisito** | **Senza agente** | **Basato su agente**
--- | --- | ---
Supporto | Questa opzione è attualmente in anteprima ed è disponibile solo per le macchine virtuali VMware.This option is currently in preview, and is only available for VMware VMs. [Esaminare i](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) sistemi operativi supportati. | In generale la disponibilità (GA).
Agente | Non è necessario installare agenti su computer che si desidera eseguire il controllo incrociato. | Agenti da installare in ogni computer locale che si desidera analizzare: [l'agente di monitoraggio Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)e l'agente di [dipendenza](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Log Analytics | Non obbligatorio. | Azure Migrate usa la soluzione [Mappa dei servizi](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) nei log di Monitoraggio di [Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) per l'analisi delle dipendenze. 
Funzionamento | Acquisisce i dati di connessione TCP nei computer abilitati per la visualizzazione delle dipendenze. Dopo l'individuazione, raccoglie i dati a intervalli di cinque minuti. | Gli agenti della mappa dei servizi installati in un computer raccolgono dati sui processi TCP e sulle connessioni in ingresso/in uscita per ogni processo.
Data | Nome del server del computer di origine, processo e nome dell'applicazione.<br/><br/> Nome del server del computer di destinazione, processo, nome dell'applicazione e porta. | Nome del server del computer di origine, processo e nome dell'applicazione.<br/><br/> Nome del server del computer di destinazione, processo, nome dell'applicazione e porta.<br/><br/> Il numero di connessioni, la latenza e le informazioni sul trasferimento dei dati vengono raccolte e disponibili per le query di Log Analytics. 
Visualizzazione | La mappa delle dipendenze di un singolo server può essere visualizzata per una durata da un'ora a 30 giorni. | Mappa delle dipendenze di un singolo server.<br/><br/> La mappa può essere visualizzata solo su un'ora.<br/><br/> Mappa delle dipendenze di un gruppo di server.<br/><br/> Aggiungere e rimuovere server in un gruppo dalla vista mappa.
Esportazione dati | Al momento non è possibile scaricarlo in formato tabulare. | È possibile eseguire query sui dati con Log Analytics.Data can be queryed with Log Analytics.



## <a name="next-steps"></a>Passaggi successivi
- Esaminare i requisiti per la configurazione dell'analisi basata su agente per [le macchine virtuali VMware,](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements)i [server fisici](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)e le macchine [virtuali Hyper-V.](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)
- [Esaminare](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) i requisiti per l'analisi senza agente delle macchine virtuali VMware.Review the requirements for agentless analysis of VMware VMs.
- [Impostare la](how-to-create-group-machine-dependencies.md) visualizzazione delle dipendenze basata su agente
- [Provare la](how-to-create-group-machine-dependencies-agentless.md) visualizzazione delle dipendenze senza agente per le macchine virtuali VMware.Try out agentless dependency visualization for VMware VMs.
- Esaminare [le domande comuni](common-questions-discovery-assessment.md#what-is-dependency-visualization) sulla visualizzazione delle dipendenze.


