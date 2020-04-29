---
title: Analisi delle dipendenze in Azure Migrate server Assessment
description: Viene descritto come utilizzare l'analisi delle dipendenze per la valutazione utilizzando Azure Migrate server assessment.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: f0b956620895ae2264b53916015d440f5e586eb2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024762"
---
# <a name="dependency-analysis"></a>Analisi delle dipendenze

Questo articolo descrive l'analisi delle dipendenze in Azure Migrate: server assessment.

## <a name="overview"></a>Panoramica

L'analisi delle dipendenze consente di identificare le dipendenze tra computer locali che si vuole valutare e migrare in Azure. 

- In Azure Migrate: server assessment è possibile raccogliere i computer in un gruppo e quindi valutare il gruppo. L'analisi delle dipendenze consente di raggruppare i computer in modo più accurato, con una sicurezza elevata per la valutazione.
- L'analisi delle dipendenze consente di identificare i computer di cui è necessario eseguire la migrazione insieme. È possibile stabilire se i computer sono in uso o se è possibile rimuoverne le autorizzazioni anziché migrare.
- L'analisi delle dipendenze contribuisce a garantire che non venga lasciato nulla ed evitare interruzioni delle sorprese durante la migrazione.
- L'analisi è particolarmente utile se non si è certi che i computer facciano parte di una distribuzione di app di cui si vuole eseguire la migrazione in Azure.
- [Esaminare](common-questions-discovery-assessment.md#what-is-dependency-visualization) le domande comuni sull'analisi delle dipendenze.

Sono disponibili due opzioni per la distribuzione dell'analisi delle dipendenze

- **Basata**su agenti: l'analisi delle dipendenze basata su agente richiede l'installazione di agenti in ogni computer locale che si vuole analizzare.
- Senza **agente**: con l'analisi senza agenti, non è necessario installare gli agenti nei computer che si desidera controllare in modo incrociato. Questa opzione è attualmente in anteprima ed è disponibile solo per le macchine virtuali VMware.

> [!NOTE]
> L'analisi delle dipendenze basata su agente non è disponibile in Azure per enti pubblici. È possibile usare l'analisi delle dipendenze senza agenti.

## <a name="agentless-analysis"></a>Analisi senza agenti

L'analisi delle dipendenze senza agente consente di acquisire i dati di connessione TCP dai computer per i quali è abilitata. Nessun agente è installato nei computer che si desidera analizzare.

### <a name="collected-data"></a>Dati raccolti

Dopo l'avvio dell'individuazione delle dipendenze, l'Appliance esegue il polling dei dati dai computer ogni cinque minuti per raccogliere i dati. Questi dati vengono raccolti dalle macchine virtuali guest tramite server vCenter, usando le API di vSphere. I dati raccolti vengono elaborati nell'appliance Azure Migrate, per dedurre le informazioni di identità e vengono inviati a Azure Migrate ogni sei ore.

Il polling raccoglie i dati dai computer: 
- Nome dei processi con connessioni attive.
- Nome dell'applicazione che esegue processi con connessioni attive.
- Porta di destinazione sulle connessioni attive.

## <a name="agent-based-analysis"></a>Analisi basata su agenti

Per l'analisi basata su agenti, server Assessment usa la [soluzione mapping dei servizi](../azure-monitor/insights/service-map.md) in monitoraggio di Azure per abilitare la visualizzazione e l'analisi delle dipendenze. È necessario installare l' [agente Microsoft Monitoring Agent/log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent) e [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent)in ogni computer che si desidera analizzare.

### <a name="collected-data"></a>Dati raccolti

Per l'analisi basata su agenti vengono raccolti i dati seguenti:

- Nome del server del computer di origine, processo, nome dell'applicazione.
- Nome del server del computer di destinazione, processo, nome dell'applicazione e porta.
- Il numero di connessioni, la latenza e le informazioni sul trasferimento dei dati sono raccolte e disponibili per Log Analytics query. 


## <a name="compare-agentless-and-agent-based"></a>Confrontare agenti senza agenti e basati su agenti

Le differenze tra la visualizzazione senza agente e la visualizzazione basata su agenti sono riepilogate nella tabella.

**Requisito** | **Senza agente** | **Basata su agenti**
--- | --- | ---
Supporto | Questa opzione è attualmente in anteprima ed è disponibile solo per le macchine virtuali VMware. [Esaminare](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) i sistemi operativi supportati. | In disponibilità generale (GA).
Agente | Non è necessario installare gli agenti nei computer che si vuole controllare in modo incrociato. | Agenti da installare in ogni computer locale che si vuole analizzare: [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)e [Dependency Agent](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Log Analytics | Non obbligatorio. | Azure Migrate usa la soluzione [mapping dei servizi](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) nei [log di monitoraggio di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) per l'analisi delle dipendenze. 
Come funziona | Acquisisce i dati di connessione TCP nei computer abilitati per la visualizzazione delle dipendenze. Dopo l'individuazione, raccoglie i dati a intervalli di cinque minuti. | Mapping dei servizi agenti installati in un computer raccolgono i dati relativi ai processi TCP e alle connessioni in ingresso/in uscita per ogni processo.
Data | Nome del server del computer di origine, processo, nome dell'applicazione.<br/><br/> Nome del server del computer di destinazione, processo, nome dell'applicazione e porta. | Nome del server del computer di origine, processo, nome dell'applicazione.<br/><br/> Nome del server del computer di destinazione, processo, nome dell'applicazione e porta.<br/><br/> Il numero di connessioni, la latenza e le informazioni sul trasferimento dei dati sono raccolte e disponibili per Log Analytics query. 
Visualizzazione | La mappa delle dipendenze di un singolo server può essere visualizzata per una durata di un'ora a 30 giorni. | Mappa delle dipendenze di un singolo server.<br/><br/> La mappa può essere visualizzata solo in un'ora.<br/><br/> Mappa delle dipendenze di un gruppo di server.<br/><br/> Aggiungere e rimuovere i server in un gruppo dalla vista mappa.
Esportazione dati | Attualmente non è possibile scaricare il formato tabulare. | È possibile eseguire query sui dati con Log Analytics.



## <a name="next-steps"></a>Passaggi successivi
- Esaminare i requisiti per la configurazione dell'analisi basata su agenti per le macchine virtuali [VMware](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements), i [server fisici](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)e le [VM Hyper-V](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements).
- [Esaminare](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) i requisiti per l'analisi senza agenti delle macchine virtuali VMware.
- [Configurare la](how-to-create-group-machine-dependencies.md) visualizzazione delle dipendenze basate su agenti
- [Provare](how-to-create-group-machine-dependencies-agentless.md) a visualizzare le dipendenze senza agenti per le macchine virtuali VMware.
- Esaminare le [domande comuni](common-questions-discovery-assessment.md#what-is-dependency-visualization) sulla visualizzazione delle dipendenze.


