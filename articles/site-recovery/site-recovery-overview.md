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
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: d8e4e4bb7dd1e40d8c561adba04b8346fcb2127d
ms.lasthandoff: 03/15/2017


---
# <a name="what-is-site-recovery"></a>Che cos'è Site Recovery?

Benvenuti nel servizio Azure Site Recovery. Questo articolo offre una rapida panoramica del servizio.

Le interruzioni sono causate da eventi naturali e problemi operativi. Le organizzazioni necessitano di una strategia per la continuità aziendale e il ripristino di emergenza (BCDR, Business Continuity and Disaster Recovery) in modo che durante i periodi di inattività pianificati e non pianificati i dati siano al sicuro, le app rimangano disponibili e l'azienda ripristini il prima possibile le normali condizioni di lavoro.

I servizi di ripristino di Azure contribuiscono alla strategia BCDR. Il servizio [Backup di Azure](https://docs.microsoft.com/en-us/azure/backup/) mantiene i dati al sicuro e ripristinabili. Site Recovery consente la replica, il failover e il ripristino dei carichi di lavoro, in modo che rimangano disponibili quando si verifica un errore.

## <a name="what-does-site-recovery-provide"></a>Che cosa offre Site Recovery?

- **Ripristino di emergenza nel cloud**: è possibile replicare i carichi di lavoro in esecuzione sulle macchine virtuali e sui server fisici in Azure, piuttosto che su un sito secondario. eliminando i costi e la complessità associati alla gestione di un data center secondario.
- **Modalità di replica flessibile per gli ambienti ibridi**: è possibile replicare i carichi di lavoro in esecuzione nelle VM Hyper-V locali, in VMware e nei server fisici di Windows o Linux.
- **Migrazione**: è possibile usare Site Recovery per eseguire la migrazione di istanze AWS locali su macchine virtuali di Azure oppure eseguire la migrazione di macchine virtuali di Azure tra aree di Azure.
- **BCDR semplificato**: è possibile distribuire la replica da un'unica posizione nel Portale di Azure.  È possibile eseguire semplici failover e failback di uno o più macchine.
- **Resilienza**: Site Recovery gestisce la replica e il failover senza intercettare i dati delle applicazioni.
I dati replicati vengono archiviati in Archiviazione di Azure grazie alla resilienza offerta. Le VM di Azure vengono create con i dati replicati in caso di failover.
- **Prestazioni di replica**: Site Recovery offre una frequenza di replica di soli 30 secondi per Hyper-V e la replica continua per VMware. È possibile impostare le soglie dell'obiettivo del punto di ripristino (RPO) per controllare la frequenza con cui vengono creati i punti di ripristino dei dati ed è possibile ridurre l'obiettivo tempo di ripristino (RTO) con il processo di ripristino automatico di Site Recovery e l'integrazione con [Gestione traffico di Azure](https://azure.microsoft.com/en-us/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/)
- **Coerenza con l'applicazione**: la replica dei computer viene eseguita con snapshot coerenti con l'applicazione. Oltre ai dati dei dischi, tali snapshot acquisiscono tutti i dati in memoria e tutte le transazioni in corso.
- **Eseguire i test senza interruzioni**: è possibile eseguire facilmente failover di test per supportare l'analisi del ripristino di emergenza senza alcun impatto sugli ambienti di produzione.
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
**Cosa è possibile replicare?** | VM di VMware locali, VM di Hyper-V, server fisici di Windows e Linux.
**Quali sistemi operativi sono richiesti per le macchine replicate?** | [Sistemi operativi supportati](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) per le macchine virtuali VMware<br/><br/> Per le macchine virtuali Hyper-V, è supportato qualsiasi [sistema operativo guest](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) supportato da Azure e Hyper-V.<br/><br/> [Sistemi operativi](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) per i server fisici
**Dove è possibile replicare?** | Nell'archiviazione di Azure o in un data center secondario<br/><br/> Per Hyper-V, solo le macchine virtuali negli host Hyper-V gestiti nei cloud di System Center VMM possono replicare in un data center secondario.
**Quali server/host VMware sono necessari?** | Le macchine virtuali VMware che si desidera replicare possono essere gestite tramite [host vSphere/server vCenter supportati](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)
**Quali carichi di lavoro è possibile replicare** | È possibile replicare qualsiasi carico di lavoro in esecuzione in un computer di replica supportato. Inoltre, il team di Site Recovery ha eseguito test specifici dell'applicazione per un certo [numero di app](site-recovery-workload.md#workload-summary).


## <a name="which-azure-portal"></a>Quale portale di Azure?

* Site Recovery può essere distribuito sia nel [portale di Azure classico](https://manage.windowsazure.com/) che nel nuovo [Portale di Azure](https://portal.azure.com).
* Nel portale di Azure classico è possibile supportare Site Recovery con il modello di gestione classica dei servizi.
* Nel Portale di Azure è possibile supportare i modelli classici o i nuovi [modelli di distribuzione Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md).
- Il portale classico deve essere usato solo per gestire le distribuzioni esistenti di Site Recovery. Non è possibile creare nuovi insiemi di credenziali nel portale classico.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sul [supporto dei carichi di lavoro](site-recovery-workload.md)
* Altre informazioni sull'[architettura e i componenti di Site Recovery](site-recovery-components.md)

