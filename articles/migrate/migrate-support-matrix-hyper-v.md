---
title: Supporto per la valutazione di Hyper-V in Azure Migrate
description: Informazioni sul supporto per la valutazione di Hyper-V con Azure Migrate server Assessment
ms.topic: conceptual
ms.date: 06/14/2020
ms.openlocfilehash: 36cc96915380a464f02df5806337a06f022ecbbf
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92311321"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Matrice di supporto per la valutazione di Hyper-V

Questo articolo riepiloga i prerequisiti e i requisiti di supporto quando si valutano le macchine virtuali Hyper-V per la migrazione ad Azure, usando lo strumento [Azure migrate: server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Se si vuole eseguire la migrazione di macchine virtuali Hyper-V in Azure, vedere la [matrice di supporto](migrate-support-matrix-hyper-v-migration.md)per la migrazione.

Per configurare la valutazione della macchina virtuale Hyper-V, creare un progetto Azure Migrate e aggiungere lo strumento Server Assessment al progetto. Dopo avere aggiunto lo strumento, distribuire [l'appliance di Azure Migrate](migrate-appliance.md). L'appliance individua i computer locali su base continuativa e invia metadati e dati sulle prestazioni dei computer ad Azure. Dopo l'individuazione, è possibile raccogliere i computer individuati in gruppi ed eseguire valutazioni per un gruppo.


## <a name="limitations"></a>Limitazioni

**Supporto** | **Dettagli**
--- | ---
**Limiti di valutazione** | È possibile individuare e valutare fino a 35.000 macchine virtuali Hyper-V in un singolo [progetto Azure migrate](migrate-support-matrix.md#azure-migrate-projects).
**Limiti di progetto** | In una sottoscrizione di Azure è possibile creare più progetti. Oltre alle macchine virtuali Hyper-V, un progetto può includere macchine virtuali VMware e server fisici, fino ai limiti di valutazione per ciascuno di essi.
**Individuazione** | L'appliance Azure Migrate è in grado di rilevare fino a 5000 VM Hyper-V.<br/><br/> Il dispositivo può connettersi a un massimo di 300 host Hyper-V.
**Valutazione** | È possibile aggiungere fino a 35.000 computer in un singolo gruppo.<br/><br/> È possibile valutare fino a 35.000 VM in una singola valutazione per un gruppo.

[Altre informazioni](concepts-assessment-calculation.md) sulle valutazioni.



## <a name="hyper-v-host-requirements"></a>Requisiti dell'host Hyper-V

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Host Hyper-V**       | L'host Hyper-V può essere autonomo o distribuito in un cluster.<br/><br/> L'host Hyper-V può eseguire Windows Server 2019, Windows Server 2016 o Windows Server 2012 R2. Sono supportate anche l'installazione dei componenti di base del server di questi sistemi operativi. <br/>Non è possibile valutare le macchine virtuali presenti negli host Hyper-V che eseguono Windows Server 2012.
| **Autorizzazioni**           | Sono necessarie autorizzazioni di amministratore per l'host Hyper-V. <br/> Se non si desidera assegnare autorizzazioni di amministratore, creare un account utente locale o di dominio e aggiungere l'account utente a questi gruppi: utenti di gestione remota, amministratori di Hyper-V e performance monitor. |
| **Comunicazione remota di PowerShell**   | La [comunicazione remota di PowerShell](/powershell/module/microsoft.powershell.core/enable-psremoting?view=powershell-7) deve essere abilitata in ogni host Hyper-V. |
| **Replica Hyper-V**       | Se si usa la replica Hyper-V (o sono presenti più macchine virtuali con gli stessi identificatori di VM) e si individuano le VM originali e replicate usando Azure Migrate, la valutazione generata da Azure Migrate potrebbe non essere accurata. |


## <a name="vm-requirements"></a>Requisiti della macchina virtuale

| **Supporto**                  | **Dettagli**               
| :----------------------------- | :------------------- |
| **Sistema operativo** | Tutti i sistemi operativi possono essere valutati per la migrazione.  |
| **Integration Services**       | Per acquisire le informazioni sul sistema operativo, è necessario che [Hyper-V Integration Services](/virtualization/hyper-v-on-windows/reference/integration-services) sia in esecuzione in macchine virtuali valutate. |
| **Archiviazione** | Disco locale, DAS, JBOD, spazi di archiviazione, CSV, SMB. Queste archiviazione host Hyper-V in cui sono archiviati i dischi rigidi virtuali/VHDX sono supportate. <br/> Sono supportati i controller virtuali IDE e SCSI| 

## <a name="azure-migrate-appliance-requirements"></a>Requisiti dell'appliance di Azure Migrate

Per l'individuazione e la valutazione, Azure Migrate usa l'[appliance di Azure Migrate](migrate-appliance.md). È possibile distribuire l'appliance usando un disco rigido virtuale Hyper-V compresso che è possibile scaricare dal portale o usando uno [script di PowerShell](deploy-appliance-script.md).

- Informazioni sui [requisiti di appliance](migrate-appliance.md#appliance---hyper-v) per Hyper-V.
- Informazioni sugli URL a cui l'appliance deve accedere nei cloud [pubblico](migrate-appliance.md#public-cloud-urls) e [per enti pubblici](migrate-appliance.md#government-cloud-urls).
- In Azure per enti pubblici è necessario distribuire l'appliance [tramite lo script](deploy-appliance-script-government.md).

## <a name="port-access"></a>Accesso alla porta

Nella tabella seguente sono riepilogati i requisiti di porta per la valutazione.

**Dispositivo** | **Connection**
--- | ---
**Appliance** | Connessioni in ingresso sulla porta TCP 3389 per consentire la connessione dal desktop remoto al dispositivo.<br/><br/> Connessioni in ingresso sulla porta 44368 per accedere in remoto all'app di gestione dell'appliance tramite l'URL: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Connessioni in uscita sulle porte 443 (HTTPS) per inviare i metadati di individuazione e prestazioni a Azure Migrate.
**Host/cluster Hyper-V** | Connessione in ingresso sulla porta WinRM 5985 (HTTP) per eseguire il pull dei dati sulle prestazioni e sui metadati per le VM Hyper-V tramite una sessione di Common Information Model (CIM).

## <a name="agent-based-dependency-analysis-requirements"></a>Requisiti dell'analisi delle dipendenze basata su agente

L'[analisi delle dipendenze](concepts-dependency-visualization.md) consente di identificare le dipendenze tra computer locali che si intende valutare e migrare in Azure. Nella tabella sono riepilogati i requisiti per la configurazione dell'analisi delle dipendenze basata su agente. Hyper-V supporta attualmente solo la visualizzazione delle dipendenze basate su agenti. 

**Requisito** | **Dettagli** 
--- | --- 
**Prima della distribuzione** | È necessario disporre di un progetto Azure Migrate con lo strumento Valutazione server aggiunto.<br/><br/>  La visualizzazione delle dipendenze viene distribuita dopo aver configurato un'appliance Azure Migrate per individuare i computer locali<br/><br/> [Informazioni](create-manage-projects.md) su come creare un progetto per la prima volta.<br/> [Informazioni](how-to-assess.md) su come aggiungere uno strumento di valutazione a un progetto esistente.<br/> Informazioni su come configurare il dispositivo Azure Migrate per la valutazione delle [macchine virtuali Hyper-V](how-to-set-up-appliance-hyper-v.md).
**Azure Government** | La visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.
**Log Analytics** | Azure Migrate usa la soluzione [Mapping dei servizi](../azure-monitor/insights/service-map.md) in [Log di Monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md) per la visualizzazione delle dipendenze.<br/><br/> Un'area di lavoro Log Analytics nuova o esistente viene associata al progetto Azure Migrate. Non è possibile modificare l'area di lavoro per un progetto Azure Migrate dopo che è stata aggiunta. <br/><br/> L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto Azure Migrate.<br/><br/> L'area di lavoro deve trovarsi nelle aree Stati Uniti orientali, Asia sud-orientale o Europa occidentale. Non è possibile associare a un progetto aree di lavoro di altre regioni.<br/><br/> L'area di lavoro deve trovarsi in una regione in cui la soluzione [Mapping dei servizi è supportata](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> In Log Analytics, l'area di lavoro associata ad Azure Migrate è contrassegnata con la chiave di migrazione progetto e con il nome del progetto.
**Agenti obbligatori** | In ogni computer che si desidera analizzare, installare gli agenti seguenti:<br/><br/> [Microsoft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md).<br/> [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Se vi sono computer locali non connessi a Internet, è necessario scaricare e installare il gateway di Log Analytics.<br/><br/> Altre informazioni sull'installazione di [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**area di lavoro Log Analytics** | L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto Azure Migrate.<br/><br/> Azure Migrate supporta aree di lavoro nelle regioni Stati Uniti orientali, Asia sud-orientale ed Europa occidentale.<br/><br/>  L'area di lavoro deve trovarsi in una regione in cui la soluzione [Mapping dei servizi](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions) è supportata.<br/><br/> Non è possibile modificare l'area di lavoro per un progetto Azure Migrate dopo che è stata aggiunta.
**Costi** | La soluzione Mapping dei servizi non genera addebiti per i primi 180 giorni a partire dal giorno dell'associazione dell'area di lavoro Log Analytics al progetto di Azure Migrate/<br/><br/> Dopo 180 giorni verranno applicati gli addebiti standard di Log Analytics.<br/><br/> Se si usa una soluzione diversa da Mapping dei servizi nell'area di lavoro Log Analytics associata verranno applicati gli [addebiti standard](https://azure.microsoft.com/pricing/details/log-analytics/) di Log Analytics.<br/><br/> All'eliminazione del progetto di Azure Migrate, l'area di lavoro non viene eliminata con il progetto. Dopo l'eliminazione del progetto, l'uso di Mapping dei servizi non sarà gratuito e ogni nodo verrà addebitato in base al livello a pagamento dell'area di lavoro Log Analytics/<br/><br/>Se sono presenti progetti creati prima che Azure Migrate fosse generalmente disponibile (disponibilità generale: 28 febbraio 2018), è possibile che vengano addebitati costi aggiuntivi per Mapping dei servizi. Per garantire che l'addebito avvenga solo dopo 180 giorni, è consigliabile creare un nuovo progetto, perché le aree di lavoro esistenti prima della disponibilità generale sono ancora addebitabili.
**Gestione** | Quando si registrano agenti nell'area di lavoro, si usano l'ID e la chiave forniti dal progetto Azure Migrate.<br/><br/> È possibile usare l'area di lavoro Log Analytics all'esterno di Azure Migrate.<br/><br/> Se si elimina il progetto Azure Migrate associato, l'area di lavoro non viene eliminata automaticamente. [Eliminarla manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Non eliminare l'area di lavoro creata da Azure Migrate, a meno che non si rimuova il progetto Azure Migrate. In caso contrario, la funzionalità di visualizzazione delle dipendenze non funzionerà come previsto.
**Connettività Internet** | Se vi sono computer non connessi a Internet, è necessario installare il gateway di Log Analytics.
**Azure Government** | L'analisi delle dipendenze basata su agente non è supportata.

## <a name="next-steps"></a>Passaggi successivi

[Preparare la valutazione della macchina virtuale Hyper-V](./tutorial-discover-hyper-v.md)