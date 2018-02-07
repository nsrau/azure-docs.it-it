---
title: Informazioni su Azure Site Recovery | Microsoft Docs
description: Panoramica del servizio Azure Site Recovery e riepilogo degli scenari di distribuzione.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: overview
ms.date: 01/07/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 4cc5d849630114577709f8373d08f0ce659e7e65
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2018
---
# <a name="about-site-recovery"></a>Informazioni su Site Recovery

Benvenuti nel servizio Azure Site Recovery. Questo articolo offre una rapida panoramica del servizio.

Le organizzazioni necessitano di una strategia di continuità aziendale e ripristino di emergenza (BCDR) in grado di garantire la sicurezza dei dati e mantenere operativi i carichi di lavoro e le app, quando si verificano interruzioni pianificate e non pianificate.

Servizi di ripristino di Azure contribuisce alla strategia BCDR:

- **Servizio Site Recovery**: Site Recovery aiuta a garantire la continuità aziendale, mantenendo carichi di lavoro e app aziendali in esecuzione in caso di interruzioni. Site Recovery replica i carichi di lavoro in esecuzione su macchine fisiche e virtuali (VM) da un sito primario in una località secondaria. Quando si verifica un'interruzione nel sito primario, viene eseguito il failover nella località secondaria, da dove è possibile accedere alle app. Quando la località primaria è di nuovo disponibile, è possibile eseguire il failback.  
- **Servizio Backup**: il servizio [Backup di Azure](https://docs.microsoft.com/azure/backup/) mantiene i dati al sicuro e ne consente il recupero grazie al backup in Azure.

Site Recovery può gestire la replica per:

- Replica di VM di Azure tra aree di Azure.
- Replica di VM locali e server fisici in Azure o in un sito secondario.


## <a name="what-does-site-recovery-provide"></a>Che cosa offre Site Recovery?


**Funzionalità** | **Dettagli**
--- | ---
**Soluzione BCDR semplice** | Con Site Recovery è possibile configurare e gestire la replica, il failover e il failback da un'unica posizione nel portale di Azure.
**Replica di VM di Azure** | È possibile configurare il ripristino di emergenza delle VM di Azure da un'area primaria a un'area secondaria.
**Replica di VM locali** | È possibile replicare VM locali e server fisici in Azure oppure in un data center locale secondario. La replica in Azure elimina i costi e la complessità associati alla gestione di un data center secondario.
**Replica di carichi di lavoro** | È possibile replicare qualsiasi carico di lavoro in esecuzione in VM di Azure, VM VMware e Hyper-V locali e server fisici Windows o Linux supportati.
**Resilienza dei dati** | Site Recovery gestisce la replica senza intercettare i dati delle applicazioni. Quando si esegue la replica in Azure, i dati vengono archiviati in Archiviazione di Azure che offre resilienza. In caso di failover, vengono create VM di Azure in base ai dati replicati.
**Obiettivo del tempo di ripristino e obiettivo del punto di ripristino** | È possibile mantenere gli obiettivi del tempo di ripristino (RTO, Recovery Time Objective) e gli obiettivi del punto di ripristino (RPO, Recovery Point Objective) entro i limiti dell'organizzazione. Site Recovery offre la replica continua per le VM di Azure e le VM WMware e una frequenza di replica di soli 30 secondi per Hyper-V. È possibile ridurre ulteriormente l'obiettivo del tempo di ripristino grazie all'integrazione con [Gestione traffico di Azure](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/).
**Mantenere la coerenza delle app nel failover** | È possibile eseguire la replica usando punti di recupero con snapshot coerenti con l'applicazione. Questi snapshot acquisiscono i dati dei dischi, tutti i dati in memoria e tutte le transazioni in corso.
**Test senza interruzioni** | È possibile eseguire facilmente simulazioni del ripristino di emergenza senza alcun impatto sulla replica in corso.
**Failover flessibili** | È possibile eseguire failover pianificati senza perdita di dati per interruzioni previste o il failover non pianificato con perdita di dati minima, in base alla frequenza di replica, per emergenze impreviste. È possibile eseguire facilmente il failback al sito primario quando risulterà nuovamente disponibile.
**Piani di ripristino personalizzati** | Usando i piani di ripristino, è possibile personalizzare e definire la sequenza di failover e ripristino di applicazioni multilivello in esecuzione in più VM. È possibile raggruppare le macchine in un piano di ripristino e, se lo si desidera, aggiungere facoltativamente azioni manuali e script. I piani di ripristino possono essere integrati con i runbook di Automazione di Azure.
**Integrazione di tecnologie BCDR** | Site Recovery si integra con altre tecnologie BCDR. È ad esempio possibile usare Site Recovery per proteggere il back-end SQL Server dei carichi di lavoro aziendali, con supporto nativo per SQL Server AlwaysOn, e gestire così il failover dei gruppi di disponibilità.
**Integrazione di Automazione di Azure** | Un'avanzata libreria di automazione di Azure offre script pronti per la produzione e specifici dell'applicazione che possono essere scaricati e integrati con Site Recovery.
**Integrazione di rete** | Site Recovery si integra con Azure per una gestione semplice della rete delle applicazioni, tra cui l'impostazione di indirizzi IP riservati, la configurazione di servizi di bilanciamento del carico e l'integrazione di Gestione traffico di Azure per cambi di rete efficienti.


## <a name="what-can-i-replicate"></a>Ciò che è possibile replicare?

**Supportato** | **Dettagli**
--- | ---
**Scenari di replica** | Replica di VM di Azure da un'area di Azure a un'altra.<br/><br/>  Replica di VM VMware locali, VM Hyper-V e server fisici (Windows e Linux) in Azure.<br/><br/> Replica di VM VMware, VM Hyper-V gestite da System Center VMM e server fisici in un sito secondario.
**Aree** | Vedere le [aree supportate](https://azure.microsoft.com/regions/services/) per Site Recovery. |
**Computer replicati** | Esaminare i requisiti di replica per [VM di Azure](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions), [VM locali](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) e [server fisici](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions).
**Server/host VMware** | Le VM VMware da replicare possono trovarsi in [host vSphere/server vCenter supportati](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
**Carichi di lavoro** | È possibile replicare qualsiasi carico di lavoro in esecuzione in un computer supportato per la replica. Inoltre, il team di Site Recovery ha eseguito test specifici dell'applicazione per un certo [numero di app](site-recovery-workload.md#workload-summary).



## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sul [supporto dei carichi di lavoro](site-recovery-workload.md).
* Introduzione alla [replica di VM di Azure tra aree](azure-to-azure-quickstart.md). 
