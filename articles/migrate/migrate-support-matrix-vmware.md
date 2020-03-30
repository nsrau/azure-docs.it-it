---
title: VMware assessment support in Azure Migrate
description: Informazioni sul supporto per la valutazione delle macchine virtuali VMware con Azure Migrate Server Assessment.Learn about support for VMware VM assessment with Azure Migrate Server Assessment.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 03d07adb6f19346901286bdae148f95e68290e4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336867"
---
# <a name="support-matrix-for-vmware-assessment"></a>Matrice di supporto per la valutazione VMware 

Questo articolo riepiloga i prerequisiti e i requisiti di supporto per la valutazione delle macchine virtuali VMware in preparazione per la migrazione ad Azure.This article summarizes prerequisites and support requirements for assessing VMware VMs in preparation for migration to Azure. Se si desidera eseguire la migrazione delle macchine virtuali VMware in Azure, esaminare la matrice di supporto per la [migrazione.](migrate-support-matrix-vmware-migration.md)

Valutare i server fisici con lo strumento [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Creare un progetto di Azure Migrate e quindi aggiungere lo strumento al progetto. Dopo aver aggiunto lo strumento, si distribuisce [l'appliance di Azure Migrate.](migrate-appliance.md) L'appliance individua continuamente i computer locali e invia i metadati e i dati sulle prestazioni del computer ad Azure.The appliance continuously discovers on-premises machines, and sends machine metadata and performance data to Azure. Dopo l'individuazione del computer, si raccolgono i computer individuati in gruppi ed si esegue una valutazione per un gruppo.

## <a name="limitations"></a>Limitazioni

**Supporto** | **Dettagli**
--- | ---
**Limiti del progetto** | È possibile creare più progetti in una sottoscrizione di Azure.You can create multiple projects in an Azure subscription.<br/><br/> È possibile individuare e valutare fino a 35.000 macchine virtuali VMware in un singolo [progetto.](migrate-support-matrix.md#azure-migrate-projects) Un progetto può includere macchine virtuali VMware, server fisici e macchine virtuali Hyper-V, fino ai limiti di valutazione per ognuno di essi.
**Scoperta** | L'appliance Azure Migrate può individuare fino a 10.000 macchine virtuali VMware in un server vCenter.
**Valutazione** | È possibile aggiungere fino a 35.000 macchine in un singolo gruppo.<br/><br/> È possibile valutare fino a 35.000 macchine virtuali in un'unica valutazione.

[Ulteriori informazioni](concepts-assessment-calculation.md) sulle valutazioni.


## <a name="application-discovery"></a>Individuazione delle applicazioni

Oltre a individuare i computer, Azure Migrate: Server Assessment può individuare app, ruoli e funzionalità in esecuzione nei computer. L'individuazione dell'inventario delle app consente di identificare e pianificare un percorso di migrazione personalizzato per i carichi di lavoro locali. 

**Supporto** | **Dettagli**
--- | ---
**Macchine supportate** | L'individuazione delle app è attualmente supportata solo per le macchine virtuali VMware locali.
**Scoperta** | L'individuazione delle app è senza agente. Utilizza le credenziali guest del computer e accede in remoto ai computer utilizzando le chiamate WMI e SSH.
**Supporto VM** | L'individuazione delle app è supportata per tutte le versioni di Windows e Linux.App-discovery is supported for all Windows and Linux versions.
**Credenziali vCenter** | L'individuazione delle app richiede un account vCenter Server con accesso in sola lettura e privilegi abilitati per le macchine virtuali > le operazioni Guest.
**Credenziali della macchina virtualeVM credentials** | L'individuazione delle app supporta attualmente l'uso di una credenziale per tutti i server Windows e di una credenziale per tutti i server Linux.App discovery currently supports the use of one credential for all Windows servers, and one credential for all Linux servers.<br/><br/> Creare un account utente guest per le macchine virtuali Windows e un account utente normale/normale (accesso non sudo) per tutte le macchine virtuali Linux.You create a guest user account for Windows VMs, and a regular/normal user account (non-sudo access) for all Linux VMs.
**Strumenti VMware** | Gli strumenti VMware devono essere installati e in esecuzione nelle macchine virtuali che si desidera individuare. <br/> La versione degli strumenti VMware deve essere successiva alla 10.2.0.
**Powershell** | Per le macchine virtuali deve essere installata la versione 2.0 o successiva.
**Accesso alla porta** | Negli host ESXi che eseguono macchine virtuali che si desidera individuare, l'appliance di Azure Migrate deve essere in grado di connettersi alla porta TCP 443.
**Limiti** | Per l'individuazione delle app, è possibile individuare fino a 10000 macchine virtuali in ogni appliance di Azure Migrate.For app-discovery, you can discover up to 10000 VMs on each Azure Migrate appliance.



## <a name="vmware-requirements"></a>Requisiti di VMware

**Vmware** | **Dettagli**
--- | ---
**VM VMware** | La valutazione è supportata per tutti i sistemi operativi Windows e Linux.
**VCenter Server** | Le macchine che si desidera individuazione e valutazione devono essere gestite da vCenter Server versione 5.5, 6.0, 6.5 o 6.7.
**Autorizzazioni (valutazione)** | Account di sola lettura del server vCenter.
**Autorizzazioni (individuazione app)** | Account di vCenter Server con accesso in sola lettura e privilegi abilitati per **le macchine virtuali > Guest Operations**.
**Autorizzazioni (visualizzazione delle dipendenze)** | Account center Server con accesso in sola lettura e privilegi abilitati per**Le operazioni guest delle**macchine >  **virtuali.**


## <a name="azure-migrate-appliance-requirements"></a>Requisiti dell'appliance di Azure Migrate

Azure Migrate usa [l'appliance di Azure Migrate](migrate-appliance.md) per l'individuazione e la valutazione. L'appliance per VMware viene distribuita utilizzando un modello OVA, importato in vCenter Server. 

- Informazioni sui [requisiti dell'appliance](migrate-appliance.md#appliance---vmware) per VMware.
- Informazioni sugli [URL](migrate-appliance.md#url-access) a cui l'appliance deve accedere.

## <a name="port-access"></a>Accesso alla porta

**Dispositivo** | **Connessione**
--- | ---
Elettrodomestici | Connessioni in ingresso sulla porta TCP 3389 per consentire le connessioni desktop remoto all'appliance.<br/><br/> Connessioni in ingresso sulla porta 44368 per accedere in remoto all'app di gestione dell'appliance utilizzando l'URL:```https://<appliance-ip-or-name>:44368``` <br/><br/>Connessioni in uscita sulla porta 443 (HTTPS), per inviare metadati di individuazione e prestazioni ad Azure Migrate.Outbound connections on port 443 (HTTPS), to send discovery and performance metadata to Azure Migrate.
Server vCenter | Connessioni in ingresso sulla porta TCP 443 per consentire all'appliance di raccogliere i metadati di configurazione e prestazioni per le valutazioni. <br/><br/> L'appliance si connette a vCenter sulla porta 443 per impostazione predefinita. Se il server vCenter è in ascolto su una porta diversa, è possibile modificare la porta quando si configura l'individuazione.
Host ESXi (individuazione app/analisi delle dipendenze senza agente) | Se si desidera eseguire [l'individuazione](how-to-discover-applications.md) delle app o [l'analisi delle dipendenze senza agente,](concepts-dependency-visualization.md#agentless-analysis)l'appliance si connette agli host ESXi sulla porta TCP 443, per individuare le applicazioni, ed eseguire la visualizzazione delle dipendenze senza agenti nelle macchine virtuali.

## <a name="agentless-dependency-analysis-requirements"></a>Requisiti di analisi delle dipendenze senza agente

[L'analisi delle dipendenze](concepts-dependency-visualization.md) consente di identificare le dipendenze tra i computer locali che si desidera valutare ed eseguire la migrazione in Azure.Dependency analysis helps you to identify dependencies between on-premises machines that you want to assess and migrate to Azure. Nella tabella sono riepilogati i requisiti per l'impostazione dell'analisi delle dipendenze senza agente. 

**Requisito** | **Dettagli**
--- | --- 
**Prima della distribuzione** | È necessario disporre di un progetto di Azure Migrate, con lo strumento Azure Migrate: Server Assessment aggiunto al progetto.<br/><br/>  La visualizzazione delle dipendenze viene distribuita dopo aver impostato un'appliance Di azure Migrate per individuare i computer VMWare locali.<br/><br/> [Scopri come](create-manage-projects.md) creare un progetto per la prima volta.<br/> [Informazioni su come](how-to-assess.md) aggiungere uno strumento di valutazione a un progetto esistente.<br/> [Informazioni su come](how-to-set-up-appliance-vmware.md) configurare l'appliance Azure Migrate per la valutazione delle macchine virtuali VMware.Learn how to set up the Azure Migrate appliance for assessment of VMware VMs.
**Supporto VM** | Attualmente supportato solo per le macchine virtuali VMware.
**Macchine virtuali di Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 bit).
**Account di Windows** |  Per l'analisi delle dipendenze, l'appliance di Azure Migrate richiede un account di amministratore locale o di dominio per accedere alle macchine virtuali Windows.For dependency analysis, the Azure Migrate appliance needs a local or a domain Administrator account to access Windows VMs.
**Macchine virtuali di Linux** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14.04, 16.04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Account Linux** | Per l'analisi delle dipendenze, nei computer Linux l'appliance di Azure Migrate richiede un account utente con privilegi root.<br/><br/> In alternativa, l'account utente necessita di queste autorizzazioni per i file /bin/netstat e /bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.
**Agenti richiesti** | Nessun agente richiesto nei computer che si desidera analizzare.
**Strumenti VMware** | VMware Tools (successivo alla 10.2) deve essere installato e in esecuzione in ogni macchina virtuale che si vuole analizzare.
**Credenziali del server vCenter** | La visualizzazione delle dipendenze richiede un account vCenter Server con accesso in sola lettura e privilegi abilitati per le macchine virtuali > le operazioni Guest. 
**Powershell** | Per le macchine virtuali deve essere installata la versione 2.0 o successiva delle macchine virtuali.
**Accesso alla porta** | Negli host ESXi che eseguono macchine virtuali che si desidera analizzare, l'appliance di Azure Migrate deve essere in grado di connettersi alla porta TCP 443.

## <a name="agent-based-dependency-analysis-requirements"></a>Requisiti di analisi delle dipendenze basati su agente

[L'analisi delle dipendenze](concepts-dependency-visualization.md) consente di identificare le dipendenze tra i computer locali che si desidera valutare ed eseguire la migrazione in Azure.Dependency analysis helps you to identify dependencies between on-premises machines that you want to assess and migrate to Azure. Nella tabella sono riepilogati i requisiti per l'impostazione dell'analisi delle dipendenze basata su agente. 

**Requisito** | **Dettagli** 
--- | --- 
**Prima della distribuzione** | È necessario disporre di un progetto di Azure Migrate, con lo strumento Azure Migrate: Server Assessment aggiunto al progetto.<br/><br/>  Distribuire la visualizzazione delle dipendenze dopo aver impostato un'appliance di Azure Migrate per individuare i computer locali<br/><br/> [Scopri come](create-manage-projects.md) creare un progetto per la prima volta.<br/> [Informazioni su come](how-to-assess.md) aggiungere uno strumento di valutazione a un progetto esistente.<br/> Informazioni su come configurare l'appliance Azure Migrate per la valutazione di [Hyper-V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)o server fisici.
**Azure Government** | La visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.
**Analisi dei log** | Azure Migrate usa la soluzione [Mappa dei servizi](../operations-management-suite/operations-management-suite-service-map.md) nei log di Monitoraggio di Azure per [la](../log-analytics/log-analytics-overview.md) visualizzazione delle dipendenze.<br/><br/> Associare un'area di lavoro di Log Analytics nuova o esistente a un progetto di Azure Migrate.You associate a new or existing Log Analytics workspace with an Azure Migrate project. L'area di lavoro per un progetto di Azure Migrate non può essere modificata dopo l'aggiunta. <br/><br/> L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto Azure Migrate.The workspace must be in the same subscription as the Azure Migrate project.<br/><br/> L'area di lavoro deve risiedere nelle aree degli Stati Uniti orientali, del sud-est asiatico o dell'Europa occidentale. Le aree di lavoro in altre aree non possono essere associate a un progetto.<br/><br/> L'area di lavoro deve trovarsi in un'area in cui la mappa del servizio [è supportata.](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)<br/><br/> In Log Analytics, l'area di lavoro associata ad Azure Migrate è contrassegnata con la chiave del progetto di migrazione e il nome del progetto.
**Agenti richiesti** | In ogni computer che si desidera analizzare, installare i seguenti agenti:<br/><br/> [L'agente di monitoraggio Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [L'agente di dipendenza](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Se i computer locali non sono connessi a Internet, è necessario scaricare e installare il gateway di Log Analytics su di essi.<br/><br/> Ulteriori informazioni sull'installazione [dell'agente di dipendenza](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e di [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Area di lavoro di Log Analytics** | L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto Azure Migrate.The workspace must be in the same subscription as the Azure Migrate project.<br/><br/> Azure Migrate supporta le aree di lavoro che risiedano nelle aree degli Stati Uniti orientali, del sud-est asiatico e dell'Europa occidentale.<br/><br/>  L'area di lavoro deve trovarsi in un'area in cui la mappa del servizio [è supportata.](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)<br/><br/> L'area di lavoro per un progetto di Azure Migrate non può essere modificata dopo l'aggiunta.
**Costi** | La soluzione Mappa di servizio non comporta alcun addebito per i primi 180 giorni (dal giorno in cui si associa l'area di lavoro Log Analytics al progetto Di mitele di Azure)/<br/><br/> Dopo 180 giorni verranno applicati gli addebiti standard di Log Analytics.<br/><br/> L'utilizzo di qualsiasi soluzione diversa da Mappa del servizio nell'area di lavoro di Log Analytics associata comporterà costi standard per Log Analytics.Using any solution other than Service Map in the associated Log Analytics workspace will incur [standard charges](https://azure.microsoft.com/pricing/details/log-analytics/) for Log Analytics.<br/><br/> All'eliminazione del progetto di Azure Migrate, l'area di lavoro non viene eliminata con il progetto. Dopo l'eliminazione del progetto, l'utilizzo della mappa del servizio non è gratuito e a ogni nodo verrà addebitato il livello a pagamento dell'area di lavoro di Log Analytics/<br/><br/>Se si hanno progetti creati prima della disponibilità generale di Azure Migrate (GA- 28 febbraio 2018), è possibile che siano stati sostenuti costi aggiuntivi per la mappa del servizio. Per garantire il pagamento dopo soli 180 giorni, ti consigliamo di creare un nuovo progetto, poiché le aree di lavoro esistenti prima di GA sono ancora addebitabili.
**dispositivi mobili** | Quando si registrano gli agenti nell'area di lavoro, si usano l'ID e la chiave forniti dal progetto Azure Migrate.When you register agents to the workspace, you use the ID and key provided by the Azure Migrate project.<br/><br/> È possibile usare l'area di lavoro Log Analytics all'esterno di Azure Migrate.<br/><br/> Se si elimina il progetto Azure Migrate associato, l'area di lavoro non viene eliminata automaticamente. [Eliminarlo manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Non eliminare l'area di lavoro creata da Azure Migrate, a meno che non si elimini il progetto Azure Migrate.Don't delete the workspace created by Azure Migrate, unless you delete the Azure Migrate project. In caso contrario, la funzionalità di visualizzazione delle dipendenze non funzionerà come previsto.
**Connettività Internet** | Se i computer non sono connessi a Internet, è necessario installare il gateway di Log Analytics su di essi.


## <a name="next-steps"></a>Passaggi successivi

- [Esaminare le](best-practices-assessment.md) procedure consigliate per la creazione di valutazioni.
- [Prepararsi per la valutazione VMware.](tutorial-prepare-vmware.md)
