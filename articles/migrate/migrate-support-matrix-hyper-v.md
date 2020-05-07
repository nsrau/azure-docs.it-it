---
title: Supporto per la valutazione di Hyper-V in Azure Migrate
description: Informazioni sul supporto per la valutazione di Hyper-V con Azure Migrate server Assessment
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 67fabebf805e38a6bca5dda6e691c263ee235219
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744609"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Matrice di supporto per la valutazione di Hyper-V

Questo articolo riepiloga i prerequisiti e i requisiti di supporto quando si valutano le macchine virtuali Hyper-V per la migrazione ad Azure, usando lo strumento [Azure migrate: server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Se si vuole eseguire la migrazione di macchine virtuali Hyper-V in Azure, vedere la [matrice di supporto](migrate-support-matrix-hyper-v-migration.md)per la migrazione.

Per configurare la valutazione della macchina virtuale Hyper-V, creare un progetto Azure Migrate e aggiungere lo strumento Server Assessment al progetto. Una volta aggiunto lo strumento, si distribuisce il [dispositivo Azure migrate](migrate-appliance.md). L'appliance individua continuamente i computer locali e invia i metadati del computer e i dati sulle prestazioni in Azure. Al termine dell'individuazione, è possibile raccogliere i computer individuati in gruppi ed eseguire una valutazione per un gruppo.


## <a name="limitations"></a>Limitazioni

**Supporto tecnico** | **Dettagli**
--- | ---
**Limiti di valutazione** | È possibile individuare e valutare fino a 35.000 macchine virtuali Hyper-V in un singolo [progetto Azure migrate](migrate-support-matrix.md#azure-migrate-projects).
**Limiti del progetto** | È possibile creare più progetti in una sottoscrizione di Azure. Oltre alle macchine virtuali Hyper-V, un progetto può includere macchine virtuali VMware e server fisici, fino ai limiti di valutazione per ciascuno di essi.
**Individuazione** | L'appliance Azure Migrate è in grado di rilevare fino a 5000 VM Hyper-V.<br/><br/> Il dispositivo può connettersi a un massimo di 300 host Hyper-V.
**Valutazione** | È possibile aggiungere fino a 35.000 computer in un singolo gruppo.<br/><br/> È possibile valutare fino a 35.000 VM in una singola valutazione per un gruppo.

[Altre](concepts-assessment-calculation.md) informazioni sulle valutazioni.



## <a name="hyper-v-host-requirements"></a>Requisiti dell'host Hyper-V

| **Supporto tecnico**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Host Hyper-V**       | L'host Hyper-V può essere autonomo o distribuito in un cluster.<br/><br/> L'host Hyper-V può eseguire Windows Server 2019, Windows Server 2016 o Windows Server 2012 R2.<br/> Non è possibile valutare le macchine virtuali presenti negli host Hyper-V che eseguono Windows Server 2012.
| **Autorizzazioni**           | Sono necessarie autorizzazioni di amministratore per l'host Hyper-V. <br/> Se non si desidera assegnare autorizzazioni di amministratore, creare un account utente locale o di dominio e aggiungere l'account utente a questi gruppi: utenti di gestione remota, amministratori di Hyper-V e performance monitor. |
| **Comunicazione remota di PowerShell**   | La [comunicazione remota di PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/enable-psremoting?view=powershell-7) deve essere abilitata in ogni host Hyper-V. |
| **Replica Hyper-V**       | Se si usa la replica Hyper-V (o sono presenti più macchine virtuali con gli stessi identificatori di VM) e si individuano le VM originali e replicate usando Azure Migrate, la valutazione generata da Azure Migrate potrebbe non essere accurata. |


## <a name="hyper-v-vm-requirements"></a>Requisiti della macchina virtuale Hyper-V

| **Supporto tecnico**                  | **Dettagli**               
| :----------------------------- | :------------------- |
| **Sistema operativo** | Tutti i sistemi operativi [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) . |
| **Integration Services**       | Per acquisire le informazioni sul sistema operativo, è necessario che [Hyper-V Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) sia in esecuzione in macchine virtuali valutate. |


## <a name="azure-migrate-appliance-requirements"></a>Requisiti dell'appliance di Azure Migrate

Azure Migrate usa l' [appliance Azure migrate](migrate-appliance.md) per l'individuazione e la valutazione. È possibile distribuire l'appliance usando un disco rigido virtuale Hyper-V compresso che è possibile scaricare dal portale o usando uno [script di PowerShell](deploy-appliance-script.md).

- Informazioni sui [requisiti di appliance](migrate-appliance.md#appliance---hyper-v) per Hyper-V.
- Informazioni sugli URL necessari all'appliance per accedere ai cloud [pubblici](migrate-appliance.md#public-cloud-urls) e [governativi](migrate-appliance.md#government-cloud-urls) .
- In Azure per enti pubblici è necessario distribuire l'appliance [con lo script](deploy-appliance-script-government.md).

## <a name="port-access"></a>Accesso alla porta

Nella tabella seguente sono riepilogati i requisiti di porta per la valutazione.

**Dispositivo** | **Connessione**
--- | ---
**Appliance** | Connessioni in ingresso sulla porta TCP 3389 per consentire le connessioni Desktop remoto al dispositivo.<br/><br/> Connessioni in ingresso sulla porta 44368 per accedere in remoto all'app di gestione Appliance usando l'URL:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Connessioni in uscita sulle porte 443 (HTTPS) per inviare i metadati di individuazione e prestazioni a Azure Migrate.
**Host/cluster Hyper-V** | Connessioni in ingresso sulle porte WinRM 5985 (HTTP) e 5986 (HTTPS) per eseguire il pull dei dati relativi ai metadati e alle prestazioni per le macchine virtuali Hyper-V tramite una sessione Common Information Model (CIM).

## <a name="agent-based-dependency-analysis-requirements"></a>Requisiti di analisi delle dipendenze basate su agenti

L' [analisi](concepts-dependency-visualization.md) delle dipendenze consente di identificare le dipendenze tra computer locali che si vuole valutare e migrare in Azure. Nella tabella sono riepilogati i requisiti per la configurazione dell'analisi delle dipendenze basata su agente. Hyper-V supporta attualmente solo la visualizzazione delle dipendenze basate su agenti. 

**Requisito** | **Dettagli** 
--- | --- 
**Prima della distribuzione** | È necessario disporre di un progetto Azure Migrate, con lo strumento Server Assessment aggiunto al progetto.<br/><br/>  La visualizzazione delle dipendenze viene distribuita dopo la configurazione di un appliance Azure Migrate per individuare i computer locali<br/><br/> [Informazioni su come](create-manage-projects.md) creare un progetto per la prima volta.<br/> [Informazioni su come](how-to-assess.md) aggiungere uno strumento di valutazione a un progetto esistente.<br/> Informazioni su come configurare il dispositivo Azure Migrate per la valutazione delle [macchine virtuali Hyper-V](how-to-set-up-appliance-hyper-v.md).
**Azure Government** | La visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.
**Log Analytics** | Azure Migrate usa la soluzione [mapping dei servizi](../operations-management-suite/operations-management-suite-service-map.md) nei [log di monitoraggio di Azure](../log-analytics/log-analytics-overview.md) per la visualizzazione delle dipendenze.<br/><br/> Si associa un'area di lavoro Log Analytics nuova o esistente a un progetto Azure Migrate. Non è possibile modificare l'area di lavoro per un progetto di Azure Migrate dopo che è stata aggiunta. <br/><br/> L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto Azure Migrate.<br/><br/> L'area di lavoro deve trovarsi nelle aree Stati Uniti orientali, Asia sudorientale o Europa occidentale. Le aree di lavoro in altre aree non possono essere associate a un progetto.<br/><br/> L'area di lavoro deve trovarsi in un'area in cui [mapping dei servizi è supportato](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> In Log Analytics, l'area di lavoro associata a Azure Migrate è contrassegnata con la chiave del progetto di migrazione e il nome del progetto.
**Agenti obbligatori** | In ogni computer che si desidera analizzare installare gli agenti seguenti:<br/><br/> [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Se i computer locali non sono connessi a Internet, è necessario scaricare e installare Log Analytics gateway.<br/><br/> Altre informazioni sull'installazione di [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Area di lavoro Log Analytics** | L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto Azure Migrate.<br/><br/> Azure Migrate supporta le aree di lavoro che risiedono nelle aree Stati Uniti orientali, Asia sudorientale ed Europa occidentale.<br/><br/>  L'area di lavoro deve trovarsi in un'area in cui [mapping dei servizi è supportato](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Non è possibile modificare l'area di lavoro per un progetto di Azure Migrate dopo che è stata aggiunta.
**I costi** | La soluzione Mapping dei servizi non comporta alcun addebito per i primi 180 giorni (dal giorno in cui si associa l'area di lavoro Log Analytics al progetto Azure Migrate)/<br/><br/> Dopo 180 giorni verranno applicati gli addebiti standard di Log Analytics.<br/><br/> L'uso di qualsiasi soluzione diversa da Mapping dei servizi nell'area di lavoro Log Analytics associata genererà [addebiti standard](https://azure.microsoft.com/pricing/details/log-analytics/) per log Analytics.<br/><br/> All'eliminazione del progetto di Azure Migrate, l'area di lavoro non viene eliminata con il progetto. Dopo l'eliminazione del progetto, Mapping dei servizi utilizzo non è gratuito e ogni nodo verrà addebitato in base al livello a pagamento di Log Analytics area di lavoro/<br/><br/>Se sono presenti progetti creati prima di Azure Migrate disponibilità generale (GA-28 febbraio 2018), è possibile che vengano addebitati costi aggiuntivi per l'Mapping dei servizi. Per garantire il pagamento solo dopo 180 giorni, è consigliabile creare un nuovo progetto, perché le aree di lavoro esistenti prima di GA sono ancora addebitabili.
**Gestione** | Quando si registrano gli agenti nell'area di lavoro, è possibile utilizzare l'ID e la chiave forniti dal progetto Azure Migrate.<br/><br/> È possibile usare l'area di lavoro Log Analytics all'esterno di Azure Migrate.<br/><br/> Se si elimina il progetto Azure Migrate associato, l'area di lavoro non viene eliminata automaticamente. [Eliminarlo manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Non eliminare l'area di lavoro creata da Azure Migrate, a meno che non si elimini il progetto Azure Migrate. In caso contrario, la funzionalità di visualizzazione delle dipendenze non funzionerà come previsto.
**Connettività Internet** | Se i computer non sono connessi a Internet, è necessario installare il gateway Log Analytics.
**Azure Government** | L'analisi delle dipendenze basata su agente non è supportata.

## <a name="next-steps"></a>Passaggi successivi

[Preparare la valutazione della macchina virtuale Hyper-V](tutorial-prepare-hyper-v.md)
