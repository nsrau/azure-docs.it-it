---
title: Supporto per la valutazione del server fisico con Azure Migrate
description: Informazioni sul supporto per la valutazione del server fisico con Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 489f95bbbbeb261b56f1a3a86da44f5fcce0adf5
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846579"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Matrice di supporto per la valutazione del server fisico 

È possibile usare il [servizio Azure migrate](migrate-overview.md) per valutare ed eseguire la migrazione di computer nel cloud Microsoft Azure. Questo articolo riepiloga le impostazioni e le limitazioni del supporto per la valutazione e la migrazione di server fisici locali.


## <a name="overview"></a>Overview

Per valutare i computer locali per la migrazione ad Azure con questo articolo, è necessario aggiungere lo strumento Azure Migrate: server Assessment a un progetto Azure Migrate. Si distribuisce il [dispositivo Azure migrate](migrate-appliance.md). L'appliance individua continuamente i computer locali e invia i dati di configurazione e delle prestazioni in Azure. Dopo l'individuazione del computer, si raccolgono i computer individuati in gruppi ed è stata eseguita una valutazione per un gruppo

## <a name="limitations"></a>Limitazioni

**Supporto** | **Dettagli**
--- | ---
**Limiti di valutazione**| Individuare e valutare fino a 35.000 di server fisici in un singolo [progetto](migrate-support-matrix.md#azure-migrate-projects).
**Limiti del progetto** | È possibile creare più progetti in una sottoscrizione di Azure. Un progetto può includere VM VMware, macchine virtuali Hyper-V e server fisici, fino ai limiti di valutazione.
**Individuazione** | L'appliance Azure Migrate è in grado di individuare fino a 250 di server fisici.
**Valutazione** | È possibile aggiungere fino a 35.000 computer in un singolo gruppo.<br/><br/> È possibile valutare fino a 35.000 computer in un'unica valutazione.

[Altre](concepts-assessment-calculation.md) informazioni sulle valutazioni.




## <a name="physical-server-requirements"></a>Requisiti del server fisico

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Distribuzione server fisico**       | Il server fisico può essere autonomo o distribuito in un cluster. |
| **Autorizzazioni**           | **Windows:** Configurare un account utente locale in tutti i server Windows che si desidera includere nell'individuazione. L'account utente deve essere aggiunto a questi gruppi: Desktop remoto utenti, Performance Monitor Users e Performance Log Users. <br/> **Linux:** È necessario un account radice nei server Linux che si desidera individuare. |
| **Sistema operativo** | Sono supportati tutti i sistemi operativi [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) , tranne i seguenti:<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="azure-migrate-appliance-requirements"></a>Requisiti dell'appliance di Azure Migrate

Azure Migrate usa l' [appliance Azure migrate](migrate-appliance.md) per l'individuazione e la valutazione. L'appliance per i server fisici può essere eseguita in una macchina virtuale o in un computer fisico. È possibile configurarlo usando uno script di PowerShell che è possibile scaricare dal portale di Azure.

- Informazioni sui [requisiti degli appliance](migrate-appliance.md#appliance---physical) per i server fisici.
- Informazioni sugli [URL](migrate-appliance.md#url-access) a cui l'appliance deve accedere.

## <a name="port-access"></a>Accesso alla porta

Nella tabella seguente sono riepilogati i requisiti di porta per la valutazione.

**Dispositivo** | **Connection**
--- | ---
**Appliance** | Connessioni in ingresso sulla porta TCP 3389 per consentire le connessioni Desktop remoto al dispositivo.<br/> Connessioni in ingresso sulla porta 44368 per accedere in remoto all'app di gestione Appliance usando l'URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Connessioni in uscita sulle porte 443 (HTTPS), 5671 e 5672 (AMQP) per inviare i metadati di individuazione e prestazioni a Azure Migrate.
**Server fisici** | **Windows:** Connessioni in ingresso sulle porte WinRM 5985 (HTTP) e 5986 (HTTPS) per eseguire il pull dei metadati di configurazione e prestazioni dai server Windows. <br/> **Linux:**  Connessioni in ingresso sulla porta 22 (UDP) per eseguire il pull dei metadati di configurazione e delle prestazioni dai server Linux. |

## <a name="agent-based-dependency-visualization"></a>Visualizzazione delle dipendenze basate su agente

La [visualizzazione](concepts-dependency-visualization.md) delle dipendenze consente di visualizzare le dipendenze tra i computer che si desidera valutare e migrare. Per la visualizzazione basata su agenti, i requisiti e le limitazioni sono riepilogati nella tabella seguente.


**Requisito** | **Dettagli**
--- | ---
**Distribuzione** | Prima di distribuire la visualizzazione delle dipendenze, è necessario disporre di un progetto Azure Migrate, con lo strumento Azure Migrate: server Assessment aggiunto al progetto. La visualizzazione delle dipendenze viene distribuita dopo aver configurato un'appliance Azure Migrate per individuare i computer locali.<br/><br/> La visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.
**Elenco dei servizi** | La visualizzazione delle dipendenze basate su agenti usa la soluzione [mapping dei servizi](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) nei [log di monitoraggio di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).<br/><br/> Per eseguire la distribuzione, è necessario associare un'area di lavoro Log Analytics nuova o esistente a un progetto Azure Migrate.
**area di lavoro Log Analytics** | L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto Azure Migrate.<br/><br/> Azure Migrate supporta le aree di lavoro che risiedono nelle aree Stati Uniti orientali, Asia sudorientale ed Europa occidentale.<br/><br/>  L'area di lavoro deve trovarsi in un'area in cui [mapping dei servizi è supportato](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Non è possibile modificare l'area di lavoro per un progetto di Azure Migrate dopo che è stata aggiunta.
**Spese** | La soluzione Mapping dei servizi non comporta alcun addebito per i primi 180 giorni (dal giorno in cui è stata associata l'area di lavoro Log Analytics al progetto Azure Migrate).<br/><br/> Dopo 180 giorni verranno applicati gli addebiti standard di Log Analytics.<br/><br/> L'uso di qualsiasi soluzione diversa da Mapping dei servizi nell'area di lavoro Log Analytics associata genererà addebiti Log Analytics standard.<br/><br/> Se si elimina il progetto Azure Migrate, l'area di lavoro non viene eliminata. Dopo l'eliminazione del progetto, Mapping dei servizi non è gratuito e ogni nodo verrà addebitato in base al livello a pagamento di Log Analytics area di lavoro.
**Agenti** | La visualizzazione delle dipendenze basate su agente richiede l'installazione di due agenti in ogni computer che si vuole analizzare.<br/><br/> - [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> - [Dependency Agent](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
**Connettività Internet** | Se i computer non sono connessi a Internet, è necessario installare il gateway Log Analytics.

## <a name="next-steps"></a>Passaggi successivi

[Preparare la valutazione del server fisico](tutorial-prepare-physical.md).
