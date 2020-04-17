---
title: Supporto per la valutazione Hyper-V in Azure MigrateSupport for Hyper-V assessment in Azure Migrate
description: Informazioni sul supporto per la valutazione Hyper-V con Azure Migrate Server Assessment
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 990d5026d9621c144c31635fabac4416eb9d20e6
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538172"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Matrice di supporto per la valutazione Hyper-VSupport matrix for Hyper-V assessment

Questo articolo riepiloga i prerequisiti e i requisiti di supporto quando si valutano le macchine virtuali Hyper-V per la migrazione ad Azure usando lo strumento [Azure Migrate:Server Assessment.This](migrate-services-overview.md#azure-migrate-server-assessment-tool) article summarizes prerequisites and support requirements when you assess Hyper-V VMs for migration to Azure, using the Azure Migrate:Server Assessment tool. Se si vuole eseguire la migrazione delle macchine virtuali Hyper-V in Azure, esaminare la matrice di supporto della [migrazione.](migrate-support-matrix-hyper-v-migration.md)

Per configurare la valutazione della macchina virtuale Hyper-V, creare un progetto Azure Migrate e aggiungere lo strumento di valutazione del server al progetto. Dopo aver aggiunto lo strumento, si distribuisce [l'appliance di Azure Migrate.](migrate-appliance.md) L'appliance individua continuamente i computer locali e invia i metadati e i dati sulle prestazioni del computer ad Azure.The appliance continuously discovers on-premises machines, and sends machine metadata and performance data to Azure. Al termine dell'individuazione, si raccolgono i computer individuati in gruppi ed si esegue una valutazione per un gruppo.


## <a name="limitations"></a>Limitazioni

**Supporto** | **Dettagli**
--- | ---
**Limiti di valutazione** | È possibile individuare e valutare fino a 35.000 macchine virtuali Hyper-V in un singolo progetto di [Azure Migrate.](migrate-support-matrix.md#azure-migrate-projects)
**Limiti del progetto** | È possibile creare più progetti in una sottoscrizione di Azure.You can create multiple projects in an Azure subscription. Oltre alle macchine virtuali Hyper-V, un progetto può includere macchine virtuali VMware e server fisici, fino ai limiti di valutazione per ognuno di essi.
**Scoperta** | L'appliance Azure Migrate può individuare fino a 5000 macchine virtuali Hyper-V.The Azure Migrate appliance can discover up to 5000 Hyper-V VMs.<br/><br/> L'appliance può connettersi a un massimo di 300 host Hyper-V.
**Valutazione** | È possibile aggiungere fino a 35.000 macchine in un singolo gruppo.<br/><br/> È possibile valutare fino a 35.000 macchine virtuali in un'unica valutazione per un gruppo.

[Ulteriori informazioni](concepts-assessment-calculation.md) sulle valutazioni.



## <a name="hyper-v-host-requirements"></a>Requisiti dell'host Hyper-V

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Host Hyper-V**       | L'host Hyper-V può essere autonomo o distribuito in un cluster.<br/><br/> L'host Hyper-V può eseguire Windows Server 2019, Windows Server 2016 o Windows Server 2012 R2.<br/> Non è possibile valutare le macchine virtuali presenti negli host Hyper-V che eseguono Windows Server 2012.
| **Autorizzazioni**           | Sono necessarie le autorizzazioni di amministratore per l'host Hyper-V. <br/> Se non si desidera assegnare autorizzazioni di amministratore, creare un account utente locale o di dominio e aggiungere l'account utente a questi gruppi: Utenti di gestione remota, Amministratori Hyper-V e Utenti di Performance Monitor. |
| **Comunicazione remota di PowerShell**   | [La comunicazione remota](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/enable-psremoting?view=powershell-7) di PowerShell deve essere abilitata in ogni host Hyper-V.PowerShell remoting must be enabled on each Hyper-V host. |
| **Replica Hyper-V**       | Se si usa la replica Hyper-V (o si hanno più macchine virtuali con gli stessi identificatori di macchina virtuale) e si individuano sia le macchine virtuali originali che le macchine virtuali replicate tramite Azure Migrate, la valutazione generata da Azure Migrate potrebbe non essere accurata. |


## <a name="hyper-v-vm-requirements"></a>Requisiti delle macchine virtuali Hyper-V

| **Supporto**                  | **Dettagli**               
| :----------------------------- | :------------------- |
| **Sistema operativo** | Tutti i sistemi operativi [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux.](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) |
| **Servizi di integrazione**       | [Servizi di integrazione Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) deve essere in esecuzione in macchine virtuali valutate per acquisire informazioni sul sistema operativo. |


## <a name="azure-migrate-appliance-requirements"></a>Requisiti dell'appliance di Azure Migrate

Azure Migrate usa [l'appliance di Azure Migrate](migrate-appliance.md) per l'individuazione e la valutazione. È possibile distribuire l'appliance utilizzando un disco rigido virtuale Hyper-V compresso scaricato dal portale o uno script di [PowerShell.](deploy-appliance-script.md)

- Informazioni sui [requisiti dell'appliance](migrate-appliance.md#appliance---hyper-v) per Hyper-V.
- Informazioni sugli URL a cui l'appliance deve accedere nei cloud [pubblici](migrate-appliance.md#public-cloud-urls) e [governativi.](migrate-appliance.md#government-cloud-urls)
- In Azure per enti pubblici è necessario distribuire l'appliance usando lo script.

## <a name="port-access"></a>Accesso alla porta

Nella tabella seguente sono riepilogati i requisiti delle porte per la valutazione.

**Dispositivo** | **Connessione**
--- | ---
**Apparecchio** | Connessioni in ingresso sulla porta TCP 3389 per consentire le connessioni desktop remoto all'appliance.<br/><br/> Connessioni in ingresso sulla porta 44368 per accedere in remoto all'app di gestione dell'appliance utilizzando l'URL:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Connessioni in uscita sulle porte 443 (HTTPS) per inviare metadati di individuazione e prestazioni ad Azure Migrate.Outbound connections on ports 443 (HTTPS), to send discovery and performance metadata to Azure Migrate.
**Host/cluster Hyper-V** | Connessioni in ingresso sulle porte di Gestione remota Windows 5985 (HTTP) e 5986 (HTTPS), per estrarre i metadati e i dati sulle prestazioni per le macchine virtuali Hyper-V usando una sessione CIM (Common Information Model).

## <a name="agent-based-dependency-analysis-requirements"></a>Requisiti di analisi delle dipendenze basati su agente

[L'analisi delle dipendenze](concepts-dependency-visualization.md) consente di identificare le dipendenze tra i computer locali che si desidera valutare ed eseguire la migrazione in Azure.Dependency analysis helps you to identify dependencies between on-premises machines that you want to assess and migrate to Azure. Nella tabella sono riepilogati i requisiti per l'impostazione dell'analisi delle dipendenze basata su agente. Hyper-V attualmente supporta solo la visualizzazione delle dipendenze basata su agenti. 

**Requisito** | **Dettagli** 
--- | --- 
**Prima della distribuzione** | È necessario disporre di un progetto di Azure Migrate sul posto, con lo strumento di valutazione del server aggiunto al progetto.<br/><br/>  Distribuire la visualizzazione delle dipendenze dopo aver impostato un'appliance di Azure Migrate per individuare i computer locali<br/><br/> [Scopri come](create-manage-projects.md) creare un progetto per la prima volta.<br/> [Informazioni su come](how-to-assess.md) aggiungere uno strumento di valutazione a un progetto esistente.<br/> Informazioni su come configurare l'appliance Azure Migrate per la valutazione delle [macchine virtuali Hyper-V.](how-to-set-up-appliance-hyper-v.md)
**Azure Government** | La visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.
**Log Analytics** | Azure Migrate usa la soluzione [Mappa dei servizi](../operations-management-suite/operations-management-suite-service-map.md) nei log di Monitoraggio di Azure per [la](../log-analytics/log-analytics-overview.md) visualizzazione delle dipendenze.<br/><br/> Associare un'area di lavoro di Log Analytics nuova o esistente a un progetto di Azure Migrate.You associate a new or existing Log Analytics workspace with an Azure Migrate project. L'area di lavoro per un progetto di Azure Migrate non può essere modificata dopo l'aggiunta. <br/><br/> L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto Azure Migrate.The workspace must be in the same subscription as the Azure Migrate project.<br/><br/> L'area di lavoro deve risiedere nelle aree degli Stati Uniti orientali, del sud-est asiatico o dell'Europa occidentale. Le aree di lavoro in altre aree non possono essere associate a un progetto.<br/><br/> L'area di lavoro deve trovarsi in un'area in cui la mappa del servizio [è supportata.](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)<br/><br/> In Log Analytics, l'area di lavoro associata ad Azure Migrate è contrassegnata con la chiave del progetto di migrazione e il nome del progetto.
**Agenti richiesti** | In ogni computer che si desidera analizzare, installare i seguenti agenti:<br/><br/> [L'agente di monitoraggio Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [L'agente di dipendenza](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Se i computer locali non sono connessi a Internet, è necessario scaricare e installare il gateway di Log Analytics su di essi.<br/><br/> Ulteriori informazioni sull'installazione [dell'agente di dipendenza](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e di [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Area di lavoro Log Analytics** | L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto Azure Migrate.The workspace must be in the same subscription as the Azure Migrate project.<br/><br/> Azure Migrate supporta le aree di lavoro che risiedano nelle aree degli Stati Uniti orientali, del sud-est asiatico e dell'Europa occidentale.<br/><br/>  L'area di lavoro deve trovarsi in un'area in cui la mappa del servizio [è supportata.](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)<br/><br/> L'area di lavoro per un progetto di Azure Migrate non può essere modificata dopo l'aggiunta.
**Costi** | La soluzione Mappa di servizio non comporta alcun addebito per i primi 180 giorni (dal giorno in cui si associa l'area di lavoro Log Analytics al progetto Di mitele di Azure)/<br/><br/> Dopo 180 giorni verranno applicati gli addebiti standard di Log Analytics.<br/><br/> L'utilizzo di qualsiasi soluzione diversa da Mappa del servizio nell'area di lavoro di Log Analytics associata comporterà costi standard per Log Analytics.Using any solution other than Service Map in the associated Log Analytics workspace will incur [standard charges](https://azure.microsoft.com/pricing/details/log-analytics/) for Log Analytics.<br/><br/> All'eliminazione del progetto di Azure Migrate, l'area di lavoro non viene eliminata con il progetto. Dopo l'eliminazione del progetto, l'utilizzo della mappa del servizio non è gratuito e a ogni nodo verrà addebitato il livello a pagamento dell'area di lavoro di Log Analytics/<br/><br/>Se si hanno progetti creati prima della disponibilità generale di Azure Migrate (GA- 28 febbraio 2018), è possibile che siano stati sostenuti costi aggiuntivi per la mappa del servizio. Per garantire il pagamento dopo soli 180 giorni, ti consigliamo di creare un nuovo progetto, poiché le aree di lavoro esistenti prima di GA sono ancora addebitabili.
**Gestione** | Quando si registrano gli agenti nell'area di lavoro, si usano l'ID e la chiave forniti dal progetto Azure Migrate.When you register agents to the workspace, you use the ID and key provided by the Azure Migrate project.<br/><br/> È possibile usare l'area di lavoro Log Analytics all'esterno di Azure Migrate.<br/><br/> Se si elimina il progetto Azure Migrate associato, l'area di lavoro non viene eliminata automaticamente. [Eliminarlo manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Non eliminare l'area di lavoro creata da Azure Migrate, a meno che non si elimini il progetto Azure Migrate.Don't delete the workspace created by Azure Migrate, unless you delete the Azure Migrate project. In caso contrario, la funzionalità di visualizzazione delle dipendenze non funzionerà come previsto.
**Connettività Internet** | Se i computer non sono connessi a Internet, è necessario installare il gateway di Log Analytics su di essi.
**Azure Government** | L'analisi delle dipendenze basata su agente non è supportata.

## <a name="next-steps"></a>Passaggi successivi

[Preparare la valutazione della macchina virtuale Hyper-VPrepare for Hyper-V VM assessment](tutorial-prepare-hyper-v.md)
