---
title: Visualizzazione delle dipendenze in Azure Migrate
description: Viene fornita una panoramica dei calcoli di valutazione nel servizio Server assessment in Azure Migrate
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: f24656d02e19f422ff26e6b06d1631a9128dff43
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361983"
---
# <a name="dependency-visualization"></a>Visualizzazione delle dipendenze

Questo articolo descrive la visualizzazione delle dipendenze in Azure Migrate: server assessment.

## <a name="what-is-dependency-visualization"></a>Informazioni sulla visualizzazione delle dipendenze

La visualizzazione delle dipendenze consente di identificare le dipendenze tra computer locali che si vuole valutare e migrare in Azure. 

- In Azure Migrate: server assessment è possibile raccogliere i computer in un gruppo e quindi valutare il gruppo. La visualizzazione delle dipendenze consente di raggruppare i computer in modo più accurato, con una sicurezza elevata per la valutazione.
- La visualizzazione delle dipendenze consente di identificare i computer di cui è necessario eseguire la migrazione insieme. È possibile stabilire se i computer sono in uso o se è possibile rimuoverne le autorizzazioni anziché migrare.
- La visualizzazione delle dipendenze contribuisce a garantire che non venga lasciato nulla ed evitare interruzioni delle sorprese durante la migrazione.
- La visualizzazione è particolarmente utile se non si è certi che i computer facciano parte di una distribuzione di app di cui si vuole eseguire la migrazione in Azure.


> [!NOTE]
> La visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.

## <a name="agent-basedagentless-visualization"></a>Visualizzazione basata su agenti o senza agenti

Sono disponibili due opzioni per la distribuzione della visualizzazione delle dipendenze:

- **Basata**su agenti: la visualizzazione delle dipendenze basata su agente richiede l'installazione di agenti in ogni computer locale che si vuole analizzare.
- Senza **agente**: con questa opzione, non è necessario installare gli agenti nei computer che si desidera controllare in modo incrociato. Questa opzione è attualmente in anteprima ed è disponibile solo per le macchine virtuali VMware.


## <a name="agent-based-visualization"></a>Visualizzazione basata su agenti

**Requisito** | **Dettagli** | **Altre informazioni**
--- | --- | ---
**Prima della distribuzione** | È necessario disporre di un progetto Azure Migrate con lo strumento Azure Migrate: server Assessment aggiunto al progetto.<br/><br/>  La visualizzazione delle dipendenze viene distribuita dopo aver configurato un'appliance Azure Migrate per individuare i computer locali. | [Informazioni su come](create-manage-projects.md) creare un progetto per la prima volta.<br/><br/> [Informazioni su come](how-to-assess.md) aggiungere uno strumento di valutazione a un progetto esistente.<br/><br/> Informazioni su come configurare il dispositivo Azure Migrate per la valutazione dei server [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)o fisici.
**Agenti obbligatori** | In ogni computer che si desidera analizzare installare gli agenti seguenti:<br/><br/> [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/><br/> [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Se i computer locali non sono connessi a Internet, è necessario scaricare e installare Log Analytics gateway. | Altre informazioni sull'installazione di [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics** | Azure Migrate usa la soluzione [mapping dei servizi](../operations-management-suite/operations-management-suite-service-map.md) nei [log di monitoraggio di Azure](../log-analytics/log-analytics-overview.md) per la visualizzazione delle dipendenze.<br/><br/> Si associa un'area di lavoro Log Analytics nuova o esistente a un progetto Azure Migrate. Non è possibile modificare l'area di lavoro per un progetto di Azure Migrate dopo che è stata aggiunta. <br/><br/> L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto Azure Migrate.<br/><br/> L'area di lavoro deve trovarsi nelle aree Stati Uniti orientali, Asia sudorientale o Europa occidentale. Le aree di lavoro in altre aree non possono essere associate a un progetto.<br/><br/> L'area di lavoro deve trovarsi in un'area in cui [mapping dei servizi è supportato](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> In Log Analytics, l'area di lavoro associata a Azure Migrate è contrassegnata con la chiave del progetto di migrazione e il nome del progetto.
**I costi** | La soluzione Mapping dei servizi non comporta alcun addebito per i primi 180 giorni (dal giorno in cui si associa l'area di lavoro Log Analytics al progetto Azure Migrate)/<br/><br/> Dopo 180 giorni verranno applicati gli addebiti standard di Log Analytics.<br/><br/> L'uso di qualsiasi soluzione diversa da Mapping dei servizi nell'area di lavoro Log Analytics associata genererà [addebiti standard](https://azure.microsoft.com/pricing/details/log-analytics/) per log Analytics.<br/><br/> All'eliminazione del progetto di Azure Migrate, l'area di lavoro non viene eliminata con il progetto. Dopo l'eliminazione del progetto, Mapping dei servizi utilizzo non è gratuito e ogni nodo verrà addebitato in base al livello a pagamento di Log Analytics area di lavoro/<br/><br/>Se sono presenti progetti creati prima di Azure Migrate disponibilità generale (GA-28 febbraio 2018), è possibile che vengano addebitati costi aggiuntivi per l'Mapping dei servizi. Per garantire il pagamento solo dopo 180 giorni, è consigliabile creare un nuovo progetto, perché le aree di lavoro esistenti prima di GA sono ancora addebitabili.
**Gestione** | Quando si registrano gli agenti nell'area di lavoro, è possibile utilizzare l'ID e la chiave forniti dal progetto Azure Migrate.<br/><br/> È possibile usare l'area di lavoro Log Analytics all'esterno di Azure Migrate.<br/><br/> Se si elimina il progetto Azure Migrate associato, l'area di lavoro non viene eliminata automaticamente. [Eliminarlo manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Non eliminare l'area di lavoro creata da Azure Migrate, a meno che non si elimini il progetto Azure Migrate. In caso contrario, la funzionalità di visualizzazione delle dipendenze non funzionerà come previsto.

## <a name="agentless-visualization"></a>Visualizzazione senza agenti


**Requisito** | **Dettagli** | **Altre informazioni**
--- | --- | ---
**Prima della distribuzione** | È necessario disporre di un progetto Azure Migrate con lo strumento Azure Migrate: server Assessment aggiunto al progetto.<br/><br/>  La visualizzazione delle dipendenze viene distribuita dopo aver configurato un'appliance Azure Migrate per individuare i computer VMWare locali. | [Informazioni su come](create-manage-projects.md) creare un progetto per la prima volta.<br/><br/> [Informazioni su come](how-to-assess.md) aggiungere uno strumento di valutazione a un progetto esistente.<br/><br/> Informazioni su come configurare l'appliance Azure Migrate per la valutazione delle macchine virtuali [VMware](how-to-set-up-appliance-vmware.md) .
**Agenti obbligatori** | Non sono necessari agenti nei computer che si vuole analizzare.
**Sistemi operativi supportati** | Esaminare i [sistemi operativi](migrate-support-matrix-vmware.md#agentless-dependency-visualization) supportati per la visualizzazione senza agenti.
**Macchine virtuali** | **Strumenti VMware**: è necessario che gli strumenti VMware siano installati e in esecuzione nelle macchine virtuali che si desidera analizzare.<br/><br/> **Account**: nell'appliance Azure migrate è necessario aggiungere un account utente che può essere usato per accedere alle macchine virtuali per l'analisi.<br/><br/> **VM Windows**: l'account utente deve essere un amministratore locale o di dominio nel computer.<br/><br/> **VM Linux**: il privilegio radice è obbligatorio per l'account. In alternativa, l'account utente richiede queste due funzionalità nei file/bin/netstat e/bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE. | Informazioni [sul](migrate-appliance.md) dispositivo Azure migrate.
**VMware** | **vCenter**: l'appliance necessita di un account server vCenter con accesso in sola lettura e dei privilegi abilitati per le macchine virtuali > le operazioni Guest.<br/><br/> **Host ESXi**: negli host ESXi che eseguono le macchine virtuali che si vuole analizzare, l'appliance Azure migrate deve essere in grado di connettersi alla porta TCP 443.
**Dati raccolti** |  La visualizzazione delle dipendenze senza agenti funziona acquisendo i dati di connessione TCP dai computer per i quali è abilitata. Dopo l'avvio dell'individuazione delle dipendenze, l'appliance raccoglie i dati dai computer eseguendo il polling ogni cinque minuti:<br/> -Connessioni TCP.<br/> : Nomi dei processi con connessioni attive.<br/> : Nomi delle applicazioni installate che eseguono il processo con connessioni attive.<br/> : Numero di connessioni rilevate a ogni intervallo di polling.


## <a name="next-steps"></a>Passaggi successivi
- [Configurare la visualizzazione delle dipendenze](how-to-create-group-machine-dependencies.md)
- [Provare a visualizzare le dipendenze senza agenti per le](how-to-create-group-machine-dependencies-agentless.md) macchine virtuali VMware.
- Esaminare le [domande comuni](common-questions-discovery-assessment.md#what-is-dependency-visualization) sulla visualizzazione delle dipendenze.


