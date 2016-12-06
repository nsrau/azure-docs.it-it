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
ms.date: 10/30/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 7455d6f99ed8ceb401224f98105f7b651f55c724
ms.openlocfilehash: 98bf94960c39565243995a1c4dd45787478b8f40


---
# <a name="what-is-site-recovery"></a>Che cos'è Site Recovery?
Benvenuti nel servizio Azure Site Recovery. Questo articolo offre una rapida panoramica di Site Recovery.

Le organizzazioni necessitano di una strategia per la continuità aziendale e il ripristino di emergenza (BCDR, Business Continuity and Disaster Recovery) che consenta di mantenere le app e i dati al sicuro e disponibili durante i periodi di inattività pianificati e non pianificati e che ripristini prima possibile le normali condizioni di lavoro.

Site Recovery favorisce l'attuazione della strategia di continuità aziendale e ripristino di emergenza orchestrando la replica delle macchine virtuali e dei server fisici locali. È possibile replicare i server e le VM da un data center primario locale al cloud (Azure) oppure a un data center secondario.

In caso di interruzioni nel sito primario, verrà eseguito il failover al sito secondario per mantenere i carichi di lavoro accessibili e disponibili. Quando la località primaria sarà di nuovo operativa, si tornerà a tale località.

## <a name="site-recovery-in-the-azure-portal"></a>Site Recovery nel portale di Azure
Azure offre due diversi [modelli di distribuzione](../azure-resource-manager/resource-manager-deployment-model.md) per creare e usare le risorse: Il modello di Azure Resource Manager e il modello di gestione classica dei servizi. Azure offre anche due portali, il [portale di Azure classico](https://manage.windowsazure.com/) e il [portale di Azure](https://portal.azure.com).

* Site Recovery può essere distribuito sia nel portale classico che nel portale di Azure.
* Nel portale di Azure classico è possibile supportare Site Recovery con il modello di gestione classica dei servizi.
* Nel portale di Azure è possibile supportare i modelli di distribuzione classica e Resource Manager.

Le informazioni in questo articolo sono applicabili al modello di distribuzione classica e al modello di distribuzione tramite il portale di Azure. Le differenze sono indicate ove applicabili.

## <a name="why-deploy-site-recovery"></a>Perché distribuire Azure Site Recovery?
Ecco cosa può fare Site Recovery per l'azienda:

* **Semplificare la continuità aziendale e il ripristino di emergenza**. È possibile eseguire la replica, il failover e il ripristino di più carichi di lavoro in un'unica posizione nel portale di Azure. Site Recovery gestisce la replica e il failover senza intercettare i dati delle applicazioni.
* **Offrire una modalità di replica flessibile**. È possibile replicare i carichi di lavoro in esecuzione nelle VM Hyper-V e VMware e nei server fisici Windows o Linux supportati.
* **Eliminare un data center secondario**. È possibile replicare i carichi di lavoro in Azure anziché in un sito secondario, eliminando i costi e la complessità associati alla gestione di un data center secondario. I dati replicati vengono archiviati in Archiviazione di Azure, caratterizzato da elevati livelli di resilienza. Le VM di Azure vengono create con i dati replicati in caso di failover.
* **Eseguire facilmente test della replica**. È possibile eseguire facilmente failover di test per supportare analisi del ripristino di emergenza senza alcun impatto sugli ambienti di produzione.
* **Eseguire il failover e il ripristino**. È possibile eseguire failover pianificati senza perdita di dati per interruzioni previste o il failover non pianificato con perdita di dati minima, in base alla frequenza di replica, per emergenze impreviste. È possibile eseguire il failback al sito primario quando risulterà nuovamente disponibile.
* **Failover di più macchine virtuali**. È possibile configurare piani di ripristino che includono script e runbook di automazione di Azure. I piani di ripristino consentono di modellare e personalizzare il failover e il ripristino di applicazioni multilivello distribuite in più macchine virtuali.
* **Offrire l'integrazione con le tecnologie BCDR esistenti**. Site Recovery si integra con altre tecnologie BCDR. È ad esempio possibile usare Site Recovery per proteggere il back-end SQL Server dei carichi di lavoro aziendali, con supporto nativo per SQL Server AlwaysOn, e gestire così il failover dei gruppi di disponibilità.

## <a name="what-can-i-replicate"></a>Ciò che è possibile replicare?
Ecco un riepilogo di ciò che è possibile replicare con Site Recovery.

![Da sito locale a sito locale](./media/site-recovery-overview/asr-overview-graphic.png)

| **REPLICA** | **DESTINAZIONE REPLICA** |
| --- | --- |
| Macchine virtuali VMware locali |[Azzurro](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Sito secondario](site-recovery-vmware-to-vmware.md) |
| Macchine virtuali Hyper-V locali gestite in cloud VMM |[Azzurro](site-recovery-vmm-to-azure.md)<br/><br/> [Sito secondario](site-recovery-vmm-to-vmm.md) |
| Macchine virtuali Hyper-V locali gestite in cloud VMM, con archiviazione SAN |[Sito secondario](site-recovery-vmm-san.md) |
| Macchine virtuali Hyper-V locali senza VMM |[Azzurro](site-recovery-hyper-v-site-to-azure.md) |
| Server fisici Windows/Linux locali |[Azzurro](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Sito secondario](site-recovery-vmware-to-vmware.md) |

## <a name="how-does-site-recovery-protect-workloads"></a>In che modo Site Recovery protegge i carichi di lavoro?
Site Recovery offre una replica compatibile con le applicazioni, per consentire l'esecuzione continua e coerente dei carichi di lavoro e delle app in caso di interruzioni.

* **Snapshot coerenti con l'applicazione**. La replica dei computer viene eseguita con snapshot coerenti con l'applicazione, per app a uno o più livelli. Oltre ai dati dei dischi, tali snapshot acquisiscono tutti i dati in memoria e tutte le transazioni in corso.
* **Replica quasi sincrona**. Site Recovery offre una frequenza di replica di soli 30 secondi per Hyper-V e la replica continua per VMware.
* **Piani di ripristino flessibili**. È possibile creare e personalizzare i piani di ripristino con script esterni e azioni manuali. L'integrazione con i runbook di automazione di Azure consente di ripristinare un intero stack di applicazioni con un solo clic.
* **Integrazione con SQL Server AlwaysOn**. È possibile gestire il failover dei gruppi di disponibilità con i piani di ripristino.
* **Libreria di automazione**. Un'avanzata libreria di automazione di Azure offre script pronti per la produzione e specifici dell'applicazione che possono essere scaricati e integrati con Site Recovery.
* **Gestione semplice della rete**. La gestione di rete avanzata in Site Recovery e Azure semplifica i requisiti di rete per le applicazioni, come l'impostazione di indirizzi IP riservati, la configurazione di servizi di bilanciamento del carico e l'integrazione di Gestione traffico di Azure per cambi di rete efficienti.

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni, vedere [Quali carichi di lavoro è possibile proteggere con Azure Site Recovery?](site-recovery-workload.md)
* Per altre informazioni sull'architettura di Site Recovery, vedere [Funzionamento di Azure Site Recovery](site-recovery-components.md)




<!--HONumber=Nov16_HO4-->


