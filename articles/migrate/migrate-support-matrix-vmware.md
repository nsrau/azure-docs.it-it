---
title: Supporto per la valutazione VMware in Azure Migrate
description: Informazioni sul supporto per la valutazione di VMware in Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 2fab94b66e09d3923e481326b3650f1beb621dc4
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048756"
---
# <a name="support-matrix-for-vmware-assessment"></a>Matrice di supporto per VMware Assessment 

Questo articolo riepiloga le impostazioni di supporto e le limitazioni per la valutazione delle macchine virtuali VMware con [Azure migrate: server Assessment](migrate-services-overview.md#azure-migrate-server-migration-tool). Per informazioni sulla migrazione di macchine virtuali VMware in Azure, vedere la matrice di [supporto](migrate-support-matrix-vmware-migration.md)per la migrazione.

## <a name="overview"></a>Panoramica

Per valutare i computer locali per la migrazione ad Azure con questo articolo, è necessario aggiungere lo strumento Azure Migrate: server Assessment a un progetto Azure Migrate. Si distribuisce il [dispositivo Azure migrate](migrate-appliance.md). L'appliance individua continuamente i computer locali e invia i dati di configurazione e delle prestazioni in Azure. Dopo l'individuazione del computer, si raccolgono i computer individuati in gruppi ed è stata eseguita una valutazione per un gruppo.


## <a name="limitations"></a>Limitazioni

**Supporto** | **Dettagli**
--- | ---
**Limiti di valutazione**| Individuare e valutare fino a 35.000 macchine virtuali VMware in un singolo [progetto](migrate-support-matrix.md#azure-migrate-projects).
**Limiti del progetto** | È possibile creare più progetti in una sottoscrizione di Azure. Un progetto può includere VM VMware, macchine virtuali Hyper-V e server fisici, fino ai limiti di valutazione.
**Individuazione** | L'appliance Azure Migrate è in grado di rilevare fino a 10.000 VM VMware in una server vCenter.
**Valutazione** | È possibile aggiungere fino a 35.000 computer in un singolo gruppo.<br/><br/> È possibile valutare fino a 35.000 VM in un'unica valutazione.

[Altre](concepts-assessment-calculation.md) informazioni sulle valutazioni.


## <a name="application-discovery"></a>Individuazione delle applicazioni

Oltre a individuare i computer, Azure Migrate: server assessment può individuare app, ruoli e funzionalità in esecuzione nei computer. L'individuazione dell'inventario delle app consente di identificare e pianificare un percorso di migrazione personalizzato per i carichi di lavoro locali. 

**Supporto** | **Dettagli**
--- | ---
**Individuazione** | L'individuazione è senza agente, usando le credenziali Guest del computer e accedendo in remoto ai computer usando le chiamate WMI e SSH.
**Computer supportati** | Macchine virtuali VMware locali.
**Sistema operativo del computer** | Tutte le versioni di Windows e Linux.
**credenziali vCenter** | Un account server vCenter con accesso in sola lettura e i privilegi abilitati per le macchine virtuali > operazioni Guest.
**Credenziali VM** | Attualmente supporta l'utilizzo di una credenziale per tutti i server Windows e una credenziale per tutti i server Linux.<br/><br/> Si crea un account utente Guest per macchine virtuali Windows e un account utente normale/normale (accesso non sudo) per tutte le macchine virtuali Linux.
**Strumenti VMware** | Gli strumenti VMware devono essere installati e in esecuzione nelle macchine virtuali che si desidera individuare.
**Accesso alla porta** | Negli host ESXi che eseguono macchine virtuali che si vuole individuare, l'appliance di Azure Migrate deve essere in grado di connettersi alla porta TCP 443.
**Limiti** | Per l'individuazione di app è possibile individuare fino a 10000 per ogni appliance. 

## <a name="vmware-requirements"></a>Requisiti di VMware

**VMware** | **Dettagli**
--- | ---
**server vCenter** | I computer che si desidera individuare e valutare devono essere gestiti da server vCenter versione 5,5, 6,0, 6,5 o 6,7.
**Autorizzazioni (valutazione)** | server vCenter account di sola lettura.
**Autorizzazioni (app-Discovery)** | server vCenter account con accesso in sola lettura e privilegi abilitati per **le macchine virtuali > operazioni Guest**.
**Autorizzazioni (visualizzazione dipendenze)** | Center Server account con accesso in sola lettura e privilegi abilitati per le **macchine virtuali** > **operazioni Guest**.


## <a name="azure-migrate-appliance-requirements"></a>Requisiti dell'appliance di Azure Migrate

Azure Migrate usa l' [appliance Azure migrate](migrate-appliance.md) per l'individuazione e la valutazione. L'appliance per VMware viene distribuita usando un modello OVA, importato in server vCenter. 

- Informazioni sui [requisiti degli appliance](migrate-appliance.md#appliance---vmware) per VMware.
- Informazioni sugli [URL](migrate-appliance.md#url-access) a cui l'appliance deve accedere.

## <a name="port-access"></a>Accesso alla porta

**Dispositivo** | **Connection**
--- | ---
Appliance | Connessioni in ingresso sulla porta TCP 3389 per consentire le connessioni Desktop remoto al dispositivo.<br/><br/> Connessioni in ingresso sulla porta 44368 per accedere in remoto all'app di gestione Appliance usando l'URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Connessioni in uscita sulla porta 443 (HTTPS), 5671 e 5672 (AMQP) per inviare i metadati di individuazione e prestazioni a Azure Migrate.
Server vCenter | Connessioni in ingresso sulla porta TCP 443 per consentire all'appliance di raccogliere i metadati di configurazione e prestazioni per le valutazioni. <br/><br/> Per impostazione predefinita, l'appliance si connette a vCenter sulla porta 443. Se il server vCenter è in ascolto su una porta diversa, è possibile modificare la porta quando si configura l'individuazione.
Host ESXi | **Obbligatorio solo per l' [individuazione delle applicazioni](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#application-discovery) e la visualizzazione delle [dipendenze senza agenti](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-dependency-visualization)** <br/><br/> L'appliance si connette agli host ESXi sulla porta TCP 443 per individuare le applicazioni ed eseguire la visualizzazione delle dipendenze senza agenti nelle macchine virtuali in esecuzione negli host.

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


## <a name="agentless-dependency-visualization"></a>Visualizzazione delle dipendenze senza agenti

Questa opzione è attualmente in fase di anteprima. [Altre informazioni](how-to-create-group-machine-dependencies-agentless.md). I requisiti sono riepilogati nella tabella seguente.

**Requisito** | **Dettagli**
--- | ---
**Distribuzione** | Prima di distribuire la visualizzazione delle dipendenze, è necessario disporre di un progetto Azure Migrate, con lo strumento Azure Migrate: server Assessment aggiunto al progetto. La visualizzazione delle dipendenze viene distribuita dopo aver configurato un'appliance Azure Migrate per individuare i computer locali.
**Supporto per VM** | Attualmente supportato solo per le macchine virtuali VMware.
**Macchine virtuali di Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 bit)
**Macchine virtuali di Linux** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14,04, 16,04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Account Windows** |  Per la visualizzazione è necessario un account di amministratore locale o di dominio.
**Account Linux** | Per la visualizzazione è necessario un account utente con privilegi radice.<br/><br/> In alternativa, l'account utente deve disporre di queste autorizzazioni per i file/bin/netstat e/bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.
**Agenti VM** | Non è necessario alcun agente nelle VM.
**Strumenti VMware** | Gli strumenti VMware devono essere installati e in esecuzione nelle macchine virtuali che si desidera analizzare.
**credenziali vCenter** | Un account server vCenter con accesso in sola lettura e i privilegi abilitati per le macchine virtuali > operazioni Guest.
**Accesso alla porta** | Negli host ESXi che eseguono macchine virtuali che si vuole analizzare, il Azure Migrate appliance deve essere in grado di connettersi alla porta TCP 443.



## <a name="next-steps"></a>Passaggi successivi

- [Esaminare](best-practices-assessment.md) le procedure consigliate per la creazione di valutazioni.
- [Prepararsi per VMware](tutorial-prepare-vmware.md) assessment.
