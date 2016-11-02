<properties 
   pageTitle="Architettura di Operations Management Suite (OMS) | Microsoft Azure"
   description="Microsoft Operations Management Suite (OMS) è la soluzione Microsoft per la gestione IT basata sul cloud che consente di gestire e proteggere l'infrastruttura locale e cloud.  Questo articolo identifica i diversi servizi di OMS e include collegamenti al contenuto dettagliato."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="bwren" />


# <a name="oms-architecture"></a>Architettura di OMS

[Operations Management Suite (OMS)](https://azure.microsoft.com/documentation/services/operations-management-suite/) è una raccolta di servizi basati sul cloud per la gestione di ambienti locali e cloud.  Questo articolo descrive i diversi componenti locali e cloud di OMS e la relativa architettura di cloud computing di alto livello.  Per altre informazioni, è possibile fare riferimento alla documentazione relativa a ogni servizio.

## <a name="log-analytics"></a>Log Analytics

Tutti i dati raccolti da [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) vengono archiviati nel repository OMS ospitato in Azure.  Le origini connesse generano i dati raccolti nel repository OMS.  Sono attualmente supportati tre tipi di origini connesse.

- Un agente installato in un computer [Windows](../log-analytics/log-analytics-windows-agents.md) o [Linux](../log-analytics/log-analytics-linux-agents.md) connesso direttamente a OMS.
- Un gruppo di gestione di System Center Operations Manager (SCOM) [connesso a Log Analytics](../log-analytics/log-analytics-om-agents.md) .  Gli agenti SCOM continuano a comunicare con i server di gestione che inoltrano a Log Analytics gli eventi e i dati relativi alle prestazioni.
- Un [account di archiviazione di Azure](../log-analytics/log-analytics-azure-storage.md) che raccoglie i dati di [Diagnostica di Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) da un ruolo di lavoro, da un ruolo Web o da una macchina virtuale di Azure.

Le origini dati definiscono i dati raccolti in Log Analytics dalle origini connesse, inclusi log eventi e contatori delle prestazioni.  Le soluzioni introducono altre funzionalità in OMS e possono essere aggiunte facilmente all'area di lavoro da [OMS Solutions Gallery](../log-analytics/log-analytics-add-solutions.md)(Raccolta soluzioni di OMS).  Alcune soluzioni possono richiedere una connessione diretta a Log Analytics dagli agenti SCOM, mentre altre possono richiedere l'installazione di un agente aggiuntivo.

Log Analytics ha un portale basato sul Web che consente di gestire risorse OMS, aggiungere e configurare soluzioni OMS, nonché visualizzare e analizzare dati nel repository OMS.

![Architettura di alto livello di Log Analytics](media/operations-management-suite-architecture/log-analytics.png)


## <a name="azure-automation"></a>Automazione di Azure

[runbook di automazione di Azure](http://azure.microsoft.com/documentation/services/automation) vengono eseguiti nel cloud di Azure e possono accedere alle risorse presenti in Azure o in altri servizi cloud o accessibili dalla rete Internet pubblica.  È anche possibile designare computer locali nel data center locale tramite [ruoli di lavoro ibridi per runbook](../automation/automation-hybrid-runbook-worker.md) per consentire ai runbook di accedere alle risorse locali.

[configurazioni DSC](../automation/automation-dsc-overview.md) archiviate in Automazione di Azure possono essere applicate direttamente alle macchine virtuali di Azure.  Altre macchine fisiche e virtuali possono richiedere configurazioni al server di pull DSC di Automazione di Azure.

Automazione di Azure offre una soluzione OMS che visualizza statistiche e collegamenti per avviare il portale di Azure per qualsiasi operazione.

![Architettura di alto livello di Automazione di Azure](media/operations-management-suite-architecture/automation.png)

## <a name="azure-backup"></a>Backup di Azure

I dati protetti in [Backup di Azure](http://azure.microsoft.com/documentation/services/backup) vengono archiviati in un insieme di credenziali di backup che si trova in una determinata area geografica.  I dati vengono replicati all'interno della stessa area geografica e, a seconda del tipo di insieme di credenziali, possono anche essere replicati in un'altra area per una maggiore ridondanza.

Backup di Azure prevede tre scenari fondamentali.

- Computer Windows con l'agente di Backup di Azure.  Questo scenario consente il backup di file e cartelle da qualsiasi client o server Windows direttamente nell'insieme di credenziali di backup di Azure.  
- System Center Data Protection Manager (DPM) o server di Backup di Microsoft Azure. scenario consente di sfruttare DPM o il server di Backup di Microsoft Azure per eseguire il backup di file e cartelle, oltre che dei carichi di lavoro di applicazioni come SQL e SharePoint, nell'archiviazione locale e quindi di eseguire la replica nell'insieme di credenziali di backup di Azure.
- Estensioni delle macchine virtuali di Azure.  Questo scenario consente di eseguire il backup di macchine virtuali di Azure nell'insieme di credenziali di backup di Azure.

Backup di Azure offre una soluzione OMS che visualizza statistiche e collegamenti per avviare il portale di Azure per qualsiasi operazione.

![Architettura di alto livello di Backup di Azure](media/operations-management-suite-architecture/backup.png)

## <a name="azure-site-recovery"></a>Azure Site Recovery

[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) coordina la replica, il failover e il failback di macchine virtuali e server fisici. I dati di replica vengono scambiati tra host Hyper-V, hypervisor VMware e server fisici nei data center primari e secondari o tra il data center e l'archiviazione di Azure.  Site Recovery archivia i metadati in insiemi di credenziali che si trovano in una determinata area geografica di Azure. Nessun dato replicato viene archiviato dal servizio Site Recovery.

Azure Site Recovery prevede tre scenari di replica fondamentali.

**Replica di macchine virtuali Hyper-V**
- Se le macchine virtuali Hyper-V vengono gestite nei cloud VMM, è possibile eseguire la replica in un data center secondario o nell'archiviazione di Azure.  La replica in Azure viene eseguita tramite una connessione Internet sicura.  La replica in un data center secondario viene eseguita tramite la LAN.
- Se le macchine virtuali Hyper-V non sono gestite tramite VMM, è possibile eseguire la replica solo nell'archiviazione di Azure.  La replica in Azure viene eseguita tramite una connessione Internet sicura.
 
**Replica di macchine virtuali VMware**
- È possibile replicare le macchine virtuali VMware in un data center secondario che esegue VMware o nell'archiviazione di Azure.  La replica in Azure può essere eseguita tramite una VPN da sito a sito, Azure ExpressRoute o una connessione Internet sicura. La replica in un data center secondario viene eseguita tramite il canale dati di InMage Scout.
 
**Replica di server fisici Windows e Linux** 
- È possibile replicare i server fisici in un data center secondario o nell'archiviazione di Azure. La replica in Azure può essere eseguita tramite una VPN da sito a sito, Azure ExpressRoute o una connessione Internet sicura. La replica in un data center secondario viene eseguita tramite il canale dati di InMage Scout.  Azure Site Recovery dispone di una soluzione OMS che visualizza alcune statistiche, ma è necessario usare il portale di Azure per qualsiasi operazione.

![Architettura di alto livello di Azure Site Recovery](media/operations-management-suite-architecture/site-recovery.png)


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics)
- Informazioni su [Automazione di Azure](https://azure.microsoft.com/documentation/services/automation)
- Informazioni su [Backup di Azure](http://azure.microsoft.com/documentation/services/backup)
- Informazioni su [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery)



<!--HONumber=Oct16_HO2-->


