---
title: Domande frequenti - Replica da VMware ad Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo riepiloga le domande frequenti relative alla replica di macchine virtuali VMware locali in Azure mediante Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/15/2018
ms.author: raynew
ms.openlocfilehash: 7e556bff2e9ebdd1efc969660cc8b4a33f3adcdb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="common-questions---vmware-to-azure-replication"></a>Domande frequenti - Replica da VMware ad Azure

Questo articolo fornisce le risposte alle domande frequenti relative alla replica di macchine virtuali VMware locali in Azure. Eventuali domande successive alla lettura di questo articolo possono essere pubblicate nel [forum relativo ai Servizi di ripristino di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Generale
### <a name="how-is-site-recovery-priced"></a>Come viene stabilito il prezzo di Site Recovery?
Vedere [Dettagli relativi ai prezzi di Azure Site Recovery](https://azure.microsoft.com/en-in/pricing/details/site-recovery/).

### <a name="how-do-i-pay-for-azure-vms"></a>Come vengono addebitati i costi per le macchine virtuali di Azure?
Durante la replica, i dati vengono replicati in Archiviazione di Azure e non viene addebitato alcun costo per le modifiche alle macchine virtuali. Quando si esegue un failover in Azure, Site Recovery crea automaticamente le macchine virtuali IaaS di Azure. In seguito vengono fatturate le risorse di calcolo utilizzate in Azure.

### <a name="what-can-i-do-with-vmware-to-azure-replication"></a>Cosa si può fare con la replica da VMware ad Azure?
- **Ripristino di emergenza**: è possibile configurare un ripristino di emergenza completo. In questo scenario viene eseguita la replica di macchine virtuali VMware locali in Archiviazione di Azure. Quindi, se l'infrastruttura locale non è disponibile, è possibile effettuare il failover in Azure. Quando si esegue un failover, vengono create macchine virtuali di Azure con i dati replicati. È possibile accedere ad app e carichi di lavoro nelle macchine virtuali di Azure finché il data center locale non è di nuovo disponibile. Quindi si può eseguire il failback da Azure al proprio sito locale.
- **Migrazione**: è possibile usare Site Recovery per eseguire la migrazione delle macchine virtuali VMware locali in Azure. In questo scenario viene eseguita la replica di macchine virtuali VMware locali in Archiviazione di Azure. Quindi si esegue il failover dal sito locale in Azure. Dopo il failover, le app e i carichi di lavoro sono disponibili e in esecuzione sulle macchine virtuali di Azure.



## <a name="azure"></a>Azure
### <a name="what-do-i-need-in-azure"></a>Cosa è necessario avere in Azure?
Sono necessari una sottoscrizione di Azure, un insieme di credenziali di Servizi di ripristino, un account di archiviazione e una rete virtuale. L'insieme di credenziali, l'account di archiviazione e la rete devono trovarsi nella stessa area.

### <a name="what-azure-storage-account-do-i-need"></a>Quale account di archiviazione di Azure serve?
È necessario un account di archiviazione con ridondanza locale o con ridondanza geografica. È consigliabile usare l'archiviazione con ridondanza geografica per una maggiore resilienza dei dati in caso di interruzione del servizio a livello di area o se non è possibile recuperare l'area primaria. L'account di archiviazione Premium è supportato.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>L'account Azure deve avere le autorizzazioni per creare macchine virtuali?
Se si ha il ruolo di amministratore della sottoscrizione, si hanno le autorizzazioni di replica necessarie. In caso contrario, sono necessarie le autorizzazioni per creare una macchina virtuale di Azure nel gruppo di risorse e nella rete virtuale specificata durante la configurazione di Site Recovery e le autorizzazioni di scrittura sull'account di archiviazione selezionato. [Altre informazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).



## <a name="on-premises"></a>Locale 

### <a name="what-do-i-need-on-premises"></a>Cosa è necessario avere in locale?
In locale sono necessari i componenti di Site Recovery, installati in una singola macchina virtuale VMware. Occorre avere anche un'infrastruttura VMware con almeno un host ESXi, oltre a un server vCenter come scelta consigliata. Inoltre, sono necessarie una o più macchine virtuali VMware da replicare. [Altre informazioni](vmware-azure-architecture.md) sull'architettura della replica da VMware ad Azure.

### <a name="where-do-on-premises-vms-replicate-to"></a>Dove viene eseguita la replica delle macchine virtuali locali?
I dati vengono replicati in Archiviazione di Azure. Quando si esegue un failover, Site Recovery crea automaticamente le macchine virtuali di Azure dall'account di archiviazione.

### <a name="what-apps-can-i-replicate"></a>Quali app è possibile replicare?
È possibile replicare qualsiasi app o carico di lavoro in esecuzione su una macchina virtuale VMware conforme ai [requisiti di replica](vmware-physical-azure-support-matrix.md##replicated-machines). Site Recovery fornisce il supporto per la replica compatibile con l'applicazione per consentire il failover e il failback delle app a uno stato intelligente. Site Recovery si integra con le applicazioni Microsoft, ad esempio SharePoint, Exchange, Dynamics, SQL Server e Active Directory, e opera a stretto contatto con importanti fornitori, tra cui Oracle, SAP, IBM e Red Hat. [Altre informazioni](site-recovery-workload.md) sulla protezione del carico di lavoro.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>È possibile eseguire la replica in Azure tramite una VPN da sito a sito?
Site Recovery replica i dati dal sito locale ad Archiviazione di Azure su un endpoint pubblico o mediante peering pubblico ExpressRoute. La replica su una rete VPN da sito a sito non è supportata.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>È possibile eseguire la replica in Azure con ExpressRoute?
Sì, è possibile usare ExpressRoute per replicare macchine virtuali in Azure. Site Recovery replica i dati in un account di archiviazione di Azure su un endpoint pubblico ed è necessario configurare il [peering pubblico](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) per la replica di Site Recovery. Dopo il failover delle macchine virtuali in una rete virtuale di Azure, è possibile accedervi usando il [peering privato](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).


### <a name="why-cant-i-replicate-over-vpn"></a>Perché non è possibile eseguire la replica su VPN?

Quando si esegue la replica in Azure, il traffico di replica raggiunge gli endpoint pubblici di un account di archiviazione di Azure, quindi è possibile eseguire la replica solo su Internet pubblico con ExpressRoute (peering pubblico), mentre la VPN non è supportata. 



## <a name="what-are-the-replicated-vm-requirements"></a>Quali sono i requisiti delle macchine virtuali replicate?

Per la replica è necessario che la macchina virtuale VMware esegua un sistema operativo supportato. Inoltre, la macchina virtuale deve soddisfare i requisiti relativi alle macchine virtuali di Azure. Vedere [altre informazioni](vmware-physical-azure-support-matrix.md##replicated-machines) nella matrice di supporto.

## <a name="how-often-can-i-replicate-to-azure"></a>Con quale frequenza è possibile eseguire la replica in Azure?
La replica di macchine virtuali VMware in Azure è continua.

## <a name="can-i-extend-replication"></a>È possibile estendere la replica?
No, la replica concatenata o estesa non è supportata. Richiedere questa funzionalità nel [forum dei commenti](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

## <a name="can-i-do-an-offline-initial-replication"></a>È possibile eseguire una replica iniziale offline?
Questa funzionalità non è supportata. Richiedere questa funzionalità nel [forum dei commenti](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>È possibile escludere dei dischi?
Sì, è possibile escludere dischi dalla replica. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>È possibile eseguire la replica delle macchine virtuali con i dischi dinamici?
I dischi dinamici possono essere replicati. Il disco del sistema operativo deve essere un disco di base.

### <a name="can-i-add-a-new-vm-to-an-existing-replication-group"></a>È possibile aggiungere una nuova macchina virtuale a un gruppo di replica esistente?
Sì.

## <a name="configuration-server"></a>Server di configurazione

### <a name="what-does-the-configuration-server-do"></a>Qual è la funzione del server di configurazione?
Il server di configurazione esegue i componenti di Site Recovery locali, tra cui: 
- Il server di configurazione che coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
- Il server di elaborazione che funge da gateway di replica. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia ad Archiviazione di Azure. Il server di elaborazione installa anche il servizio Mobility nelle macchine virtuali da replicare ed esegue l'individuazione automatica delle macchine virtuali VMware locali.
- Il server di destinazione master che gestisce i dati di replica durante il failback da Azure.

### <a name="where-do-i-set-up-the-configuration-server"></a>Dove si configura il server di configurazione?
Per il server di configurazione è necessaria una macchina virtuale VMware locale a disponibilità elevata.

### <a name="what-are-the-requirements-for-the-configuration-server"></a>Quali sono i requisiti del server di configurazione?

Esaminare i [prerequisiti](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>È possibile configurare manualmente il server di configurazione invece di usare un modello?
È consigliabile usare l'ultima versione del modello OVF per [creare la macchina virtuale del server di configurazione](vmware-azure-deploy-configuration-server.md). Se per qualche motivo non è possibile, ad esempio se non si ha accesso al server VMware, è possibile [scaricare il file di Installazione unificata](physical-azure-set-up-source.md) dal portale ed eseguirlo su una macchina virtuale. 

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Un server di configurazione può eseguire la replica in più aree?
No. Occorre configurare un server di configurazione in ogni area.

### <a name="can-i-host-a-configuration-server-in-azure"></a>È possibile ospitare un server di configurazione in Azure?
È possibile, ma a questo scopo la macchina virtuale di Azure che esegue il server di configurazione dovrebbe comunicare con l'infrastruttura VMware locale e con le macchine virtuali. Il sovraccarico non è probabilmente fattibile.


### <a name="where-can-i-get-the-latest-version-of-the-configuration-server-template"></a>Dove è possibile ottenere la versione più recente del modello di server di configurazione?
Scaricare la versione più recente dall'[Area download Microsoft](https://aka.ms/asrconfigurationserver).

### <a name="how-do-i-update-the-configuration-server"></a>Come si aggiorna il server di configurazione?
Occorre installare gli aggiornamenti cumulativi. Le informazioni più recenti sugli aggiornamenti sono disponibili nella [pagina wiki degli aggiornamenti](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

## <a name="mobility-service"></a>Servizio Mobility

### <a name="where-can-i-find-the-mobility-service-installers"></a>Dove è possibile trovare i programmi di installazione del servizio Mobility?
I programmi di installazione sono nella cartella **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** nel server di configurazione.

## <a name="how-do-i-install-the-mobility-service"></a>Come si installa il servizio Mobility?
Occorre installarlo su ogni macchina virtuale da replicare usando un'[installazione push](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) o un'installazione manuale [dall'interfaccia utente](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui) o [mediante PowerShell](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt). In alternativa, è possibile usare uno strumento di distribuzione come [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md) o [Automazione di Azure e DSC](vmware-azure-mobility-deploy-automation-dsc.md).



## <a name="security"></a>Sicurezza

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>A quale scopo Site Recovery deve accedere ai server VMware?
Site Recovery richiede l'accesso ai server VMware per:

- Configurare una macchina virtuale VMware che esegua il server di configurazione e altri componenti di Site Recovery locali. [Altre informazioni](vmware-azure-deploy-configuration-server.md)
- Individuare automaticamente le macchine virtuali per la replica. Leggere le informazioni sulla preparazione di un account per l'individuazione automatica. [Altre informazioni](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>A quale scopo Site Recovery deve accedere alle macchine virtuali VMware?

- Per eseguire la replica, il servizio Mobility di Site Recovery deve essere installato e in esecuzione su una macchina virtuale VMware. È possibile distribuire lo strumento manualmente o specificare che Site Recovery esegua un'installazione push del servizio quando si abilita la replica per una macchina virtuale. Per l'installazione push Site Recovery necessita di un account da usare per installare il componente del servizio. [Altre informazioni](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation). Questa installazione viene eseguita dal server di elaborazione, che per impostazione predefinita è in esecuzione nel server di configurazione. [Altre informazioni](vmware-azure-install-mobility-service.md) sull'installazione del servizio Mobility.
- Durante la replica, le macchine virtuali comunicano con Site Recovery nel modo seguente:
    - Le macchine virtuali comunicano con il server di configurazione sulla porta HTTPS 443 per la gestione delle repliche.
    - Le macchine virtuali inviano i dati di replica al server di elaborazione sulla porta HTTPS 9443 (può essere modificata).
    - Se si abilita la coerenza tra più macchine virtuali, le macchine virtuali comunicano tra loro sulla porta 20004.


### <a name="is-replication-data-sent-to-site-recovery"></a>I dati di replica vengono inviati a Site Recovery?
No, Site Recovery non intercetta i dati replicati né raccoglie informazioni su ciò che è in esecuzione sulle macchine virtuali. I dati di replica vengono scambiati tra gli hypervisor VMware e Archiviazione di Azure. Site Recovery non è in grado di intercettare i dati. Al servizio Site Recovery vengono inviati solo i metadati necessari per gestire la replica e il failover.  

Site Recovery è certificato ISO 27001:2013, 27018, HIPAA e DPA e le valutazioni SOC2 e FedRAMP JAB sono in fase di completamento.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>È possibile mantenere metadati locali in un'area geografica?
Sì. Quando si crea un insieme di credenziali in un'area, tutti i metadati usati da Site Recovery restano all'interno dei confini di tale area geografica.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery consente di crittografare la replica?
Sì, sono supportate sia la crittografia in transito che la [crittografia in Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption).


## <a name="failover-and-failback"></a>Failover e failback
### <a name="how-far-back-can-i-recover"></a>Fino a quando può risalire il recupero?
Per la replica da VMware ad Azure il punto di recupero meno recente che si può usare è di 72 ore.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Come si accede alle macchine virtuali di Azure dopo il failover?
Dopo il failover, è possibile accedere alle macchine virtuali di Azure tramite una connessione Internet sicura, una connessione VPN da sito a sito o ExpressRoute di Azure. Per poter eseguire la connessione, è necessario completare una serie di operazioni. [Altre informazioni](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>I dati di cui è stato effettuato il failover sono resilienti?
Azure è progettato nell'ottica della resilienza. Site Recovery è progettato per il failover a un data center secondario di Azure in conformità con il contratto di servizio di Azure. Al momento del failover, Microsoft si assicura che i metadati e gli insiemi di credenziali rimangano nella stessa area geografica selezionata per l'insieme di credenziali.

### <a name="is-failover-automatic"></a>Il failover è automatico?
Il [failover](site-recovery-failover.md) non è automatico. Può essere avviato con un singolo clic nel portale oppure mediante [PowerShell](/powershell/module/azurerm.siterecovery).

### <a name="can-i-fail-back-to-a-different-location"></a>È possibile eseguire il failback in una posizione diversa?
Sì, se è stato effettuato il failover ad Azure, è possibile eseguire il failback in una posizione diversa se quella originale non è disponibile. [Altre informazioni](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>Perché è necessaria una VPN o ExpressRoute per eseguire il failback?

Quando si esegue il failback da Azure, i dati di Azure vengono copiati di nuovo nella macchina virtuale locale ed è necessario l'accesso privato. 



## <a name="automation-and-scripting"></a>Automazione e scripting

### <a name="can-i-set-up-replication-with-scripting"></a>È possibile configurare la replica con lo scripting?
Sì. È possibile automatizzare i flussi di lavoro di Site Recovery usando l'API Rest, PowerShell o Azure SDK. [Altre informazioni](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Prestazioni e capacità
### <a name="can-i-throttle-replication-bandwidth"></a>È possibile limitare la larghezza di banda per la replica?
Sì. [Altre informazioni](site-recovery-plan-capacity-vmware.md).


## <a name="next-steps"></a>Passaggi successivi
* [Esaminare](vmware-physical-azure-support-matrix.md) i requisiti di supporto.
* [Configurare](vmware-azure-tutorial.md) la replica da VMware ad Azure.
