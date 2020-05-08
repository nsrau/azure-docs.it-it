---
title: Supporto per la valutazione del server fisico in Azure Migrate
description: Informazioni sul supporto per la valutazione del server fisico con Azure Migrate server Assessment
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 31fd676a339a6c82cec84e0f355ac875f68a653c
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983670"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Matrice di supporto per la valutazione del server fisico 

Questo articolo riepiloga i prerequisiti e i requisiti di supporto quando si valutano i server fisici per la migrazione ad Azure, usando lo strumento [Azure migrate: server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Per eseguire la migrazione di server fisici ad Azure, esaminare la [matrice di supporto](migrate-support-matrix-physical-migration.md)per la migrazione.


Per valutare i server fisici, creare un progetto Azure Migrate e aggiungere lo strumento Server Assessment al progetto. Una volta aggiunto lo strumento, si distribuisce il [dispositivo Azure migrate](migrate-appliance.md). L'appliance individua continuamente i computer locali e invia i metadati del computer e i dati sulle prestazioni in Azure. Al termine dell'individuazione, è possibile raccogliere i computer individuati in gruppi ed eseguire una valutazione per un gruppo.


## <a name="limitations"></a>Limitazioni

**Supporto tecnico** | **Dettagli**
--- | ---
**Limiti di valutazione** | È possibile individuare e valutare fino a 35.000 di server fisici in un singolo [progetto Azure migrate](migrate-support-matrix.md#azure-migrate-projects).
**Limiti del progetto** | È possibile creare più progetti in una sottoscrizione di Azure. Oltre ai server fisici, un progetto può includere macchine virtuali VMware e macchine virtuali Hyper-V, fino ai limiti di valutazione per ciascuno di essi.
**Individuazione** | L'appliance Azure Migrate è in grado di individuare fino a 250 di server fisici.
**Valutazione** | È possibile aggiungere fino a 35.000 computer in un singolo gruppo.<br/><br/> È possibile valutare fino a 35.000 computer in un'unica valutazione.

[Altre](concepts-assessment-calculation.md) informazioni sulle valutazioni.

## <a name="physical-server-requirements"></a>Requisiti per i server fisici

| **Supporto tecnico**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Distribuzione server fisico**       | Il server fisico può essere autonomo o distribuito in un cluster. |
| **Autorizzazioni**           | **Windows:** È necessario essere un amministratore di dominio o un amministratore locale in tutti i server Windows che si desidera individuare. L'account utente deve essere aggiunto a questi gruppi: utenti Gestione remota, utenti di performance monitor e utenti di log delle prestazioni. <br/><br/> **Linux:** È necessario un account radice nei server Linux che si desidera individuare. |
| **Sistema operativo** | Tutti i sistemi operativi server [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) supportati da Azure, ad eccezione di Windows Server 2003 e SUSE Linux.<br/><br/> Sistemi operativi client Windows 10 e Windows 8. |


## <a name="azure-migrate-appliance-requirements"></a>Requisiti dell'appliance di Azure Migrate

Azure Migrate usa l' [appliance Azure migrate](migrate-appliance.md) per l'individuazione e la valutazione. L'appliance per i server fisici può essere eseguita in una macchina virtuale o in un computer fisico. 

- Informazioni sui [requisiti degli appliance](migrate-appliance.md#appliance---physical) per i server fisici.
- Informazioni sugli URL necessari all'appliance per accedere ai cloud [pubblici](migrate-appliance.md#public-cloud-urls) e [governativi](migrate-appliance.md#government-cloud-urls) .
- È possibile impostare l'appliance usando uno [script di PowerShell](how-to-set-up-appliance-physical.md) che è possibile scaricare dal portale di Azure.
In Azure per enti pubblici distribuire l'appliance [usando questo script](deploy-appliance-script-government.md).

## <a name="port-access"></a>Accesso alla porta

Nella tabella seguente sono riepilogati i requisiti di porta per la valutazione.

**Dispositivo** | **Connessione**
--- | ---
**Appliance** | Connessioni in ingresso sulla porta TCP 3389, per consentire le connessioni Desktop remoto al dispositivo.<br/><br/> Connessioni in ingresso sulla porta 44368, per accedere in remoto all'app di gestione Appliance usando l'URL:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Connessioni in uscita sulle porte 443 (HTTPS) per inviare i metadati di individuazione e prestazioni a Azure Migrate.
**Server fisici** | **Windows:** Connessioni in ingresso sulle porte WinRM 5985 (HTTP) e 5986 (HTTPS) per eseguire il pull dei metadati di configurazione e delle prestazioni dai server Windows. <br/><br/> **Linux:**  Connessioni in ingresso sulla porta 22 (UDP), per eseguire il pull dei metadati di configurazione e delle prestazioni dai server Linux. |

## <a name="agent-based-dependency-analysis-requirements"></a>Requisiti di analisi delle dipendenze basate su agenti

L' [analisi](concepts-dependency-visualization.md) delle dipendenze consente di identificare le dipendenze tra computer locali che si vuole valutare e migrare in Azure. Nella tabella sono riepilogati i requisiti per la configurazione dell'analisi delle dipendenze basata su agente. Attualmente solo l'analisi delle dipendenze basata su agenti è supportata per i server fisici.

**Requisito** | **Dettagli** 
--- | --- 
**Prima della distribuzione** | È necessario disporre di un progetto Azure Migrate, con lo strumento Server Assessment aggiunto al progetto.<br/><br/>  La visualizzazione delle dipendenze viene distribuita dopo la configurazione di un appliance Azure Migrate per individuare i computer locali<br/><br/> [Informazioni su come](create-manage-projects.md) creare un progetto per la prima volta.<br/> [Informazioni su come](how-to-assess.md) aggiungere uno strumento di valutazione a un progetto esistente.<br/> Informazioni su come configurare il dispositivo Azure Migrate per la valutazione dei server [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)o fisici.
**Azure Government** | La visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.
**Log Analytics** | Azure Migrate usa la soluzione [mapping dei servizi](../operations-management-suite/operations-management-suite-service-map.md) nei [log di monitoraggio di Azure](../log-analytics/log-analytics-overview.md) per la visualizzazione delle dipendenze.<br/><br/> Si associa un'area di lavoro Log Analytics nuova o esistente a un progetto Azure Migrate. Non è possibile modificare l'area di lavoro per un progetto di Azure Migrate dopo che è stata aggiunta. <br/><br/> L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto Azure Migrate.<br/><br/> L'area di lavoro deve trovarsi nelle aree Stati Uniti orientali, Asia sudorientale o Europa occidentale. Le aree di lavoro in altre aree non possono essere associate a un progetto.<br/><br/> L'area di lavoro deve trovarsi in un'area in cui [mapping dei servizi è supportato](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> In Log Analytics, l'area di lavoro associata a Azure Migrate è contrassegnata con la chiave del progetto di migrazione e il nome del progetto.
**Agenti obbligatori** | In ogni computer che si desidera analizzare installare gli agenti seguenti:<br/><br/> [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Se i computer locali non sono connessi a Internet, è necessario scaricare e installare Log Analytics gateway.<br/><br/> Altre informazioni sull'installazione di [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Area di lavoro Log Analytics** | L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto Azure Migrate.<br/><br/> Azure Migrate supporta le aree di lavoro che risiedono nelle aree Stati Uniti orientali, Asia sudorientale ed Europa occidentale.<br/><br/>  L'area di lavoro deve trovarsi in un'area in cui [mapping dei servizi è supportato](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Non è possibile modificare l'area di lavoro per un progetto di Azure Migrate dopo che è stata aggiunta.
**I costi** | La soluzione Mapping dei servizi non comporta alcun addebito per i primi 180 giorni (dal giorno in cui si associa l'area di lavoro Log Analytics al progetto Azure Migrate)/<br/><br/> Dopo 180 giorni verranno applicati gli addebiti standard di Log Analytics.<br/><br/> L'uso di qualsiasi soluzione diversa da Mapping dei servizi nell'area di lavoro Log Analytics associata genererà [addebiti standard](https://azure.microsoft.com/pricing/details/log-analytics/) per log Analytics.<br/><br/> All'eliminazione del progetto di Azure Migrate, l'area di lavoro non viene eliminata con il progetto. Dopo l'eliminazione del progetto, Mapping dei servizi utilizzo non è gratuito e ogni nodo verrà addebitato in base al livello a pagamento di Log Analytics area di lavoro/<br/><br/>Se sono presenti progetti creati prima di Azure Migrate disponibilità generale (GA-28 febbraio 2018), è possibile che vengano addebitati costi aggiuntivi per l'Mapping dei servizi. Per garantire il pagamento solo dopo 180 giorni, è consigliabile creare un nuovo progetto, perché le aree di lavoro esistenti prima di GA sono ancora addebitabili.
**Gestione** | Quando si registrano gli agenti nell'area di lavoro, è possibile utilizzare l'ID e la chiave forniti dal progetto Azure Migrate.<br/><br/> È possibile usare l'area di lavoro Log Analytics all'esterno di Azure Migrate.<br/><br/> Se si elimina il progetto Azure Migrate associato, l'area di lavoro non viene eliminata automaticamente. [Eliminarlo manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Non eliminare l'area di lavoro creata da Azure Migrate, a meno che non si elimini il progetto Azure Migrate. In caso contrario, la funzionalità di visualizzazione delle dipendenze non funzionerà come previsto.
**Connettività Internet** | Se i computer non sono connessi a Internet, è necessario installare il gateway Log Analytics.
**Azure Government** | L'analisi delle dipendenze basata su agente non è supportata.

## <a name="next-steps"></a>Passaggi successivi

[Preparare la valutazione del server fisico](tutorial-prepare-physical.md).
