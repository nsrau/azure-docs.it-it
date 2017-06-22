---
title: "Che cos&quot;è Site Recovery? | Documentazione Microsoft"
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
ms.date: 03/14/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 276d7949e01b8936804514c5fe8bd26af438bd08
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017


---
# <a name="what-is-site-recovery"></a>Che cos'è Site Recovery?

Benvenuti nel servizio Azure Site Recovery. Questo articolo offre una rapida panoramica del servizio.

Le interruzioni sono causate da eventi naturali e problemi operativi. Le organizzazioni necessitano di una strategia per la continuità aziendale e il ripristino di emergenza (BCDR, Business Continuity and Disaster Recovery) in modo che durante i periodi di inattività pianificati e non pianificati i dati siano al sicuro, le app rimangano disponibili e l'azienda ripristini il prima possibile le normali condizioni di lavoro.

I servizi di ripristino di Azure contribuiscono alla strategia BCDR. Il servizio [Backup di Azure](https://docs.microsoft.com/en-us/azure/backup/) mantiene i dati al sicuro e ripristinabili. Site Recovery consente la replica, il failover e il ripristino dei carichi di lavoro, in modo che rimangano disponibili quando si verifica un errore.

## <a name="what-does-site-recovery-provide"></a>Che cosa offre Site Recovery?

- **Ripristino di emergenza nel cloud**: è possibile replicare e proteggere le VM in esecuzione in Azure, usando la soluzione di ripristino di emergenza da Azure ad Azure. È possibile replicare in Azure, invece che in un sito secondario, i carichi di lavoro in esecuzione nelle VM e nei server fisici, eliminando i costi e la complessità associati alla gestione di un data center secondario.
- **Modalità di replica flessibile per gli ambienti ibridi**: è possibile replicare i carichi di lavoro supportati in VM di Azure, in VM Hyper-V locali, in VM VMware e in server fisici Windows/Linux.
- **Migrazione**: è possibile usare Site Recovery per eseguire la migrazione di istanze AWS e locali a VM di Azure oppure per eseguire la migrazione di VM di Azure tra aree di Azure.
- **BCDR semplificato**: è possibile distribuire la replica da un'unica posizione nel Portale di Azure.  È possibile eseguire semplici failover e failback di uno o più macchine.
- **Resilienza**: Site Recovery gestisce la replica e il failover senza intercettare i dati delle applicazioni. I dati replicati vengono salvati in Archiviazione di Azure grazie alla resilienza offerta da Archiviazione di Azure. Le VM di Azure vengono create con i dati replicati in caso di failover.
- **Prestazioni di replica**: Site Recovery offre la replica continua per le VM di Azure e le VM WMware e una frequenza di replica di soli 30 secondi per Hyper-V. È possibile ridurre l'obiettivo del tempo di ripristino con il processo di ripristino automatizzato di Site Recovery e l'integrazione con [Gestione traffico di Azure](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/).
- **Coerenza con l'applicazione**: è possibile configurare snapshot coerenti con l'applicazione per i punti di recupero. Oltre ai dati dei dischi, tali snapshot acquisiscono tutti i dati in memoria e tutte le transazioni in corso.
- **Test senza interruzioni**: è possibile eseguire facilmente failover di test per supportare l'analisi del ripristino di emergenza senza alcun impatto sugli ambienti di produzione e sulla replica in corso.
- **Eseguire il failover e il ripristino flessibile**: è possibile eseguire failover pianificati senza perdita di dati per interruzioni previste o il failover non pianificato con perdita di dati minima, in base alla frequenza di replica, per emergenze impreviste. È possibile eseguire facilmente il failback al sito primario quando risulterà nuovamente disponibile.
- **Piani di ripristino personalizzati**: i piani di ripristino consentono di modellare e personalizzare il failover e il ripristino di applicazioni multilivello distribuite in più macchine virtuali. L'utente deve ordinare i gruppi all'interno dei piani e aggiungere script e azioni manuali. I piani di ripristino possono essere integrati con i runbook di Automazione di Azure.
- **App a più livelli**: è possibile creare piani di ripristino per il failover in sequenza e il ripristino di app a più livelli. È possibile raggruppare le macchine su livelli diversi (ad esempio database, web, app) all'interno di un piano di ripristino e personalizzare la modalità di failover e di avvio di ogni gruppo.
* **Integrazione con le tecnologie BCDR esistenti**: Site Recovery si integra con altre tecnologie BCDR. È ad esempio possibile usare Site Recovery per proteggere il back-end SQL Server dei carichi di lavoro aziendali, con supporto nativo per SQL Server AlwaysOn, e gestire così il failover dei gruppi di disponibilità.
* **Integrazione con libreria di automazione**: un'avanzata libreria di automazione di Azure offre script pronti per la produzione e specifici dell'applicazione che possono essere scaricati e integrati con Site Recovery.
* **Gestione semplice della rete**. La gestione di rete avanzata in Site Recovery e Azure semplifica i requisiti di rete per le applicazioni, come l'impostazione di indirizzi IP riservati, la configurazione di servizi di bilanciamento del carico e l'integrazione di Gestione traffico di Azure per cambi di rete efficienti.


## <a name="whats-supported"></a>Attività supportate

**Supportato** | **Dettagli**
--- | ---
**Quali aree sono supportate per Site Recovery?** | [Aree supportate](https://azure.microsoft.com/en-us/regions/services/) |
**Cosa è possibile replicare?** | VM di Azure (in anteprima), VM VMware locali, VM Hyper-V, server fisici Windows e Linux.
**Quali sistemi operativi sono richiesti per le macchine replicate?** | [Sistemi operativi supportati](site-recovery-support-matrix-azure-to-azure.md#support-for-replicated-machine-os-versions) per le VM di Azure<br></br>[Sistemi operativi supportati](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) per le macchine virtuali VMware<br/><br/> Per le macchine virtuali Hyper-V, è supportato qualsiasi [sistema operativo guest](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) supportato da Azure e Hyper-V.<br/><br/> [Sistemi operativi](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) per i server fisici
**Dove è possibile replicare?** | Per le VM di Azure, è possibile replicare un'altra area di Azure. <br></br> Per i computer locali, è possibile eseguire la replica in Archiviazione di Azure o in un data center locale.<br/><br/> 

>[!NOTE]
>
> Per Hyper-V, solo le macchine virtuali negli host Hyper-V gestiti nei cloud di System Center VMM possono replicare in un data center secondario.

**Quali server/host VMware sono necessari?** | Le VM VMware che si vuole replicare possono essere gestite da [host vSphere/server vCenter supportati](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)
**Quali carichi di lavoro è possibile replicare?** | È possibile replicare tutti i carichi di lavoro in esecuzione in un computer di replica supportato. Inoltre, il team di Site Recovery ha eseguito test specifici dell'applicazione per un certo [numero di app](site-recovery-workload.md#workload-summary).


## <a name="which-azure-portal"></a>Quale portale di Azure?

* È possibile distribuire Site Recovery nel [portale di Azure](https://portal.azure.com).
* Nel portale di Azure classico è possibile gestire Site Recovery con il modello di gestione classica dei servizi.
- Il portale classico deve essere usato solo per gestire le distribuzioni esistenti di Site Recovery. Non è possibile creare nuovi insiemi di credenziali nel portale classico.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulla [replica di macchine virtuali di Azure] (site-recovery-azure-to-azure.md)
* Altre informazioni sul [supporto dei carichi di lavoro](site-recovery-workload.md)
* Altre informazioni sull'[architettura e i componenti di Site Recovery](site-recovery-components.md)

