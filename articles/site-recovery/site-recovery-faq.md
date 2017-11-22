---
title: 'Azure Site Recovery: domande frequenti | Microsoft Docs'
description: Questo articolo illustra le domande frequenti su Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5cdc4bcd-b4fe-48c7-8be1-1db39bd9c078
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/19/2017
ms.author: raynew
ms.openlocfilehash: ad6f70cf9c2f420e887031c8b240d2f831e6c359
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Azure Site Recovery: domande frequenti
In questo articolo sono riportate le domande frequenti su Azure Site Recovery. Eventuali domande successive alla lettura di questo articolo possono essere pubblicate nel [forum relativo ai Servizi di ripristino di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).

## <a name="general"></a>Generale
### <a name="what-does-site-recovery-do"></a>Quali sono le funzioni di Site Recovery?
Site Recovery favorisce l'attuazione della strategia di continuità aziendale e ripristino di emergenza mediante la gestione e l'automatizzazione delle operazioni di replica delle macchine virtuali di Azure tra le aree, delle macchine virtuali e dei server fisici locali in Azure o in un data center secondario. [Altre informazioni](site-recovery-overview.md).

### <a name="what-can-site-recovery-protect"></a>Quali elementi può proteggere Site Recovery?
* **Macchine virtuali di Azure**: Site Recovery può replicare qualsiasi carico di lavoro in esecuzione in una macchina virtuale di Azure supportata
* **Macchine virtuali Hyper-V**: Site Recovery può proteggere qualsiasi carico di lavoro in esecuzione in una macchina virtuale Hyper-V.
* **Server fisici**: Site Recovery può proteggere server fisici che eseguono Windows o Linux.
* **Macchine virtuali VMware**: Site Recovery può proteggere qualsiasi carico di lavoro in esecuzione in una macchina virtuale VMware.



### <a name="can-i-replicate-azure-vms"></a>È possibile replicare le macchine virtuali di Azure?
Sì, è possibile replicare le macchine virtuali di Azure supportate tra le aree di Azure. [Altre informazioni](site-recovery-azure-to-azure.md).

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Cosa occorre in Hyper-V per orchestrare la replica con Site Recovery?
Gli elementi necessari per il server host Hyper-V dipendono dallo scenario di distribuzione. Verificare i prerequisiti di Hyper-V in:

* [Replica di macchine virtuali Hyper-V in Azure (senza VMM)](site-recovery-hyper-v-site-to-azure.md)
* [Replica di macchine virtuali Hyper-V in Azure (con VMM)](site-recovery-vmm-to-azure.md)
* [Replica di macchine virtuali Hyper-V in un data center secondario](site-recovery-vmm-to-vmm.md)
* Se si esegue la replica in un centro dati secondario, vedere [Sistemi operativi guest supportati per le VM Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
* Se si esegue la replica in Azure, Site Recovery supporta tutti i sistemi operativi guest [supportati da Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>È possibile proteggere le macchine virtuali quando Hyper-V è in esecuzione in un sistema operativo client?
No, le VM devono trovarsi in un server host Hyper-V in esecuzione in un computer server Windows supportato. Se è necessario proteggere un computer client, è possibile replicarlo come computer fisico in [Azure](site-recovery-vmware-to-azure.md) o in un [centro dati secondario](site-recovery-vmware-to-vmware.md).

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Quali carichi di lavoro è possibile proteggere con Site Recovery?
È possibile usare Site Recovery per proteggere la maggior parte dei carichi di lavoro in esecuzione in una VM o in un server fisico supportati. Site Recovery fornisce il supporto per la replica compatibile con l'applicazione per consentire il ripristino delle app a uno stato intelligente. Si integra con le applicazioni Microsoft, ad esempio SharePoint, Exchange, Dynamics, SQL Server e Active Directory, e opera a stretto contatto con importanti fornitori, tra cui Oracle, SAP, IBM e Red Hat. [Altre informazioni](site-recovery-workload.md) sulla protezione del carico di lavoro.

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Gli host Hyper-V devono trovarsi nei cloud VMM?
Per la replica in un data center secondario, le VM Hyper-V devono trovarsi in server host Hyper-V in un cloud VMM. Se si vuole eseguire la replica in Azure, è possibile replicare le VM con o senza cloud VMM. [Altre informazioni](tutorial-hyper-v-to-azure.md) sulla replica Hyper-V in Azure.

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>È possibile distribuire Site Recovery con VMM se si dispone di un solo server VMM?

Sì. È possibile replicare le VM in server Hyper-V nel cloud VMM in Azure oppure tra cloud VMM nello stesso server. Per la replica da locale a locale è consigliabile avere un server VMM nei siti primario e secondario.  

### <a name="what-physical-servers-can-i-protect"></a>Quali server fisici è possibile proteggere?
È possibile replicare server fisici che eseguono Windows e Linux in Azure o in un sito secondario. [Informazioni](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) sui requisiti del sistema operativo.  Gli stessi requisiti valgono se si esegue la replica di server fisici in Azure o in un sito secondario.


Si noti che se il server locale si arresta, i server fisici vengono eseguiti come le macchine virtuali in Azure. Il failback a un server fisico locale non è attualmente supportato. Per un computer protetto come fisico, è possibile solo il failback a una macchina virtuale VMware.

### <a name="what-vmware-vms-can-i-protect"></a>Quali macchine virtuali VMware è possibile proteggere?

Per proteggere le VM VMware, è necessario un hypervisor vSphere e macchine virtuali che eseguono strumenti VMware. È anche consigliabile avere un server VMware vCenter per gestire gli hypervisor. [Altre informazioni](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) sui requisiti specifici per la replica di server e VM VMware in Azure o in un sito secondario.


### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>È possibile gestire il ripristino di emergenza per le succursali con Site Recovery?
Sì. Quando si usa Site Recovery per gestire la replica e il failover nelle succursali, si ottiene una gestione unificata e vengono visualizzati tutti i carichi di lavoro delle succursali in una posizione centrale. È possibile eseguire facilmente i failover e amministrare il ripristino di emergenza di tutte le succursali senza abbandonare la sede centrale.

## <a name="pricing"></a>Prezzi
Per domande relative a i prezzi, consultare le domande frequenti in [Prezzi di Azure Site Recovery](https://azure.microsoft.com/en-in/pricing/details/site-recovery/).

## <a name="security"></a>Sicurezza
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>I dati di replica vengono inviati al servizio Site Recovery?
No, Site Recovery non intercetta i dati replicati né raccoglie informazioni su ciò che è in esecuzione sulle macchine virtuali o sui server fisici.
I dati di replica vengono scambiati tra host Hyper-V, hypervisor VMware o server fisici e Archiviazione di Azure o il sito secondario. Site Recovery non è in grado di intercettare i dati. Al servizio Site Recovery vengono inviati solo i metadati necessari per gestire la replica e il failover.  

Site Recovery è certificato ISO 27001:2013, 27018, HIPAA e DPA e le valutazioni SOC2 e FedRAMP JAB sono in fase di completamento.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Per motivi di conformità, è necessario che anche i metadati locali restino all'interno della stessa area geografica. Site Recovery può offrire vantaggi?
Sì. Quando si crea un insieme di credenziali di Site Recovery in un'area, tutti i metadati necessari per abilitare e gestire la replica e il failover restano all'interno dei confini di tale area geografica.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery consente di crittografare la replica?
Per la replica di macchine virtuali e server fisici tra siti locali, è supportata la crittografia in transito. Per la replica di macchine virtuali e server fisici in Azure, sono supportate sia la crittografia in transito che la [crittografia inattiva (in Azure)](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption).

## <a name="replication"></a>Replica

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>È possibile replicare tramite una VPN da sito a sito in Azure?
Azure Site Recovery replica i dati in un account di archiviazione di Azure su un endpoint pubblico. La replica non avviene tramite una rete VPN da sito a sito. È possibile creare una rete VPN da sito a sito con una rete virtuale di Azure. Ciò non interferisce con la replica di Site Recovery.

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>È possibile usare ExpressRoute per replicare macchine virtuali in Azure?
Sì, è possibile. Azure Site Recovery replica i dati in un account di archiviazione di Azure su un endpoint pubblico. Per usare ExpressRoute per la replica con Site Recovery, è necessario configurare il [peering pubblico](../expressroute/expressroute-circuit-peerings.md#azure-public-peering). Dopo che è stato eseguito il failover delle macchine virtuali su una rete virtuale di Azure, è possibile accedere alle macchine virtuali usando la configurazione di [peering privato](../expressroute/expressroute-circuit-peerings.md#azure-private-peering) con la rete virtuale di Azure.

### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>È necessario disporre di prerequisiti specifici per la replica di macchine virtuali in Azure?
Le macchine virtuali da replicare in Azure devono essere conformi ai [requisiti di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

L'account utente di Azure deve disporre di determinate [autorizzazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) per abilitare la replica di una nuova macchina virtuale in Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>È possibile replicare le macchine virtuali Hyper-V di seconda generazione in Azure?
Sì. Site Recovery esegue la conversione dalla seconda generazione alla prima durante il failover. In caso di failback, la macchina virtuale viene riconvertita nella seconda generazione. [Altre informazioni](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>Se si esegue la replica in Azure, come vengono addebitati i costi per le macchine virtuali di Azure?
Durante la replica regolare, i dati vengono replicati nell'archiviazione di Azure con ridondanza geografica e non vengono addebitati i costi di macchine virtuali IaaS di Azure. Questo aspetto rappresenta un vantaggio significativo. Quando si esegue un failover in Azure, Site Recovery crea automaticamente le macchine virtuali IaaS di Azure e in seguito vengono fatturate le risorse di calcolo utilizzate in Azure.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>È possibile automatizzare gli scenari di Site Recovery con un SDK?
Sì. È possibile automatizzare i flussi di lavoro di Site Recovery usando l'API Rest, PowerShell o Azure SDK. Scenari attualmente supportati per la distribuzione di Site Recovery tramite PowerShell:

* [Replica da VM Hyper-V nei cloud VMM ad Azure PowerShell Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
* [Replica da VM Hyper-V senza VMM ad Azure PowerShell Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)

### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>Se si esegue la replica in Azure che tipo di account di archiviazione è necessario?
È necessario un account di archiviazione con ridondanza locale o con ridondanza geografica. È consigliabile usare l'archiviazione con ridondanza geografica per una maggiore resilienza dei dati in caso di interruzione del servizio a livello di area o se non è possibile recuperare l'area primaria. L'account deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino. Archiviazione Premium è supportato per la replica di macchine virtuali VMware e Hyper-V e di server fisici, quando si distribuisce Site Recovery nel portale di Azure.

### <a name="how-often-can-i-replicate-data"></a>Con quale frequenza è possibile eseguire la replica dei dati?
* **Hyper-V:** le macchine virtuali Hyper-V possono essere replicate ogni 30 secondi (eccetto per Archiviazione Premium), 5 minuti o 15 minuti. Se è stata configurata la replica SAN, questa sarà sincrona.
* **VMware e server fisici:** in questo caso la frequenza di replica non è rilevante. La replica è continua.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>È possibile estendere la replica dal sito di ripristino esistente a un sito terziario?
No, la replica concatenata o estesa non è supportata. Richiedere questa funzionalità nel [forum dei commenti](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>È possibile eseguire una replica offline la prima volta che si esegue la replica in Azure?
Questa funzionalità non è supportata. Richiedere questa funzionalità nel [forum dei commenti](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>È possibile escludere dischi specifici dalla replica?
Questa opzione è supportata quando si [esegue la replica da VM VMware e Hyper-V](site-recovery-exclude-disk.md) ad Azure tramite il portale di Azure.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>È possibile eseguire la replica delle macchine virtuali con i dischi dinamici?
I dischi dinamici sono supportati durante la replica delle macchine virtuali Hyper-V, anche durante la replica delle VM VMware e dei computer fisici in Azure. Il disco del sistema operativo deve essere un disco di base.

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>È possibile aggiungere un nuovo computer a un gruppo di replica esistente?
L'aggiunta di nuovi computer ai gruppi di replica esistenti è supportata. A tale scopo, selezionare il gruppo di replica (dal pannello "Elementi replicati") e fare clic con il pulsante destro sul menu di scelta rapida nel gruppo di replica e selezionare l'opzione appropriata.

![Aggiungere al gruppo di replica](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>È possibile applicare limitazioni della larghezza di banda allocata per il traffico di replica Hyper-V?
Sì. Ulteriori informazioni sulla limitazione della larghezza di banda sono disponibili negli articoli sulla distribuzione:

* [Pianificazione della capacità per la replica da VM VMware e server fisici](site-recovery-plan-capacity-vmware.md)
* [Pianificazione della capacità per la replica da VM Hyper-V in Azure](site-recovery-capacity-planning-for-hyper-v-replication.md)

## <a name="failover"></a>Failover
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>Se si esegue il failover in Azure, come è possibile accedere alle macchine virtuali dopo il failover?
È possibile accedere alle VM di Azure tramite una connessione Internet sicura, una connessione VPN da sito a sito o ExpressRoute di Azure. Per poter eseguire la connessione, è necessario completare una serie di operazioni. [Altre informazioni](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Se si esegue il failover in Azure, in che modo Azure si assicura che i dati siano resilienti?
Azure è progettato nell'ottica della resilienza. Site Recovery è già progettato per il failover a un centro dati secondario di Azure in conformità con il contratto di servizio di Azure, se necessario. In questo caso, Microsoft si assicura che i metadati e gli insiemi di credenziali rimangano nella stessa area geografica selezionata per l'insieme di credenziali.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Se si esegue la replica tra due data center, che cosa accade se nel data center principale si verifica un'interruzione imprevista?
È possibile attivare un failover non pianificato dal sito secondario. Per eseguire il failover, in Site Recovery non è necessaria la connettività dal sito primario.

### <a name="is-failover-automatic"></a>Il failover è automatico?
Il failover non è automatico. Le operazioni di failover si avviano con un singolo clic nel portale oppure mediante [PowerShell per Site Recovery](/powershell/module/azurerm.siterecovery) . Il failback è una semplice operazione nel portale di Site Recovery.

Per avviarlo in automatico, è possibile usare Orchestrator o Operations Manager locale per rilevare un errore della macchina virtuale e quindi attivare il failover tramite l'SDK.

* [Ulteriori informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.
* [Altre informazioni](site-recovery-failover.md) sul failover.
* [Altre informazioni](site-recovery-failback-azure-to-vmware.md) sul failback di VM VMware e server fisici

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-failover-back-to-a-different-host"></a>Se l'host locale non risponde o è bloccato, è possibile eseguire il failback a un host diverso?
Sì, è possibile usare il ripristino nel percorso alternativo per eseguire il failback in un host diverso da Azure. Sono disponibili altre informazioni sulle opzioni indicate dai collegamenti seguenti per le macchine virtuali Hyper-V e VMware.

* [Per macchine virtuali VMware](site-recovery-how-to-failback-azure-to-vmware.md#fail-back-to-the-original-or-alternate-location)
* [Per macchine virtuali Hyper-V](site-recovery-failback-from-azure-to-hyper-v.md#failback-to-an-alternate-location)

## <a name="service-providers"></a>Provider di servizi
### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Per i provider di servizi, Site Recovery funziona per modelli di infrastruttura dedicati e condivisi?
Sì, Site Recovery supporta i modelli di infrastruttura dedicati e condivisi.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Per i provider di servizi l'identità del tenant è condivisa con il servizio Site Recovery?
No. L'identità del tenant rimane anonima. Per i tenant non è necessario l'accesso al portale di Site Recovery. Solo l'amministratore del provider di servizi interagisce con il portale.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>I dati delle applicazioni tenant potranno mai essere ricevuti da Azure?
Quando si esegue la replica tra i siti di proprietà del provider di servizi, i dati delle applicazioni non vengono mai inviati ad Azure. I dati vengono crittografati in transito e replicati direttamente tra i siti del provider di servizi.

Se si esegue la replica in Azure, i dati dell'applicazione vengono inviati all'archiviazione di Azure, ma non al servizio Site Recovery. I dati vengono crittografati in transito e rimangono crittografati in Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>I tenant riceveranno una fattura per ogni servizio Azure?
No. La fatturazione di Azure avviene direttamente con il provider di servizi. I provider di servizi sono responsabili della generazione di fatture specifiche per i tenant.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Se si esegue la replica in Azure, è sempre necessario eseguire le macchine virtuali in Azure?
No, i dati vengono replicati in un account di archiviazione di Azure nella sottoscrizione. Quando si esegue un failover di test (un'analisi del ripristino di emergenza) o un failover effettivo, Site Recovery crea automaticamente macchine virtuali nella sottoscrizione.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>È possibile garantire isolamento a livello di tenant durante la replica in Azure?
Sì.

### <a name="what-platforms-do-you-currently-support"></a>Quali piattaforme sono attualmente supportate?
Sono supportate distribuzioni basate su Azure Pack, Cloud Platform System e System Center (2012 e versioni successive). [Alter informazioni](https://technet.microsoft.com/library/dn850370.aspx) sull'integrazione di Azure Pack e Site Recovery.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Sono supportate singole distribuzioni di Azure Pack e del server VMM?
Sì, è possibile eseguire la replica da macchine virtuali Hyper-V ad Azure o tra siti del provider di servizi.  Si noti che se si esegue la replica tra i siti del provider di servizi, l'integrazione del runbook di Azure non è disponibile.

## <a name="next-steps"></a>Passaggi successivi
* Leggere la [panoramica di Site Recovery](site-recovery-overview.md)
* Informazioni sull' [architettura di Site Recovery](site-recovery-components.md)  
