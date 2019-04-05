---
title: Domande frequenti - Ripristino di emergenza da VMware ad Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo presenta un riepilogo delle domande frequenti relative alla configurazione del ripristino di emergenza per le macchine virtuali VMware locali in Azure tramite Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 03/21/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 4237e259d1ba9cb826d89eba212b6931d933626d
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051921"
---
# <a name="common-questions---vmware-to-azure-replication"></a>Domande frequenti - Replica da VMware ad Azure

Questo articolo fornisce le risposte alle domande frequenti relative all'implementazione del ripristino di emergenza di macchine virtuali VMware locali in Azure. Eventuali domande successive alla lettura di questo articolo possono essere pubblicate nel [forum relativo ai Servizi di ripristino di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).

## <a name="general"></a>Generale
### <a name="how-is-site-recovery-priced"></a>Come viene stabilito il prezzo di Site Recovery?
Vedere [Dettagli relativi ai prezzi di Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).

### <a name="how-do-i-pay-for-azure-vms"></a>Come vengono addebitati i costi per le macchine virtuali di Azure?
Durante la replica, i dati vengono replicati in Archiviazione di Azure e non viene addebitato alcun costo per le modifiche alle macchine virtuali. Quando si esegue un failover in Azure, Site Recovery crea automaticamente le macchine virtuali IaaS di Azure. In seguito vengono fatturate le risorse di calcolo utilizzate in Azure.

### <a name="what-can-i-do-with-vmware-to-azure-replication"></a>Cosa si può fare con la replica da VMware ad Azure?
- **Ripristino di emergenza**: è possibile configurare il ripristino di emergenza completo. In questo scenario viene eseguita la replica di macchine virtuali VMware locali in Archiviazione di Azure. Quindi, se l'infrastruttura locale non è disponibile, è possibile effettuare il failover in Azure. Quando si esegue un failover, vengono create macchine virtuali di Azure con i dati replicati. È possibile accedere ad app e carichi di lavoro nelle macchine virtuali di Azure finché il data center locale non è di nuovo disponibile. Quindi si può eseguire il failback da Azure al proprio sito locale.
- **Migrazione**: è possibile usare Site Recovery per eseguire la migrazione delle macchine virtuali VMware locali in Azure. In questo scenario viene eseguita la replica di macchine virtuali VMware locali in Archiviazione di Azure. Quindi si esegue il failover dal sito locale in Azure. Dopo il failover, le app e i carichi di lavoro sono disponibili e in esecuzione all'interno di macchine virtuali di Azure.

## <a name="azure"></a>Azure
### <a name="what-do-i-need-in-azure"></a>Cosa è necessario avere in Azure?
Sono necessari una sottoscrizione di Azure, un insieme di credenziali di servizi di ripristino, un account di archiviazione della cache, i dischi gestiti e una rete virtuale. Insieme di credenziali di account di archiviazione della cache, i dischi gestiti e di rete deve trovarsi nella stessa area.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>L'account Azure deve avere le autorizzazioni per creare macchine virtuali?
L'amministratore della sottoscrizione ha le autorizzazioni di replica necessarie. Se non si ha, sono necessarie autorizzazioni per creare una VM di Azure nel gruppo di risorse e rete virtuale specificata durante la configurazione di Site Recovery e autorizzazioni di scrittura per l'account di archiviazione selezionato o gestiti disco in base alla configurazione. [Altre informazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)

### <a name="can-i-use-guest-os-server-license-on-azure"></a>È possibile usare la licenza server del sistema operativo guest in Azure?
Sì, i clienti di Microsoft Software Assurance possono usare il [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) per risparmiare sui costi di licenza per i **computer Windows Server** di cui viene eseguita la migrazione ad Azure o usare Azure per il ripristino di emergenza.

## <a name="pricing"></a>Prezzi

### <a name="how-are-licensing-charges-handled-during-replication-after-failover"></a>Come vengono gestiti i costi di licenza durante la replica, in seguito al failover?

Per altre informazioni, consultare [qui](https://aka.ms/asr_pricing_FAQ) le domande frequenti sulle licenze.

### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Come è possibile fare una stima dei costi durante l'uso di Site Recovery?

È possibile usare la funzione [Calcolatore dei prezzi](https://aka.ms/asr_pricing_calculator) per stimare i costi quando si usa Azure Site Recovery. Per la stima dettagliata sui costi, eseguire lo strumento deployment planner (https://aka.ms/siterecovery_deployment_planner) e analizzare le [report di stima dei costi](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-when-i-replicate-directly-to-managed-disk"></a>È disponibile alcuna differenza in costi quando si esegue la replica direttamente al disco gestito?

I dischi gestiti vengono addebitati leggermente diverso rispetto agli account di archiviazione. Vedere l'esempio riportato di seguito per un disco di origine della dimensione 100 GiB. L'esempio è specifico di backup differenziale del costo di archiviazione. Questo costo non include il costo per gli snapshot, l'archiviazione della cache e le transazioni.

* Account di archiviazione standard Visual Studio. Disco gestito standard HDD

    - **Disco di archiviazione con provisioning da Azure Site Recovery**: S10
    - **Account di archiviazione standard il costo addebitato utilizzato volume**: $5 al mese
    - **Disco gestito standard il costo addebitato volumi con provisioning**: $5.89 al mese

* Visual Studio l'account di archiviazione Premium. Disco gestito Premium SSD 
    - **Disco di archiviazione con provisioning da Azure Site Recovery**: P10
    - **Account di archiviazione Premium un addebito su volumi con provisioning**: $17.92 al mese
    - **Disco gestito Premium un addebito su volumi con provisioning**: $17.92 al mese

Altre informazioni, vedere [informazioni dettagliate sui prezzi dei dischi gestiti](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="do-i-incur-additional-charges-for-cache-storage-account-with-managed-disks"></a>Sono previsti addebiti aggiuntivi per l'Account di archiviazione della Cache con dischi gestiti?

No, non comportano addebiti aggiuntivi per la cache. Cache fanno sempre parte di VMware per l'architettura di Azure. Quando esegue la replica in account di archiviazione standard, questa risorsa di archiviazione della cache fa parte dello stesso account di archiviazione di destinazione.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Sono un utente di Azure Site Recovery da più di un mese. Posso ottenere ancora i primi 31 giorni gratuiti per ogni istanza protetta?

Sì, non importa da quanto tempo si usi Azure Site Recovery. Per ogni istanza protetta non vengono addebitati costi per Azure Site Recovery per i primi 31 giorni. Se, ad esempio, si è protetto 10 istanze negli ultimi 6 mesi e si connette un'undicesima istanza ad Azure Site Recovery, non verranno addebitati costi per Azure Site Recovery per l'undicesima istanza per i primi 31 giorni. Per le prime 10 istanze continuano a venire addebitati i costi per Azure Site Recovery, in quanto sono state protette per più di 31 giorni.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Durante i primi 31 giorni, vengono addebitati altri costi per Azure?

Sì, anche se Azure Site Recovery è gratuito durante i primi 31 giorni di un'istanza protetta, possono venire addebitati i costi per Archiviazione di Azure, transazioni di archiviazione e trasferimento dei dati. Per una macchina virtuale ripristinata possono venire addebitati anche i costi di calcolo di Azure.

### <a name="what-charges-do-i-incur-while-using-azure-site-recovery"></a>Quali tariffe vengono applicate quando si usa Azure Site Recovery?

Per informazioni dettagliate, vedere [domande frequenti sui costi sostenuti](https://aka.ms/asr_pricing_FAQ).

### <a name="is-there-a-cost-associated-to-perform-dr-drillstest-failover"></a>È previsto un costo per eseguire il failover di test o per le esercitazioni sul ripristino di emergenza?

Non sono previsti costi separati per le esercitazioni sul ripristino di emergenza. Saranno presenti i costi di calcolo dopo la creazione di una macchina virtuale in seguito al failover di test.

## <a name="azure-site-recovery-components-upgrade"></a>Aggiornamento dei componenti di Azure Site Recovery

### <a name="my-mobility-agentconfiguration-serverprocess-server-version-is-very-old-and-my-upgrade-has-failed-how-should-i-upgrade-to-latest-version"></a>La versione disponibile di agente di mobilità/server di configurazione/server di elaborazione è molto vecchia e l'aggiornamento non è riuscito. Come è possibile eseguire l'aggiornamento alla versione più recente?

Azure Site Recovery segue il modello di supporto N-4. Fare riferimento all'[informativa sul supporto Microsoft](https://aka.ms/asr_support_statement) per informazioni dettagliate su come eseguire l'aggiornamento da versioni molto vecchie.

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-azure-site-recovery"></a>Dove si possono trovare le note sulla versione e gli aggiornamenti cumulativi di Azure Site Recovery?

Vedere questo [documento](https://aka.ms/asr_update_rollups) per informazioni sulle note sulla versione. È possibile trovare i collegamenti per l'installazione dei rispettivi componenti in ogni aggiornamento cumulativo.

### <a name="how-should-i-upgrade-site-recovery-components-for-on-premises-vmware-or-physical-site-to-azure"></a>Come è necessario aggiornare i componenti di Site Recovery per un sito VMware o fisico locale in Azure?

Vedere le linee guida disponibili [qui](https://aka.ms/asr_vmware_upgrades) per aggiornare i componenti.

## <a name="is-reboot-of-source-machine-mandatory-for-each-upgrade"></a>È obbligatorio il riavvio della macchina virtuale di origine per ogni aggiornamento?

Anche se consigliato, non è obbligatorio per ogni aggiornamento. Vedere [qui](https://aka.ms/asr_vmware_upgrades) per linee guida dettagliate.

## <a name="on-premises"></a>Locale

### <a name="what-do-i-need-on-premises"></a>Cosa è necessario avere in locale?

In locale sono necessari:
- I componenti di Site Recovery, installati in un'unica macchina virtuale VMware.
- Un'infrastruttura VMware con almeno un host ESXi, oltre a un server vCenter come scelta consigliata.
- Una o più macchine virtuali VMware da replicare.

[Altre informazioni](vmware-azure-architecture.md) sull'architettura della replica da VMware ad Azure.

Il server di configurazione locale può essere distribuito come segue:

- È consigliabile distribuire il server di configurazione sotto forma di macchina virtuale VMware usando un modello OVA con il server di configurazione pre-installato.
- Se per qualsiasi motivo non è possibile usare un modello, è possibile installare il server di configurazione manualmente. [Altre informazioni](physical-azure-disaster-recovery.md#set-up-the-source-environment)



### <a name="where-do-on-premises-vms-replicate-to"></a>Dove viene eseguita la replica delle macchine virtuali locali?
I dati vengono replicati in Archiviazione di Azure. Quando si esegue un failover, Site Recovery crea macchine virtuali di Azure dall'account di archiviazione automaticamente o in base alla configurazione di disco gestito.

## <a name="replication"></a>Replica

### <a name="what-applications-can-i-replicate"></a>Quali applicazioni è possibile replicare?
È possibile replicare qualsiasi app o carico di lavoro in esecuzione su una macchina virtuale VMware conforme ai [requisiti di replica](vmware-physical-azure-support-matrix.md##replicated-machines). Site Recovery fornisce il supporto per la replica compatibile con l'applicazione per consentire il failover e il failback delle app a uno stato intelligente. Site Recovery si integra con le applicazioni Microsoft, ad esempio SharePoint, Exchange, Dynamics, SQL Server e Active Directory, e opera a stretto contatto con importanti fornitori, tra cui Oracle, SAP, IBM e Red Hat. [Altre informazioni](site-recovery-workload.md) sulla protezione del carico di lavoro.

### <a name="can-i-protect-a-virtual-machine-that-has-docker-disk-configuration"></a>È possibile proteggere una macchina virtuale che dispone della configurazione del disco Docker?

No, questo scenario non è supportato.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>È possibile eseguire la replica in Azure tramite una VPN da sito a sito?
Site Recovery replica i dati dal sito locale ad Archiviazione di Azure su un endpoint pubblico o mediante peering pubblico ExpressRoute. La replica su una rete VPN da sito a sito non è supportata.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>È possibile eseguire la replica in Azure con ExpressRoute?
Sì, è possibile usare ExpressRoute per replicare macchine virtuali in Azure. Site Recovery replica i dati in archiviazione di Azure su un endpoint pubblico. Per usare ExpressRoute per la replica con Site Recovery, è necessario configurare il [peering pubblico](../expressroute/expressroute-circuit-peerings.md#publicpeering) o il [peering Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). Il peering Microsoft è il dominio di routing consigliato per la replica. Verificare che i [requisiti di rete](vmware-azure-configuration-server-requirements.md#network-requirements) siano soddisfatti anche per la replica. Dopo il failover delle macchine virtuali in una rete virtuale di Azure, è possibile accedervi usando il [peering privato](../expressroute/expressroute-circuit-peerings.md#privatepeering).

### <a name="how-can-i-change-storage-account-after-machine-is-protected"></a>Come si può modificare l'account di archiviazione una volta protetto il computer?

È necessario disabilitare e abilitare la replica eseguire l'aggiornamento o downgrade il tipo di account di archiviazione.

### <a name="can-i-replicate-to-storage-accounts-for-new-machine"></a>È possibile replicare gli account di archiviazione per la nuova macchina?

No, a partire da marzo ' 19, è possibile replicare al servizio managed disks in Azure dal portale. La replica in account di archiviazione per una nuova macchina è disponibile solo tramite l'API REST e Powershell. Usare l'API versione 2016-08-10 o 2018-01-10 per la replica in account di archiviazione.

### <a name="what-are-the-benefits-in-replicating-to-managed-disks"></a>Quali sono i vantaggi inclusi la replica in dischi gestiti?

Leggere l'articolo sulla [Azure Site Recovery semplifica il ripristino di emergenza con dischi gestiti](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/).

### <a name="how-can-i-change-managed-disk-type-after-machine-is-protected"></a>Come si può modificare il tipo di disco gestito dopo aver protetto macchina?

Sì, è possibile modificare con facilità il tipo di disco gestito. [Altre informazioni](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) Tuttavia, dopo aver modificato il tipo di disco gestito, assicurarsi di attendere i punti di ripristino aggiornato da generare se è necessario eseguire il failover o il failover post questa attività.

### <a name="can-i-switch-the-replication-from-managed-disks-to-unmanaged-disks"></a>È possibile passare la replica da dischi gestiti a dischi non gestiti?

No, il passaggio da gestito a codice non gestito non è supportato.

### <a name="why-cant-i-replicate-over-vpn"></a>Perché non è possibile eseguire la replica su VPN?

Quando esegue la replica in Azure, il traffico di replica raggiunge gli endpoint pubblici di una risorsa di archiviazione di Azure, in questo modo è possibile solo eseguire la replica sulla rete internet pubblica con ExpressRoute (peering pubblico) e VPN non è supportata.

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>È possibile usare Riverbed SteelHeads per la replica?

Il nostro partner, Riverbed, include indicazioni dettagliate sull'uso di Azure Site Recovery. Vedere le [Guida alla soluzione](https://community.riverbed.com/s/article/DOC-4627).

### <a name="what-are-the-replicated-vm-requirements"></a>Quali sono i requisiti delle macchine virtuali replicate?

Per la replica è necessario che la macchina virtuale VMware esegua un sistema operativo supportato. Inoltre, la macchina virtuale deve soddisfare i requisiti relativi alle macchine virtuali di Azure. Vedere [altre informazioni](vmware-physical-azure-support-matrix.md##replicated-machines) nella matrice di supporto.

### <a name="how-often-can-i-replicate-to-azure"></a>Con quale frequenza è possibile eseguire la replica in Azure?
La replica di macchine virtuali VMware in Azure è continua.

### <a name="can-i-retain-the-ip-address-on-failover"></a>È possibile conservare l'indirizzo IP in caso di failover?
Sì, è possibile conservare l'indirizzo IP in caso di failover. Assicurarsi di precisare l'indirizzo IP di destinazione nel pannello "Calcolo e rete" prima del failover. Inoltre, assicurarsi di arrestare le macchine al momento del failover per evitare conflitti IP durante il failback.

### <a name="can-i-extend-replication"></a>È possibile estendere la replica?
No, la replica concatenata o estesa non è supportata. Richiedere questa funzionalità nel [forum dei commenti](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>È possibile eseguire una replica iniziale offline?
Questa funzionalità non è supportata. Richiedere questa funzionalità nel [forum dei commenti](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>È possibile escludere dei dischi?
Sì, è possibile escludere dischi dalla replica.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>È possibile modificare la dimensione di VM di destinazione o il tipo di macchina virtuale prima del failover?
Sì, è possibile modificare il tipo o le dimensioni della macchina virtuale qualsiasi momento prima del failover, passare a impostazioni dell'elemento di replica dal portale di calcolo e rete.

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>È possibile eseguire la replica delle macchine virtuali con i dischi dinamici?
I dischi dinamici possono essere replicati. Il disco del sistema operativo deve essere un disco di base.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Se si usano i gruppi di replica per la coerenza tra più macchine virtuali, è possibile aggiungere una nuova macchina virtuale a un gruppo di replica esistente?
Sì, è possibile aggiungere nuove macchine virtuali a un gruppo di replica esistente quando si abilita la replica per le suddette macchine. Non è possibile aggiungere una macchina virtuale a un gruppo di replica esistente dopo che la replica viene avviata e non è possibile creare un gruppo di replica per le macchine virtuali esistenti.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>È possibile modificare le macchine virtuali che eseguono la replica mediante l'aggiunta o il ridimensionamento dei dischi?

Per eseguire la replica VMware in Azure, è possibile modificare le dimensioni del disco. Se si desidera aggiungere nuovi dischi, è necessario aggiungere il disco e riabilitare la protezione per la macchina virtuale.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-without-impacting-ongoing-replication"></a>È possibile migrare nei computer locali per un nuovo Vcenter senza influire sulla replica in corso?
No, le modifiche o migrazioni al server Vcenter influiranno sulla replica in corso. È necessario configurare Azure Site Recovery con il nuovo Vcenter e abilitare la replica per le macchine.

### <a name="can-i-replicate-to-cachetarget-storage-account-which-has-a-vnet-with-azure-storage-firewalls-configured-on-it"></a>È possibile eseguire la replica in un account di archiviazione della cache/di destinazione che abbia una rete virtuale (con i firewall di archiviazione di Azure) configurata?
No, Azure Site Recovery non supporta la replica di archiviazione in una rete virtuale.

## <a name="configuration-server"></a>Server di configurazione

### <a name="what-does-the-configuration-server-do"></a>Qual è la funzione del server di configurazione?
Il server di configurazione esegue i componenti di Site Recovery locali, tra cui:
- Il server di configurazione che coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
- Il server di elaborazione che funge da gateway di replica. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia ad Archiviazione di Azure. Il server di elaborazione installa anche il servizio Mobility nelle macchine virtuali da replicare ed esegue l'individuazione automatica delle macchine virtuali VMware locali.
- Il server di destinazione master che gestisce i dati di replica durante il failback da Azure.

[Altre informazioni](vmware-azure-architecture.md) sui processi e i componenti del server di configurazione.

### <a name="where-do-i-set-up-the-configuration-server"></a>Dove si configura il server di configurazione?
Per il server di configurazione è necessaria una macchina virtuale VMware locale a disponibilità elevata.

### <a name="what-are-the-requirements-for-the-configuration-server"></a>Quali sono i requisiti del server di configurazione?

Esaminare i [prerequisiti](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>È possibile configurare manualmente il server di configurazione invece di usare un modello?
È consigliabile usare l'ultima versione del modello OVF per [creare la macchina virtuale del server di configurazione](vmware-azure-deploy-configuration-server.md). Se per qualche motivo non è possibile, ad esempio se non si ha accesso al server VMware, è possibile [scaricare il file di Installazione unificata](physical-azure-set-up-source.md) dal portale ed eseguirlo su una macchina virtuale.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Un server di configurazione può eseguire la replica in più aree?
 No. Occorre configurare un server di configurazione in ogni area.

### <a name="can-i-host-a-configuration-server-in-azure"></a>È possibile ospitare un server di configurazione in Azure?
È possibile, ma a questo scopo la macchina virtuale di Azure che esegue il server di configurazione dovrebbe comunicare con l'infrastruttura VMware locale e con le macchine virtuali. Ciò può aggiungere latenze e influire sulla replica in corso.

### <a name="how-do-i-update-the-configuration-server"></a>Come si aggiorna il server di configurazione?
[Informazioni](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) sull'aggiornamento del server di configurazione. Le informazioni più recenti sugli aggiornamenti sono disponibili nella [pagina degli aggiornamenti di Azure](https://azure.microsoft.com/updates/?product=site-recovery). È anche possibile scaricare direttamente l'ultima versione del server di configurazione dall'[Area download Microsoft](https://aka.ms/asrconfigurationserver). Se la versione è precedente di 4 versioni rispetto alla versione corrente, fare riferimento all'[informativa sul supporto](https://aka.ms/asr_support_statement) per istruzioni per l'aggiornamento.

### <a name="should-i-backup-the-deployed-configuration-server"></a>È consigliabile eseguire il backup del server di configurazione distribuito?
Si consiglia di eseguire backup pianificati regolari del server di configurazione. Affinché il failback riesca, la macchina virtuale di cui si esegue il failback deve esistere nel database del server di configurazione e il server di configurazione deve essere in esecuzione e in uno stato connesso. Atre informazioni sulle attività comuni di gestione del server di configurazione sono disponibili [qui](vmware-azure-manage-configuration-server.md).

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Durante l'impostazione del server di configurazione è possibile scaricare e installare MySQL manualmente?

Sì. Scaricare MySQL e inserirlo nella cartella **C:\Temp\ASRSetup**. Quindi installarlo manualmente. Dopo aver configurato la macchina virtuale del server di configurazione e aver accettato le condizioni, MySQL appare come **Installazione già eseguita** in **Scarica e installa**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Si può evitare di scaricare MySQL ma lasciare che venga installato da Site Recovery?

Sì. Scaricare il programma di installazione di MySQL e inserirlo nella cartella **C:\Temp\ASRSetup**.  Dopo aver configurato la macchina virtuale del server di configurazione, aver accettato le condizioni e aver fatto clic su **Scarica e installa**, il portale userà il programma di installazione aggiunto per installare MySQL.
 
### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>È possibile usare la macchina virtuale del server di configurazione per altri scopi?
No, la macchina virtuale deve essere usata solo per il server di configurazione. 

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>È possibile clonare un server di configurazione e usarlo per l'orchestrazione?
No, è necessario configurare un server di configurazione nuovo per evitare problemi di registrazione.

### <a name="can-i-change-the-vault-registered-in-the-configuration-server"></a>È possibile cambiare l'insieme di credenziali registrato nel server di configurazione?
 No. Una volta registrato con il server di configurazione, l'insieme di credenziali non può più essere cambiato. Vedere [questo articolo](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) per i passaggi necessari per ripetere la registrazione.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>È possibile usare lo stesso server di configurazione per le macchine virtuali VMware e per i server fisici?
Sì, ma tenere presente che è possibile eseguire il failback del computer fisico solo in una macchina virtuale VMware.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Dove è possibile scaricare la passphrase per il server di configurazione?
[Vedere questo articolo](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) per informazioni sul download della passphrase.

### <a name="where-can-i-download-vault-registration-keys"></a>Dove è possibile scaricare le chiavi di registrazione dell'insieme di credenziali?

Nelle **credenziali di Servizi di ripristino**, **Gestisci** > **Infrastruttura di Site Recovery** > **Server di configurazione**. In **Server** selezionare **Scarica chiave di registrazione** per scaricare il file di credenziali dell'insieme di credenziali.



## <a name="mobility-service"></a>Servizio Mobility

### <a name="where-can-i-find-the-mobility-service-installers"></a>Dove è possibile trovare i programmi di installazione del servizio Mobility?
I programmi di installazione sono nella cartella **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** nel server di configurazione.

## <a name="how-do-i-install-the-mobility-service"></a>Come si installa il servizio Mobility?
Si installa su ogni macchina virtuale da replicare usando l'[installazione push](vmware-physical-mobility-service-overview.md#push-installation) o l'[installazione manuale](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui) dall'interfaccia utente o da PowerShell. In alternativa, è possibile usare uno strumento di distribuzione, ad esempio [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md).



## <a name="security"></a>Security

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>A quale scopo Site Recovery deve accedere ai server VMware?
Site Recovery richiede l'accesso ai server VMware per:

- Configurare una macchina virtuale VMware che esegua il server di configurazione e altri componenti di Site Recovery locali. [Altre informazioni](vmware-azure-deploy-configuration-server.md)
- Individuare automaticamente le macchine virtuali per la replica. Leggere le informazioni sulla preparazione di un account per l'individuazione automatica. [Altre informazioni](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>A quale scopo Site Recovery deve accedere alle macchine virtuali VMware?

- Per eseguire la replica, il servizio Mobility di Site Recovery deve essere installato e in esecuzione su una macchina virtuale VMware. È possibile distribuire lo strumento manualmente o specificare che Site Recovery esegua un'installazione push del servizio quando si abilita la replica per una macchina virtuale. Per l'installazione push Site Recovery necessita di un account da usare per installare il componente del servizio. [Altre informazioni](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation) Questa installazione viene eseguita dal server di elaborazione, che per impostazione predefinita è in esecuzione nel server di configurazione. [Altre informazioni](vmware-azure-install-mobility-service.md) sull'installazione del servizio Mobility.
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
### <a name="can-i-use-the-process-server-at-on-premises-for-failback"></a>È possibile usare il server di elaborazione in locale per il failback?
È consigliabile creare un server di elaborazione in Azure per utilizzo generico di failback per evitare la latenza di trasferimento dei dati. Inoltre, nel caso in cui la rete VM di origine è separato con la rete di Azure con connessione al server di configurazione, quindi è essenziale usare il Server di elaborazione creato in Azure per il failback.

### <a name="how-far-back-can-i-recover"></a>Fino a quando può risalire il recupero?
Per la replica da VMware ad Azure il punto di recupero meno recente che si può usare è di 72 ore.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Come si accede alle macchine virtuali di Azure dopo il failover?
Dopo il failover, è possibile accedere alle macchine virtuali di Azure tramite una connessione Internet sicura, una connessione VPN da sito a sito o ExpressRoute di Azure. Per poter eseguire la connessione, è necessario completare una serie di operazioni. [Altre informazioni](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>I dati di cui è stato effettuato il failover sono resilienti?
Azure è progettato nell'ottica della resilienza. Site Recovery è progettato per il failover a un data center secondario di Azure in conformità con il contratto di servizio di Azure. Al momento del failover, Microsoft si assicura che i metadati e gli insiemi di credenziali rimangano nella stessa area geografica selezionata per l'insieme di credenziali.

### <a name="is-failover-automatic"></a>Il failover è automatico?
Il [failover](site-recovery-failover.md) non è automatico. Può essere avviato con singolo clic nel portale oppure è possibile usare [PowerShell](/powershell/module/az.recoveryservices) per attivare un failover.

### <a name="can-i-fail-back-to-a-different-location"></a>È possibile eseguire il failback in una posizione diversa?
Sì, se è stato effettuato il failover ad Azure, è possibile eseguire il failback in una posizione diversa se quella originale non è disponibile. [Altre informazioni](concepts-types-of-failback.md#alternate-location-recovery-alr)

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>Perché è necessaria una VPN o ExpressRoute per eseguire il failback?
Quando si esegue il failback da Azure, i dati di Azure vengono copiati di nuovo nella macchina virtuale locale ed è necessario l'accesso privato.

### <a name="can-i-resize-the-azure-vm-after-failover"></a>È possibile ridimensionare la macchina virtuale di Azure dopo il failover?
No, non è possibile modificare le dimensioni o il tipo della macchina virtuale di destinazione dopo il failover.


## <a name="automation-and-scripting"></a>Automazione e scripting

### <a name="can-i-set-up-replication-with-scripting"></a>È possibile configurare la replica con lo scripting?
Sì. È possibile automatizzare i flussi di lavoro di Site Recovery usando l'API Rest, PowerShell o Azure SDK. [Altre informazioni](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Prestazioni e capacità
### <a name="can-i-throttle-replication-bandwidth"></a>È possibile limitare la larghezza di banda per la replica?
Sì. [Altre informazioni](site-recovery-plan-capacity-vmware.md)


## <a name="next-steps"></a>Passaggi successivi
* [Esaminare](vmware-physical-azure-support-matrix.md) i requisiti di supporto.
* [Configurare](vmware-azure-tutorial.md) la replica da VMware ad Azure.
