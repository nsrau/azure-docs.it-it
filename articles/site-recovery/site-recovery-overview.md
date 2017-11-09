---
title: Informazioni su Azure Site Recovery | Microsoft Docs
description: Panoramica del servizio Azure Site Recovery e riepilogo degli scenari di distribuzione.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: e9b97b00-0c92-4970-ae92-5166a4d43b68
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/01/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b4b39cd23557093edaec97f7ef7a3e354f1ecd03
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2017
---
# <a name="about-site-recovery"></a>Informazioni su Site Recovery

Benvenuti nel servizio Azure Site Recovery. Questo articolo offre una rapida panoramica del servizio.

## <a name="business-continuity-and-disaster-recovery-bcdr-with-azure-recovery-services"></a>Continuità aziendale e ripristino di emergenza (BCDR) con Servizi di ripristino di Azure

Un'organizzazione deve sapere come si intende proteggere i dati e preservare l'esecuzione di applicazioni e carichi di lavoro quando si verificano interruzioni pianificate e impreviste.

Servizi di ripristino di Azure contribuisce alla strategia BCDR:

- **Servizio Site Recovery**: Site Recovery consente di garantire la continuità aziendale, mantenendo le app in esecuzione nelle macchine virtuali e nei server fisici disponibili in caso di arresto di un sito. Site Recovery replica i carichi di lavoro in esecuzione in macchine virtuali e server fisici in modo che rimangano disponibili in una posizione secondaria se il sito primario non è raggiungibile. Ripristina i carichi di lavoro nel sito primario quando è di nuovo attivo.
- **Backup di Azure**: il servizio [Backup di Azure](https://docs.microsoft.com/azure/backup/) mantiene i dati al sicuro e recuperabili eseguendone il backup in Azure.

Site Recovery può gestire la replica per:

- Replica di VM di Azure tra aree di Azure.
- Replica di macchine virtuali locali o server fisici in Azure o in un sito secondario.


## <a name="what-does-site-recovery-provide"></a>Che cosa offre Site Recovery?

**Funzionalità** | **Dettagli**
--- | ---
**Distribuire una soluzione BCDR semplice** | Con Site Recovery è possibile configurare e gestire la replica, il failover e il failback da un'unica posizione nel portale di Azure.
**Replicare le VM di Azure** | È possibile configurare la strategia di BCDR in modo che le VM di Azure vengano replicate tra le aree di Azure.
**Replicare le VM locali in una posizione esterna** | È possibile replicare VM locali e server fisici in Azure e in una posizione locale secondaria. La replica in Azure elimina i costi e la complessità associati alla gestione di un data center secondario.
**Replica di qualsiasi carico di lavoro** | Replicare qualsiasi carico di lavoro in esecuzione in VM di Azure, VM Hyper-V locali, VM VMware supportate e nei server fisici di Windows o Linux.
**Mantenere i dati resilienti e sicuri** | Site Recovery gestisce la replica senza intercettare i dati delle applicazioni. I dati replicati vengono archiviati in Archiviazione di Azure grazie alla resilienza offerta. Le VM di Azure vengono create con i dati replicati in caso di failover.
**Soddisfare RTO e RPO** | Mantenere gli obiettivi del punto di ripristino (RPO, Recovery Point Objective) e gli obiettivi del tempo di ripristino (RTO, Recovery Time Objective) entro i limiti dell'organizzazione. Site Recovery offre la replica continua per le VM di Azure e le VM WMware e una frequenza di replica di soli 30 secondi per Hyper-V. È possibile ridurre ulteriormente gli obiettivi del tempo di ripristino (RTO) grazie all'integrazione con [Gestione traffico di Azure](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/).
**Mantenere la coerenza delle app nel failover** | È possibile configurare punti di ripristino con snapshot coerenti con l'applicazione. Gli snapshot coerenti con l'applicazione acquisiscono i dati dei dischi, tutti i dati in memoria e tutte le transazioni in corso.
**Test senza interruzioni** | È possibile eseguire facilmente failover di test per supportare analisi del ripristino di emergenza senza alcun impatto sulla replica in corso.
**Eseguire failover flessibili** | È possibile eseguire failover pianificati senza perdita di dati per interruzioni previste o il failover non pianificato con perdita di dati minima, in base alla frequenza di replica, per emergenze impreviste. È possibile eseguire facilmente il failback al sito primario quando risulterà nuovamente disponibile.
**Creare piani di ripristino** | Con i piani di ripristino è possibile personalizzare e definire la sequenza di failover e ripristino di applicazioni multilivello distribuite in più macchine virtuali. L'utente raggruppa le macchine all'interno dei piani e aggiunge script e azioni manuali. I piani di ripristino possono essere integrati con i runbook di Automazione di Azure.
**Eseguire l'integrazione con tecnologie BCDR esistenti** | Site Recovery si integra con altre tecnologie BCDR. È ad esempio possibile usare Site Recovery per proteggere il back-end SQL Server dei carichi di lavoro aziendali, con supporto nativo per SQL Server AlwaysOn, e gestire così il failover dei gruppi di disponibilità.
**Eseguire l'integrazione con la libreria di automazione** | Un'avanzata libreria di automazione di Azure offre script pronti per la produzione e specifici dell'applicazione che possono essere scaricati e integrati con Site Recovery.
**Gestire le impostazioni di rete** | Site Recovery si integra con Azure per una gestione semplice della rete delle applicazioni, tra cui l'impostazione di indirizzi IP riservati, la configurazione di servizi di bilanciamento del carico e l'integrazione di Gestione traffico di Azure per cambi di rete efficienti.


## <a name="what-can-i-replicate"></a>Ciò che è possibile replicare?

**Supportato** | **Dettagli**
--- | ---
**Cosa è possibile replicare?** | VM di Azure tra aree di Azure.<br/><br/>  VM VMware locali, VM Hyper-V, server fisici di Windows e Linux in Azure.<br/><br/> VM VMware locali, VM Hyper-V, server fisici in Virtual Machine Manager (VMM).
**Quali aree sono supportate per Site Recovery?** | [Aree supportate](https://azure.microsoft.com/regions/services/) |
**Quali sistemi operativi sono richiesti per le macchine replicate?** | [Requisiti per le VM di Azure](site-recovery-support-matrix-azure-to-azure.md#support-for-replicated-machine-os-versions)</br></br>[Requisiti per le VM VMware](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)<br/><br/> Per le macchine virtuali Hyper-V, è supportato qualsiasi [sistema operativo guest](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) supportato da Azure e Hyper-V.<br/><br/> [Requisiti per i server fisici](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)
**Quali server/host VMware sono necessari?** | Le VM VMware possono trovarsi in [host vSphere/server vCenter supportati](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
**Quali carichi di lavoro è possibile replicare?** | È possibile replicare qualsiasi carico di lavoro in esecuzione in un computer di replica supportato. Inoltre, il team di Site Recovery ha eseguito test specifici dell'applicazione per un certo [numero di app](site-recovery-workload.md#workload-summary).



## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sul [supporto dei carichi di lavoro](site-recovery-workload.md).
* Introduzione alla [replica di VM di Azure tra aree](azure-to-azure-quickstart.md). 
