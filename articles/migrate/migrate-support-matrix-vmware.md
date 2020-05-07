---
title: Supporto per la valutazione VMware in Azure Migrate
description: Informazioni sul supporto per la valutazione delle VM VMware con Azure Migrate server assessment.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: d378ece1eda906a30ec33e2cf27ad59df473b5c7
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779910"
---
# <a name="support-matrix-for-vmware-assessment"></a>Matrice di supporto per VMware Assessment 

Questo articolo riepiloga i prerequisiti e i requisiti di supporto quando si valutano le macchine virtuali VMware per la migrazione ad Azure, usando lo strumento [Azure migrate: server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Per eseguire la migrazione di macchine virtuali VMware in Azure, esaminare la [matrice di supporto](migrate-support-matrix-vmware-migration.md)per la migrazione.

Per valutare le macchine virtuali VMware, è necessario creare un progetto Azure Migrate e quindi aggiungere lo strumento Server Assessment al progetto. Una volta aggiunto lo strumento, si distribuisce il [dispositivo Azure migrate](migrate-appliance.md). L'appliance individua continuamente i computer locali e invia i metadati del computer e i dati sulle prestazioni in Azure. Al termine dell'individuazione, è possibile raccogliere i computer individuati in gruppi ed eseguire una valutazione per un gruppo.

## <a name="limitations"></a>Limitazioni

**Supporto tecnico** | **Dettagli**
--- | ---
**Limiti del progetto** | È possibile creare più progetti in una sottoscrizione di Azure.<br/><br/> È possibile individuare e valutare fino a 35.000 macchine virtuali VMware in un singolo [progetto](migrate-support-matrix.md#azure-migrate-projects). Un progetto può includere anche i server fisici e le macchine virtuali Hyper-V, fino ai limiti di valutazione per ciascuno di essi.
**Individuazione** | L'appliance Azure Migrate è in grado di rilevare fino a 10.000 VM VMware in una server vCenter.
**Valutazione** | È possibile aggiungere fino a 35.000 computer in un singolo gruppo.<br/><br/> È possibile valutare fino a 35.000 VM in un'unica valutazione.

[Altre](concepts-assessment-calculation.md) informazioni sulle valutazioni.


## <a name="application-discovery"></a>Individuazione delle applicazioni

Oltre a individuare i computer, server assessment può individuare app, ruoli e funzionalità in esecuzione nei computer. L'individuazione dell'inventario delle app consente di identificare e pianificare un percorso di migrazione personalizzato per i carichi di lavoro locali. 

**Supporto tecnico** | **Dettagli**
--- | ---
**Computer supportati** | L'individuazione delle app è attualmente supportata solo per le macchine virtuali VMware.
**Individuazione** | L'individuazione delle app è senza agente. Usa le credenziali Guest del computer e accede in remoto ai computer usando WMI e le chiamate SSH.
**Supporto per VM** | App-Discovery è supportato per tutte le versioni di Windows e Linux.
**credenziali vCenter** | Per l'individuazione delle app è necessario un account server vCenter con accesso in sola lettura e i privilegi abilitati per le macchine virtuali > le operazioni Guest.
**Credenziali VM** | App Discovery supporta attualmente l'uso di una credenziale per tutti i server Windows e una credenziale per tutti i server Linux.<br/><br/> Si crea un account utente Guest per macchine virtuali Windows e un account utente normale/normale (accesso non sudo) per tutte le macchine virtuali Linux.
**Strumenti VMware** | Gli strumenti VMware devono essere installati e in esecuzione nelle macchine virtuali che si desidera individuare. <br/> La versione degli strumenti VMware deve essere successiva a 10.2.0.
**PowerShell** | Per le macchine virtuali deve essere installata la versione 2,0 o successiva di PowerShell.
**Accesso alla porta** | Negli host ESXi che eseguono macchine virtuali che si vuole individuare, l'appliance di Azure Migrate deve essere in grado di connettersi alla porta TCP 443.
**Limiti** | Per l'individuazione delle app è possibile individuare fino a 10000 VM in ogni appliance Azure Migrate.



## <a name="vmware-requirements"></a>Requisiti di VMware

**VMware** | **Dettagli**
--- | ---
**VM VMware** | La valutazione è supportata per tutti i sistemi operativi Windows e Linux.
**server vCenter** | I computer che si desidera individuare e valutare devono essere gestiti da server vCenter versione 5,5, 6,0, 6,5 o 6,7.
**Autorizzazioni (valutazione)** | server vCenter account di sola lettura.
**Autorizzazioni (app-Discovery)** | server vCenter account con accesso in sola lettura e privilegi abilitati per **le macchine virtuali > operazioni Guest**.
**Autorizzazioni (visualizzazione dipendenze)** | Account del server Center con accesso in sola lettura e privilegi abilitati per > **le operazioni Guest**delle **macchine virtuali**.


## <a name="azure-migrate-appliance-requirements"></a>Requisiti dell'appliance di Azure Migrate

Azure Migrate usa l' [appliance Azure migrate](migrate-appliance.md) per l'individuazione e la valutazione. È possibile distribuire l'appliance come macchina virtuale VMWare usando un modello OVA, importato in server vCenter o usando uno [script di PowerShell](deploy-appliance-script.md).

- Informazioni sui [requisiti degli appliance](migrate-appliance.md#appliance---vmware) per VMware.
- Informazioni sugli URL necessari all'appliance per accedere ai cloud [pubblici](migrate-appliance.md#public-cloud-urls) e [governativi](migrate-appliance.md#government-cloud-urls) .
- In Azure per enti pubblici è necessario distribuire l'appliance [con lo script](deploy-appliance-script-government.md).


## <a name="port-access"></a>Accesso alla porta

**Dispositivo** | **Connessione**
--- | ---
Elettrodomestici | Connessioni in ingresso sulla porta TCP 3389 per consentire le connessioni Desktop remoto al dispositivo.<br/><br/> Connessioni in ingresso sulla porta 44368 per accedere in remoto all'app di gestione Appliance usando l'URL:```https://<appliance-ip-or-name>:44368``` <br/><br/>Connessioni in uscita sulla porta 443 (HTTPS) per inviare i metadati di individuazione e prestazioni a Azure Migrate.
Server vCenter | Connessioni in ingresso sulla porta TCP 443 per consentire all'appliance di raccogliere i metadati di configurazione e prestazioni per le valutazioni. <br/><br/> Per impostazione predefinita, l'appliance si connette a vCenter sulla porta 443. Se il server vCenter è in ascolto su una porta diversa, è possibile modificare la porta quando si configura l'individuazione.
Host ESXi (individuazione app/analisi dipendenza senza agenti) | Se si vuole eseguire l' [individuazione delle app](how-to-discover-applications.md) o l' [analisi delle dipendenze senza agenti](concepts-dependency-visualization.md#agentless-analysis), l'appliance si connette agli host ESXi sulla porta TCP 443, per individuare le applicazioni, per eseguire la visualizzazione delle dipendenze senza agenti nelle macchine virtuali.

## <a name="application-discovery"></a>Individuazione delle applicazioni

Oltre a individuare i computer, server assessment può individuare app, ruoli e funzionalità in esecuzione nei computer. L'individuazione dell'inventario delle app consente di identificare e pianificare un percorso di migrazione personalizzato per i carichi di lavoro locali. 

**Supporto tecnico** | **Dettagli**
--- | ---
**Computer supportati** | L'individuazione delle app è attualmente supportata solo per le macchine virtuali VMware.
**Individuazione** | L'individuazione delle app è senza agente. Usa le credenziali Guest del computer e accede in remoto ai computer usando WMI e le chiamate SSH.
**Supporto per VM** | App-Discovery è supportato per tutte le versioni di Windows e Linux.
**credenziali vCenter** | Per l'individuazione delle app è necessario un account server vCenter con accesso in sola lettura e i privilegi abilitati per le macchine virtuali > le operazioni Guest.
**Credenziali VM** | App Discovery supporta attualmente l'uso di una credenziale per tutti i server Windows e una credenziale per tutti i server Linux.<br/><br/> Si crea un account utente Guest per macchine virtuali Windows e un account utente normale/normale (accesso non sudo) per tutte le macchine virtuali Linux.
**Strumenti VMware** | Gli strumenti VMware devono essere installati e in esecuzione nelle macchine virtuali che si desidera individuare. <br/> La versione degli strumenti VMware deve essere successiva a 10.2.0.
**PowerShell** | Per le macchine virtuali deve essere installata la versione 2,0 o successiva di PowerShell.
**Accesso alla porta** | Negli host ESXi che eseguono macchine virtuali che si vuole individuare, l'appliance di Azure Migrate deve essere in grado di connettersi alla porta TCP 443.
**Limiti** | Per l'individuazione delle app è possibile individuare fino a 10000 VM in ogni appliance Azure Migrate.


## <a name="agentless-dependency-analysis-requirements"></a>Requisiti per l'analisi delle dipendenze senza agenti

L' [analisi](concepts-dependency-visualization.md) delle dipendenze consente di identificare le dipendenze tra computer locali che si vuole valutare e migrare in Azure. Nella tabella sono riepilogati i requisiti per la configurazione dell'analisi delle dipendenze senza agenti. 

**Requisito** | **Dettagli**
--- | --- 
**Prima della distribuzione** | È necessario disporre di un progetto Azure Migrate, con lo strumento Server Assessment aggiunto al progetto.<br/><br/>  La visualizzazione delle dipendenze viene distribuita dopo aver configurato un'appliance Azure Migrate per individuare i computer VMWare locali.<br/><br/> [Informazioni su come](create-manage-projects.md) creare un progetto per la prima volta.<br/> [Informazioni su come](how-to-assess.md) aggiungere uno strumento di valutazione a un progetto esistente.<br/> [Informazioni su come](how-to-set-up-appliance-vmware.md) configurare l'appliance Azure migrate per la valutazione delle macchine virtuali VMware.
**Supporto per VM** | Attualmente supportato solo per le macchine virtuali VMware.
**Macchine virtuali di Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 bit).
**Account di Windows** |  Per l'analisi delle dipendenze, l'appliance Azure Migrate necessita di un account amministratore di dominio o di un account amministratore locale per accedere alle macchine virtuali Windows.
**Macchine virtuali di Linux** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14,04, 16,04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Account Linux** | Per l'analisi delle dipendenze, nei computer Linux il dispositivo Azure Migrate necessita di un account utente con privilegi radice.<br/><br/> In alternativa, l'account utente deve disporre di queste autorizzazioni per i file/bin/netstat e/bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.
**Agenti obbligatori** | Non sono necessari agenti nei computer che si vuole analizzare.
**Strumenti VMware** | Gli strumenti VMware (successivi a 10,2) devono essere installati e in esecuzione in ogni macchina virtuale che si vuole analizzare.
**credenziali server vCenter** | La visualizzazione delle dipendenze necessita di un account server vCenter con accesso in sola lettura e dei privilegi abilitati per le macchine virtuali > le operazioni Guest. 
**PowerShell** | Per le macchine virtuali deve essere installata la versione 2,0 o successiva di PowerShell.
**Accesso alla porta** | Negli host ESXi che eseguono macchine virtuali che si vuole analizzare, il Azure Migrate appliance deve essere in grado di connettersi alla porta TCP 443.


## <a name="agent-based-dependency-analysis-requirements"></a>Requisiti di analisi delle dipendenze basate su agenti

L' [analisi](concepts-dependency-visualization.md) delle dipendenze consente di identificare le dipendenze tra computer locali che si vuole valutare e migrare in Azure. Nella tabella sono riepilogati i requisiti per la configurazione dell'analisi delle dipendenze basata su agente. 

**Requisito** | **Dettagli** 
--- | --- 
**Prima della distribuzione** | È necessario disporre di un progetto Azure Migrate con lo strumento Azure Migrate: server Assessment aggiunto al progetto.<br/><br/>  La visualizzazione delle dipendenze viene distribuita dopo la configurazione di un appliance Azure Migrate per individuare i computer locali<br/><br/> [Informazioni su come](create-manage-projects.md) creare un progetto per la prima volta.<br/> [Informazioni su come](how-to-assess.md) aggiungere uno strumento di valutazione a un progetto esistente.<br/> Informazioni su come configurare il dispositivo Azure Migrate per la valutazione dei server [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)o fisici.
**Azure Government** | La visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.
**Log Analytics** | Azure Migrate usa la soluzione [mapping dei servizi](../operations-management-suite/operations-management-suite-service-map.md) nei [log di monitoraggio di Azure](../log-analytics/log-analytics-overview.md) per la visualizzazione delle dipendenze.<br/><br/> Si associa un'area di lavoro Log Analytics nuova o esistente a un progetto Azure Migrate. Non è possibile modificare l'area di lavoro per un progetto di Azure Migrate dopo che è stata aggiunta. <br/><br/> L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto Azure Migrate.<br/><br/> L'area di lavoro deve trovarsi nelle aree Stati Uniti orientali, Asia sudorientale o Europa occidentale. Le aree di lavoro in altre aree non possono essere associate a un progetto.<br/><br/> L'area di lavoro deve trovarsi in un'area in cui [mapping dei servizi è supportato](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> In Log Analytics, l'area di lavoro associata a Azure Migrate è contrassegnata con la chiave del progetto di migrazione e il nome del progetto.
**Agenti obbligatori** | In ogni computer che si desidera analizzare installare gli agenti seguenti:<br/><br/> [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Se i computer locali non sono connessi a Internet, è necessario scaricare e installare Log Analytics gateway.<br/><br/> Altre informazioni sull'installazione di [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Area di lavoro Log Analytics** | L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto Azure Migrate.<br/><br/> Azure Migrate supporta le aree di lavoro che risiedono nelle aree Stati Uniti orientali, Asia sudorientale ed Europa occidentale.<br/><br/>  L'area di lavoro deve trovarsi in un'area in cui [mapping dei servizi è supportato](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Non è possibile modificare l'area di lavoro per un progetto di Azure Migrate dopo che è stata aggiunta.
**I costi** | La soluzione Mapping dei servizi non comporta alcun addebito per i primi 180 giorni (dal giorno in cui si associa l'area di lavoro Log Analytics al progetto Azure Migrate)/<br/><br/> Dopo 180 giorni verranno applicati gli addebiti standard di Log Analytics.<br/><br/> L'uso di qualsiasi soluzione diversa da Mapping dei servizi nell'area di lavoro Log Analytics associata genererà [addebiti standard](https://azure.microsoft.com/pricing/details/log-analytics/) per log Analytics.<br/><br/> All'eliminazione del progetto di Azure Migrate, l'area di lavoro non viene eliminata con il progetto. Dopo l'eliminazione del progetto, Mapping dei servizi utilizzo non è gratuito e ogni nodo verrà addebitato in base al livello a pagamento di Log Analytics area di lavoro/<br/><br/>Se sono presenti progetti creati prima di Azure Migrate disponibilità generale (GA-28 febbraio 2018), è possibile che vengano addebitati costi aggiuntivi per l'Mapping dei servizi. Per garantire il pagamento solo dopo 180 giorni, è consigliabile creare un nuovo progetto, perché le aree di lavoro esistenti prima di GA sono ancora addebitabili.
**Gestione** | Quando si registrano gli agenti nell'area di lavoro, è possibile utilizzare l'ID e la chiave forniti dal progetto Azure Migrate.<br/><br/> È possibile usare l'area di lavoro Log Analytics all'esterno di Azure Migrate.<br/><br/> Se si elimina il progetto Azure Migrate associato, l'area di lavoro non viene eliminata automaticamente. [Eliminarlo manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Non eliminare l'area di lavoro creata da Azure Migrate, a meno che non si elimini il progetto Azure Migrate. In caso contrario, la funzionalità di visualizzazione delle dipendenze non funzionerà come previsto.
**Connettività Internet** | Se i computer non sono connessi a Internet, è necessario installare il gateway Log Analytics.
**Azure Government** | L'analisi delle dipendenze basata su agente non è supportata.


## <a name="next-steps"></a>Passaggi successivi

- [Esaminare](best-practices-assessment.md) le procedure consigliate per la creazione di valutazioni.
- [Prepararsi per VMware](tutorial-prepare-vmware.md) assessment.
