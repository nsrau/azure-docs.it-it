---
title: Analisi delle dipendenze in Azure Migrate server Assessment
description: Viene descritto come utilizzare l'analisi delle dipendenze per la valutazione utilizzando Azure Migrate server assessment.
ms.topic: conceptual
ms.date: 06/14/2020
ms.openlocfilehash: ff563668666207f35fa2ea796d6c909a59df245f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84771343"
---
# <a name="dependency-analysis"></a>Analisi delle dipendenze

Questo articolo descrive l'analisi delle dipendenze in Azure Migrate: server assessment.


L'analisi delle dipendenze identifica le dipendenze tra i computer locali individuati. Questa funzionalità offre i vantaggi seguenti: 

- È possibile raccogliere i computer in gruppi per la valutazione, in modo più accurato, con maggiore sicurezza.
- È possibile identificare i computer di cui è necessario eseguire la migrazione insieme. Questa operazione è particolarmente utile se non si è certi dei computer che fanno parte di una distribuzione di app di cui si vuole eseguire la migrazione ad Azure.
- È possibile stabilire se i computer sono in uso e quali macchine possono essere rimosse anziché migrate.
- L'analisi delle dipendenze contribuisce a garantire che non venga lasciato nulla, evitando così le interruzioni delle sorprese dopo la migrazione.
- [Esaminare](common-questions-discovery-assessment.md#what-is-dependency-visualization) le domande comuni sull'analisi delle dipendenze.


## <a name="analysis-types"></a>Tipi di analisi

Sono disponibili due opzioni per la distribuzione dell'analisi delle dipendenze

**Opzione** | **Dettagli** | **Cloud pubblico** | **Azure per enti pubblici**
----  |---- | ---- 
**Senza agente** | Esegue il polling di dati da macchine virtuali VMware usando le API di vSphere.<br/><br/> Non è necessario installare gli agenti nelle macchine virtuali.<br/><br/> Questa opzione è attualmente in anteprima, solo per le macchine virtuali VMware. | Supportata. | Supportata.
**Analisi basata su agenti** | Usa la [soluzione mapping dei servizi](../azure-monitor/insights/service-map.md) in monitoraggio di Azure per abilitare la visualizzazione e l'analisi delle dipendenze.<br/><br/> È necessario installare gli agenti in ogni computer locale che si vuole analizzare. | Supportato | Non supportato.


## <a name="agentless-analysis"></a>Analisi senza agenti

L'analisi delle dipendenze senza agente consente di acquisire i dati di connessione TCP dai computer per i quali è abilitata. Nessun agente installato nelle macchine virtuali. Le connessioni con lo stesso server di origine e processo, e il server di destinazione, il processo e la porta sono raggruppate logicamente in una dipendenza. È possibile visualizzare i dati delle dipendenze acquisiti in una vista mappa oppure esportarli come CSV. Nessun agente è installato nei computer che si desidera analizzare.

### <a name="dependency-data"></a>Dati sulle dipendenze

Al termine dell'individuazione dei dati delle dipendenze, inizia il polling:

- Il dispositivo Azure Migrate esegue il polling dei dati di connessione TCP dai computer ogni cinque minuti per raccogliere i dati.
- I dati vengono raccolti dalle macchine virtuali guest tramite server vCenter, usando le API di vSphere.
- Il polling raccoglie i dati seguenti:

    - Nome dei processi con connessioni attive.
    - Nome dell'applicazione che esegue processi con connessioni attive.
    - Porta di destinazione sulle connessioni attive.

- I dati raccolti vengono elaborati nell'appliance Azure Migrate, per dedurre le informazioni di identità e vengono inviati a Azure Migrate ogni sei ore


## <a name="agent-based-analysis"></a>Analisi basata su agenti

Per l'analisi basata su agenti, server Assessment usa la soluzione [mapping dei servizi](../azure-monitor/insights/service-map.md) in monitoraggio di Azure. Installare l' [agente Microsoft Monitoring Agent/log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent) e [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent)in ogni computer che si desidera analizzare.

### <a name="dependency-data"></a>Dati sulle dipendenze

L'analisi basata su agenti fornisce questi dati:

- Nome del server del computer di origine, processo, nome dell'applicazione.
- Nome del server del computer di destinazione, processo, nome dell'applicazione e porta.
- Il numero di connessioni, la latenza e le informazioni sul trasferimento dei dati sono raccolte e disponibili per Log Analytics query. 



## <a name="compare-agentless-and-agent-based"></a>Confrontare agenti senza agenti e basati su agenti

Le differenze tra la visualizzazione senza agente e la visualizzazione basata su agenti sono riepilogate nella tabella.

**Requisito** | **Senza agente** | **Basata su agenti**
--- | --- | ---
**Supporto** | In anteprima solo per le macchine virtuali VMware. [Esaminare](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) i sistemi operativi supportati. | In disponibilità generale (GA).
**Agent** | Non sono necessari agenti nei computer che si vuole analizzare. | Agenti necessari in ogni computer locale che si vuole analizzare.
**Log Analytics** | Non obbligatorio. | Azure Migrate usa la soluzione [mapping dei servizi](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) nei [log di monitoraggio di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) per l'analisi delle dipendenze. 
**Processo** | Acquisisce i dati di connessione TCP. Dopo l'individuazione, raccoglie i dati a intervalli di cinque minuti. | Mapping dei servizi agenti installati in un computer raccolgono i dati relativi ai processi TCP e alle connessioni in ingresso/in uscita per ogni processo.
**Dati** | Nome del server del computer di origine, processo, nome dell'applicazione.<br/><br/> Nome del server del computer di destinazione, processo, nome dell'applicazione e porta. | Nome del server del computer di origine, processo, nome dell'applicazione.<br/><br/> Nome del server del computer di destinazione, processo, nome dell'applicazione e porta.<br/><br/> Il numero di connessioni, la latenza e le informazioni sul trasferimento dei dati sono raccolte e disponibili per Log Analytics query. 
**Visualizzazione** | La mappa delle dipendenze di un singolo server può essere visualizzata per una durata di un'ora a 30 giorni. | Mappa delle dipendenze di un singolo server.<br/><br/> Mappa delle dipendenze di un gruppo di server.<br/><br/>  La mappa può essere visualizzata solo in un'ora.<br/><br/> Aggiungere e rimuovere i server in un gruppo dalla vista mappa.
Esportazione dati | Ultimi 30 giorni è possibile scaricare i dati in formato CSV. | È possibile eseguire query sui dati con Log Analytics.



## <a name="next-steps"></a>Passaggi successivi

- [Configurare la](how-to-create-group-machine-dependencies.md) visualizzazione delle dipendenze basate su agenti.
- [Provare](how-to-create-group-machine-dependencies-agentless.md) a visualizzare le dipendenze senza agenti per le macchine virtuali VMware.
- Esaminare le [domande comuni](common-questions-discovery-assessment.md#what-is-dependency-visualization) sulla visualizzazione delle dipendenze.


