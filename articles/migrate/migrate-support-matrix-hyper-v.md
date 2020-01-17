---
title: Supporto per la valutazione di Hyper-V in Azure Migrate
description: Informazioni sul supporto per la valutazione di Hyper-V con Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 1a036e2f22bb1fd9dac65a3cc643224ecbea3c69
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154806"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Matrice di supporto per la valutazione di Hyper-V

Questo articolo riepiloga le impostazioni di supporto e le limitazioni per la valutazione delle macchine virtuali Hyper-V con [Azure migrate: server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Per informazioni sulla migrazione di macchine virtuali Hyper-V in Azure, vedere la matrice di [supporto](migrate-support-matrix-hyper-v-migration.md)per la migrazione.

## <a name="overview"></a>Overview

Per valutare i computer locali per la migrazione ad Azure con questo articolo, è necessario aggiungere lo strumento Azure Migrate: server Assessment a un progetto Azure Migrate. Si distribuisce il [dispositivo Azure migrate](migrate-appliance.md). L'appliance individua continuamente i computer locali e invia i dati di configurazione e delle prestazioni in Azure. Dopo l'individuazione del computer, si raccolgono i computer individuati in gruppi ed è stata eseguita una valutazione per un gruppo.


## <a name="limitations"></a>Limitazioni

**Supporto** | **Dettagli**
--- | ---
**Limiti di valutazione**| Individuare e valutare fino a 35.000 macchine virtuali Hyper-V in un singolo [progetto](migrate-support-matrix.md#azure-migrate-projects).
**Limiti del progetto** | È possibile creare più progetti in una sottoscrizione di Azure. Un progetto può includere VM VMware, macchine virtuali Hyper-V e server fisici, fino ai limiti di valutazione.
**Individuazione** | L'appliance Azure Migrate è in grado di rilevare fino a 5000 VM Hyper-V.<br/><br/> Il dispositivo può connettersi a un massimo di 300 host Hyper-V.
**Valutazione** | È possibile aggiungere fino a 35.000 computer in un singolo gruppo.<br/><br/> È possibile valutare fino a 35.000 VM in un'unica valutazione.

[Altre](concepts-assessment-calculation.md) informazioni sulle valutazioni.



## <a name="hyper-v-host-requirements"></a>Requisiti dell'host Hyper-V

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Distribuzione host**       | L'host Hyper-V può essere autonomo o distribuito in un cluster. |
| **Autorizzazioni**           | Sono necessarie autorizzazioni di amministratore per l'host Hyper-V. <br/> In alternativa, se non si desidera assegnare autorizzazioni di amministratore, creare un account utente locale o di dominio e aggiungere l'utente a questi gruppi: utenti di gestione remota, amministratori di Hyper-V e performance monitor. |
| **Sistema operativo host** | Windows Server 2019, Windows Server 2016 o Windows Server 2012 R2.<br/> Non è possibile valutare le macchine virtuali presenti negli host Hyper-V che eseguono Windows Server 2012. |
| **Comunicazione remota di PowerShell**   | Deve essere abilitato in ogni host. |
| **Replica Hyper-V**       | Se si usa la replica Hyper-V (o sono presenti più macchine virtuali con gli stessi identificatori di VM) e si individuano le VM originali e replicate usando Azure Migrate, la valutazione generata da Azure Migrate potrebbe non essere accurata. |


## <a name="hyper-v-vm-requirements"></a>Requisiti della macchina virtuale Hyper-V

| **Supporto**                  | **Dettagli**               
| :----------------------------- | :------------------- |
| **Sistema operativo** | Tutti i sistemi operativi [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) supportati da Azure. |
| **Integration Services**       | Per acquisire le informazioni sul sistema operativo, è necessario che [Hyper-V Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) sia in esecuzione in macchine virtuali valutate. |


## <a name="azure-migrate-appliance-requirements"></a>Requisiti del dispositivo Azure Migrate

Azure Migrate usa l' [appliance Azure migrate](migrate-appliance.md) per l'individuazione e la valutazione. L'appliance per Hyper-V viene eseguita in una macchina virtuale Hyper-V e viene distribuita usando un VHD Hyper-V compresso che è possibile scaricare dal portale di Azure. 

- Informazioni sui [requisiti di appliance](migrate-appliance.md#appliance---hyper-v) per Hyper-V.
- Informazioni sugli [URL](migrate-appliance.md#url-access) a cui l'appliance deve accedere.

## <a name="port-access"></a>Accesso alla porta

Nella tabella seguente sono riepilogati i requisiti di porta per la valutazione.

**Dispositivo** | **Connection**
--- | ---
**Appliance** | Connessioni in ingresso sulla porta TCP 3389 per consentire le connessioni Desktop remoto al dispositivo.<br/> Connessioni in ingresso sulla porta 44368 per accedere in remoto all'app di gestione Appliance usando l'URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Connessioni in uscita sulle porte 443, 5671 e 5672 per inviare i metadati di individuazione e prestazioni a Azure Migrate.
**Host/cluster Hyper-V** | Connessioni in ingresso sulle porte WinRM 5985 (HTTP) e 5986 (HTTPS) per eseguire il pull dei metadati di configurazione e delle prestazioni delle VM Hyper-V usando una sessione di Common Information Model (CIM).

## <a name="agent-based-dependency-visualization"></a>Visualizzazione delle dipendenze basate su agente

La [visualizzazione](concepts-dependency-visualization.md) delle dipendenze consente di visualizzare le dipendenze tra i computer che si desidera valutare e migrare. Per la visualizzazione basata su agenti, i requisiti e le limitazioni sono riepilogati nella tabella seguente.


**Requisito** | **Dettagli**
--- | ---
**Distribuzione** | Prima di distribuire la visualizzazione delle dipendenze, è necessario disporre di un progetto Azure Migrate, con lo strumento Azure Migrate: server Assessment aggiunto al progetto. La visualizzazione delle dipendenze viene distribuita dopo aver configurato un'appliance Azure Migrate per individuare i computer locali.<br/><br/> La visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.
**Elenco dei servizi** | La visualizzazione delle dipendenze basate su agenti usa la soluzione [mapping dei servizi](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) nei [log di monitoraggio di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).<br/><br/> Per eseguire la distribuzione, è necessario associare un'area di lavoro Log Analytics nuova o esistente a un progetto Azure Migrate.
**area di lavoro Log Analytics** | L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto Azure Migrate.<br/><br/> Azure Migrate supporta le aree di lavoro che risiedono nelle aree Stati Uniti orientali, Asia sudorientale ed Europa occidentale.<br/><br/>  L'area di lavoro deve trovarsi in un'area in cui [mapping dei servizi è supportato](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Non è possibile modificare l'area di lavoro per un progetto di Azure Migrate dopo che è stata aggiunta.
**Charges** | La soluzione Mapping dei servizi non comporta alcun addebito per i primi 180 giorni (dal giorno in cui è stata associata l'area di lavoro Log Analytics al progetto Azure Migrate).<br/><br/> Dopo 180 giorni verranno applicati gli addebiti standard di Log Analytics.<br/><br/> L'uso di qualsiasi soluzione diversa da Mapping dei servizi nell'area di lavoro Log Analytics associata genererà addebiti Log Analytics standard.<br/><br/> Se si elimina il progetto Azure Migrate, l'area di lavoro non viene eliminata. Dopo l'eliminazione del progetto, Mapping dei servizi non è gratuito e ogni nodo verrà addebitato in base al livello a pagamento di Log Analytics area di lavoro.
**Agenti** | La visualizzazione delle dipendenze basate su agente richiede l'installazione di due agenti in ogni computer che si vuole analizzare.<br/><br/> - [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> - [Dependency Agent](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
**Connettività Internet** | Se i computer non sono connessi a Internet, è necessario installare il gateway Log Analytics.


## <a name="next-steps"></a>Passaggi successivi

[Preparare la valutazione della macchina virtuale Hyper-V](tutorial-prepare-hyper-v.md)
