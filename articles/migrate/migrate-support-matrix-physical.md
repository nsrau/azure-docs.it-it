---
title: Supporto per la valutazione del server fisico in Azure Migrate
description: Informazioni sul supporto per la valutazione del server fisico con Azure Migrate server Assessment
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: 97da09fa88cc3e69965237cb5b4326b8b59739bd
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423780"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Matrice di supporto per la valutazione del server fisico 

Questo articolo riepiloga i prerequisiti e i requisiti di supporto quando si valutano i server fisici per la migrazione ad Azure, usando lo strumento [Azure migrate: server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Per eseguire la migrazione di server fisici ad Azure, esaminare la [matrice di supporto](migrate-support-matrix-physical-migration.md)per la migrazione.


Per valutare i server fisici, creare un progetto Azure Migrate e aggiungere lo strumento Server Assessment al progetto. Dopo avere aggiunto lo strumento, distribuire [l'appliance di Azure Migrate](migrate-appliance.md). L'appliance individua i computer locali su base continuativa e invia metadati e dati sulle prestazioni dei computer ad Azure. Dopo l'individuazione, è possibile raccogliere i computer individuati in gruppi ed eseguire valutazioni per un gruppo.


## <a name="limitations"></a>Limitazioni

**Supporto** | **Dettagli**
--- | ---
**Limiti di valutazione** | È possibile individuare e valutare fino a 35.000 di server fisici in un singolo [progetto Azure migrate](migrate-support-matrix.md#azure-migrate-projects).
**Limiti di progetto** | In una sottoscrizione di Azure è possibile creare più progetti. Oltre ai server fisici, un progetto può includere macchine virtuali VMware e macchine virtuali Hyper-V, fino ai limiti di valutazione per ciascuno di essi.
**Individuazione** | L'appliance Azure Migrate è in grado di individuare fino a 1000 di server fisici.
**Valutazione** | È possibile aggiungere fino a 35.000 computer in un singolo gruppo.<br/><br/> È possibile valutare fino a 35.000 computer in un'unica valutazione.

[Altre informazioni](concepts-assessment-calculation.md) sulle valutazioni.

## <a name="physical-server-requirements"></a>Requisiti per i server fisici

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Distribuzione server fisico**       | Il server fisico può essere autonomo o distribuito in un cluster. |
| **Autorizzazioni**           | **Windows:** Usare un account di dominio per i computer aggiunti a un dominio e un account locale per computer che non fanno parte di un dominio. L'account utente deve essere aggiunto a questi gruppi: Utenti Gestione remota, Performance Monitor Users e Performance Log Users. <br/><br/> **Linux:** È necessario un account radice nei server Linux che si desidera individuare. |
| **Sistema operativo** | Tutti i sistemi operativi ad eccezione di Windows Server 2003 e SUSE Linux possono essere valutati per la migrazione. |


## <a name="azure-migrate-appliance-requirements"></a>Requisiti dell'appliance di Azure Migrate

Per l'individuazione e la valutazione, Azure Migrate usa l'[appliance di Azure Migrate](migrate-appliance.md). L'appliance per i server fisici può essere eseguita in una macchina virtuale o in un computer fisico. 

- Informazioni sui [requisiti degli appliance](migrate-appliance.md#appliance---physical) per i server fisici.
- Informazioni sugli URL a cui l'appliance deve accedere nei cloud [pubblico](migrate-appliance.md#public-cloud-urls) e [per enti pubblici](migrate-appliance.md#government-cloud-urls).
- È possibile impostare l'appliance usando uno [script di PowerShell](how-to-set-up-appliance-physical.md) che è possibile scaricare dal portale di Azure.
In Azure per enti pubblici distribuire l'appliance [usando questo script](deploy-appliance-script-government.md).

## <a name="port-access"></a>Accesso alla porta

Nella tabella seguente sono riepilogati i requisiti di porta per la valutazione.

**Dispositivo** | **Connection**
--- | ---
**Appliance** | Connessioni in ingresso sulla porta TCP 3389, per consentire le connessioni Desktop remoto al dispositivo.<br/><br/> Connessioni in ingresso sulla porta 44368, per accedere in remoto all'app di gestione Appliance usando l'URL:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Connessioni in uscita sulle porte 443 (HTTPS) per inviare i metadati di individuazione e prestazioni a Azure Migrate.
**Server fisici** | **Windows:** Connessione in ingresso sulla porta WinRM 5985 (HTTP) per il pull dei metadati di configurazione e delle prestazioni dai server Windows. <br/><br/> **Linux:**  Connessioni in ingresso sulla porta 22 (TCP), per eseguire il pull dei metadati di configurazione e delle prestazioni dai server Linux. |

## <a name="agent-based-dependency-analysis-requirements"></a>Requisiti dell'analisi delle dipendenze basata su agente

L'[analisi delle dipendenze](concepts-dependency-visualization.md) consente di identificare le dipendenze tra computer locali che si intende valutare e migrare in Azure. Nella tabella sono riepilogati i requisiti per la configurazione dell'analisi delle dipendenze basata su agente. Attualmente solo l'analisi delle dipendenze basata su agenti è supportata per i server fisici.

**Requisito** | **Dettagli** 
--- | --- 
**Prima della distribuzione** | È necessario disporre di un progetto Azure Migrate con lo strumento Valutazione server aggiunto.<br/><br/>  La visualizzazione delle dipendenze viene distribuita dopo aver configurato un'appliance Azure Migrate per individuare i computer locali<br/><br/> [Informazioni](create-manage-projects.md) su come creare un progetto per la prima volta.<br/> [Informazioni](how-to-assess.md) su come aggiungere uno strumento di valutazione a un progetto esistente.<br/> Informazioni su come configurare l'appliance Azure Migrate per la valutazione di server [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md) o fisici.
**Azure Government** | La visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.
**Log Analytics** | Azure Migrate usa la soluzione [Mapping dei servizi](../azure-monitor/insights/service-map.md) in [Log di Monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md) per la visualizzazione delle dipendenze.<br/><br/> Un'area di lavoro Log Analytics nuova o esistente viene associata al progetto Azure Migrate. Non è possibile modificare l'area di lavoro per un progetto Azure Migrate dopo che è stata aggiunta. <br/><br/> L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto Azure Migrate.<br/><br/> L'area di lavoro deve trovarsi nelle aree Stati Uniti orientali, Asia sud-orientale o Europa occidentale. Non è possibile associare a un progetto aree di lavoro di altre regioni.<br/><br/> L'area di lavoro deve trovarsi in una regione in cui la soluzione [Mapping dei servizi è supportata](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> In Log Analytics, l'area di lavoro associata ad Azure Migrate è contrassegnata con la chiave di migrazione progetto e con il nome del progetto.
**Agenti obbligatori** | In ogni computer che si desidera analizzare, installare gli agenti seguenti:<br/><br/> [Microsoft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md).<br/> [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Se vi sono computer locali non connessi a Internet, è necessario scaricare e installare il gateway di Log Analytics.<br/><br/> Altre informazioni sull'installazione di [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**area di lavoro Log Analytics** | L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto Azure Migrate.<br/><br/> Azure Migrate supporta aree di lavoro nelle regioni Stati Uniti orientali, Asia sud-orientale ed Europa occidentale.<br/><br/>  L'area di lavoro deve trovarsi in una regione in cui la soluzione [Mapping dei servizi](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions) è supportata.<br/><br/> Non è possibile modificare l'area di lavoro per un progetto Azure Migrate dopo che è stata aggiunta.
**Costi** | La soluzione Mapping dei servizi non genera addebiti per i primi 180 giorni a partire dal giorno dell'associazione dell'area di lavoro Log Analytics al progetto di Azure Migrate/<br/><br/> Dopo 180 giorni verranno applicati gli addebiti standard di Log Analytics.<br/><br/> Se si usa una soluzione diversa da Mapping dei servizi nell'area di lavoro Log Analytics associata verranno applicati gli [addebiti standard](https://azure.microsoft.com/pricing/details/log-analytics/) di Log Analytics.<br/><br/> All'eliminazione del progetto di Azure Migrate, l'area di lavoro non viene eliminata con il progetto. Dopo l'eliminazione del progetto, l'uso di Mapping dei servizi non sarà gratuito e ogni nodo verrà addebitato in base al livello a pagamento dell'area di lavoro Log Analytics/<br/><br/>Se sono presenti progetti creati prima che Azure Migrate fosse generalmente disponibile (disponibilità generale: 28 febbraio 2018), è possibile che vengano addebitati costi aggiuntivi per Mapping dei servizi. Per garantire che l'addebito avvenga solo dopo 180 giorni, è consigliabile creare un nuovo progetto, perché le aree di lavoro esistenti prima della disponibilità generale sono ancora addebitabili.
**Gestione** | Quando si registrano agenti nell'area di lavoro, si usano l'ID e la chiave forniti dal progetto Azure Migrate.<br/><br/> È possibile usare l'area di lavoro Log Analytics all'esterno di Azure Migrate.<br/><br/> Se si elimina il progetto Azure Migrate associato, l'area di lavoro non viene eliminata automaticamente. [Eliminarla manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Non eliminare l'area di lavoro creata da Azure Migrate, a meno che non si rimuova il progetto Azure Migrate. In caso contrario, la funzionalità di visualizzazione delle dipendenze non funzionerà come previsto.
**Connettività Internet** | Se vi sono computer non connessi a Internet, è necessario installare il gateway di Log Analytics.
**Azure Government** | L'analisi delle dipendenze basata su agente non è supportata.

## <a name="next-steps"></a>Passaggi successivi

[Preparare la valutazione del server fisico](tutorial-prepare-physical.md).
