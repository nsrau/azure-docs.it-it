---
title: Supporto per la valutazione fisica del server in Azure MigrateSupport for physical server assessment in Azure Migrate
description: Informazioni sul supporto per la valutazione fisica del server con Azure Migrate Server Assessment
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: ae76a6b570ec58e71a8a1728a2a601728030f58c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538155"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Matrice di supporto per la valutazione fisica del server 

Questo articolo riepiloga i prerequisiti e i requisiti di supporto quando si valutano i server fisici per la migrazione ad Azure usando lo strumento [Azure Migrate:Server Assessment.This](migrate-services-overview.md#azure-migrate-server-assessment-tool) article summarizes prerequisites and support requirements when you assess physical servers for migration to Azure, using the Azure Migrate:Server Assessment tool. Se si desidera eseguire la migrazione dei server fisici in Azure, esaminare la matrice di [supporto della migrazione.](migrate-support-matrix-physical-migration.md)


Per valutare i server fisici, creare un progetto Di Azure Migrate e aggiungere lo strumento di valutazione del server al progetto. Dopo aver aggiunto lo strumento, si distribuisce [l'appliance di Azure Migrate.](migrate-appliance.md) L'appliance individua continuamente i computer locali e invia i metadati e i dati sulle prestazioni del computer ad Azure.The appliance continuously discovers on-premises machines, and sends machine metadata and performance data to Azure. Al termine dell'individuazione, si raccolgono i computer individuati in gruppi ed si esegue una valutazione per un gruppo.


## <a name="limitations"></a>Limitazioni

**Supporto** | **Dettagli**
--- | ---
**Limiti di valutazione** | È possibile individuare e valutare fino a 35.000 server fisici in un singolo [progetto di Azure Migrate.](migrate-support-matrix.md#azure-migrate-projects)
**Limiti del progetto** | È possibile creare più progetti in una sottoscrizione di Azure.You can create multiple projects in an Azure subscription. Oltre ai server fisici, un progetto può includere macchine virtuali VMware e macchine virtuali Hyper-V, fino ai limiti di valutazione per ognuno di essi.
**Scoperta** | L'appliance Di miete di Azure può individuare fino a 250 server fisici.
**Valutazione** | È possibile aggiungere fino a 35.000 macchine in un singolo gruppo.<br/><br/> È possibile valutare fino a 35.000 macchine in un'unica valutazione.

[Ulteriori informazioni](concepts-assessment-calculation.md) sulle valutazioni.

## <a name="physical-server-requirements"></a>Requisiti per i server fisici

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Distribuzione del server fisico**       | Il server fisico può essere autonomo o distribuito in un cluster. |
| **Autorizzazioni**           | **Le finestre Windows:** È necessario un account utente locale o di dominio in tutti i server Windows che si desidera individuare. L'account utente deve essere aggiunto a questi gruppi: Utenti desktop remoto, Utenti di Performance Monitor e Utenti registro prestazioni. <br/><br/> **Linux:** È necessario un account radice nei server Linux che si desidera individuare. |
| **Sistema operativo** | Tutti i sistemi operativi [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) supportati da Azure, ad eccezione di Windows Server 2003 e SUSE Linux.|


## <a name="azure-migrate-appliance-requirements"></a>Requisiti dell'appliance di Azure Migrate

Azure Migrate usa [l'appliance di Azure Migrate](migrate-appliance.md) per l'individuazione e la valutazione. L'appliance per i server fisici può essere eseguita in una macchina virtuale o in una macchina fisica. Configurare l'appliance utilizzando uno script di PowerShell scaricato dal portale di Azure.You set the appliance up using a PowerShell script that you download from the Azure portal.

- Informazioni sui [requisiti dell'appliance](migrate-appliance.md#appliance---physical) per i server fisici.
- Informazioni sugli URL a cui l'appliance deve accedere nei cloud [pubblici](migrate-appliance.md#public-cloud-urls) e [governativi.](migrate-appliance.md#government-cloud-urls)

## <a name="port-access"></a>Accesso alla porta

Nella tabella seguente sono riepilogati i requisiti delle porte per la valutazione.

**Dispositivo** | **Connessione**
--- | ---
**Apparecchio** | Connessioni in ingresso sulla porta TCP 3389, per consentire le connessioni desktop remoto all'appliance.<br/><br/> Connessioni in ingresso sulla porta 44368, per accedere in remoto all'app di gestione dell'appliance utilizzando l'URL:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Connessioni in uscita sulle porte 443 (HTTPS) per inviare metadati di individuazione e prestazioni ad Azure Migrate.Outbound connections on ports 443 (HTTPS), to send discovery and performance metadata to Azure Migrate.
**Server fisici** | **Le finestre Windows:** Connessioni in ingresso sulle porte Di Gestione remota Windows 5985 (HTTP) e 5986 (HTTPS), per estrarre i metadati di configurazione e prestazioni dai server Windows. <br/><br/> **Linux:**  Connessioni in ingresso sulla porta 22 (UDP) per estrarre i metadati di configurazione e prestazioni dai server Linux.Inbound connections on port 22 (UDP), to pull configuration and performance metadata from Linux servers. |

## <a name="agent-based-dependency-analysis-requirements"></a>Requisiti di analisi delle dipendenze basati su agente

[L'analisi delle dipendenze](concepts-dependency-visualization.md) consente di identificare le dipendenze tra i computer locali che si desidera valutare ed eseguire la migrazione in Azure.Dependency analysis helps you to identify dependencies between on-premises machines that you want to assess and migrate to Azure. Nella tabella sono riepilogati i requisiti per l'impostazione dell'analisi delle dipendenze basata su agente. Attualmente solo l'analisi delle dipendenze basata su agente è supportata per i server fisici.

**Requisito** | **Dettagli** 
--- | --- 
**Prima della distribuzione** | È necessario disporre di un progetto di Azure Migrate sul posto, con lo strumento di valutazione del server aggiunto al progetto.<br/><br/>  Distribuire la visualizzazione delle dipendenze dopo aver impostato un'appliance di Azure Migrate per individuare i computer locali<br/><br/> [Scopri come](create-manage-projects.md) creare un progetto per la prima volta.<br/> [Informazioni su come](how-to-assess.md) aggiungere uno strumento di valutazione a un progetto esistente.<br/> Informazioni su come configurare l'appliance Azure Migrate per la valutazione di [Hyper-V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)o server fisici.
**Azure Government** | La visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.
**Log Analytics** | Azure Migrate usa la soluzione [Mappa dei servizi](../operations-management-suite/operations-management-suite-service-map.md) nei log di Monitoraggio di Azure per [la](../log-analytics/log-analytics-overview.md) visualizzazione delle dipendenze.<br/><br/> Associare un'area di lavoro di Log Analytics nuova o esistente a un progetto di Azure Migrate.You associate a new or existing Log Analytics workspace with an Azure Migrate project. L'area di lavoro per un progetto di Azure Migrate non può essere modificata dopo l'aggiunta. <br/><br/> L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto Azure Migrate.The workspace must be in the same subscription as the Azure Migrate project.<br/><br/> L'area di lavoro deve risiedere nelle aree degli Stati Uniti orientali, del sud-est asiatico o dell'Europa occidentale. Le aree di lavoro in altre aree non possono essere associate a un progetto.<br/><br/> L'area di lavoro deve trovarsi in un'area in cui la mappa del servizio [è supportata.](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)<br/><br/> In Log Analytics, l'area di lavoro associata ad Azure Migrate è contrassegnata con la chiave del progetto di migrazione e il nome del progetto.
**Agenti richiesti** | In ogni computer che si desidera analizzare, installare i seguenti agenti:<br/><br/> [L'agente di monitoraggio Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [L'agente di dipendenza](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Se i computer locali non sono connessi a Internet, è necessario scaricare e installare il gateway di Log Analytics su di essi.<br/><br/> Ulteriori informazioni sull'installazione [dell'agente di dipendenza](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e di [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Area di lavoro Log Analytics** | L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto Azure Migrate.The workspace must be in the same subscription as the Azure Migrate project.<br/><br/> Azure Migrate supporta le aree di lavoro che risiedano nelle aree degli Stati Uniti orientali, del sud-est asiatico e dell'Europa occidentale.<br/><br/>  L'area di lavoro deve trovarsi in un'area in cui la mappa del servizio [è supportata.](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)<br/><br/> L'area di lavoro per un progetto di Azure Migrate non può essere modificata dopo l'aggiunta.
**Costi** | La soluzione Mappa di servizio non comporta alcun addebito per i primi 180 giorni (dal giorno in cui si associa l'area di lavoro Log Analytics al progetto Di mitele di Azure)/<br/><br/> Dopo 180 giorni verranno applicati gli addebiti standard di Log Analytics.<br/><br/> L'utilizzo di qualsiasi soluzione diversa da Mappa del servizio nell'area di lavoro di Log Analytics associata comporterà costi standard per Log Analytics.Using any solution other than Service Map in the associated Log Analytics workspace will incur [standard charges](https://azure.microsoft.com/pricing/details/log-analytics/) for Log Analytics.<br/><br/> All'eliminazione del progetto di Azure Migrate, l'area di lavoro non viene eliminata con il progetto. Dopo l'eliminazione del progetto, l'utilizzo della mappa del servizio non è gratuito e a ogni nodo verrà addebitato il livello a pagamento dell'area di lavoro di Log Analytics/<br/><br/>Se si hanno progetti creati prima della disponibilità generale di Azure Migrate (GA- 28 febbraio 2018), è possibile che siano stati sostenuti costi aggiuntivi per la mappa del servizio. Per garantire il pagamento dopo soli 180 giorni, ti consigliamo di creare un nuovo progetto, poiché le aree di lavoro esistenti prima di GA sono ancora addebitabili.
**Gestione** | Quando si registrano gli agenti nell'area di lavoro, si usano l'ID e la chiave forniti dal progetto Azure Migrate.When you register agents to the workspace, you use the ID and key provided by the Azure Migrate project.<br/><br/> È possibile usare l'area di lavoro Log Analytics all'esterno di Azure Migrate.<br/><br/> Se si elimina il progetto Azure Migrate associato, l'area di lavoro non viene eliminata automaticamente. [Eliminarlo manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Non eliminare l'area di lavoro creata da Azure Migrate, a meno che non si elimini il progetto Azure Migrate.Don't delete the workspace created by Azure Migrate, unless you delete the Azure Migrate project. In caso contrario, la funzionalità di visualizzazione delle dipendenze non funzionerà come previsto.
**Connettività Internet** | Se i computer non sono connessi a Internet, è necessario installare il gateway di Log Analytics su di essi.
**Azure Government** | L'analisi delle dipendenze basata su agente non è supportata.

## <a name="next-steps"></a>Passaggi successivi

[Prepararsi per la valutazione fisica](tutorial-prepare-physical.md)del server .
