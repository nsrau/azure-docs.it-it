---
title: Domande frequenti - Ripristino di emergenza da VMware ad Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo riepiloga il ripristino di emergenza fir con domande comuni delle macchine virtuali VMware locali in Azure usando Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 04/26/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 22d3bdf8c60e6682c360395b44fe6f1dcc1207b0
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925526"
---
# <a name="common-questions---vmware-to-azure-replication"></a>Domande frequenti - Replica da VMware ad Azure

Questo articolo fornisce le risposte alle domande più comuni quando si distribuisce il ripristino di emergenza di macchine virtuali VMware locali in Azure. 

## <a name="general"></a>Generale 
### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>Cosa occorre per il ripristino di emergenza di VM VMware?

[Informazioni su](vmware-azure-architecture.md) i componenti coinvolti nel ripristino di emergenza di macchine virtuali VMware. 

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>È possibile usare Site Recovery per eseguire la migrazione di macchine virtuali VMware in Azure?

Sì, oltre a usare Site Recovery per configurare il ripristino di emergenza completa per le macchine virtuali VMware, è possibile usare anche Site Recovery per eseguire la migrazione di macchine virtuali VMware locali in Azure. In questo scenario viene eseguita la replica di macchine virtuali VMware locali in Archiviazione di Azure. Quindi si esegue il failover dal sito locale in Azure. Dopo il failover, le app e i carichi di lavoro sono disponibili e in esecuzione all'interno di macchine virtuali di Azure. Il processo è simile alla configurazione di ripristino di emergenza completo, ad eccezione del fatto che la migrazione non è possibile eseguire il failback da Azure.


### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>L'account Azure deve avere le autorizzazioni per creare macchine virtuali?
L'amministratore della sottoscrizione ha le autorizzazioni di replica necessarie. Se non si ha, sono necessarie autorizzazioni per creare una VM di Azure nel gruppo di risorse e rete virtuale specificata durante la configurazione di Site Recovery e autorizzazioni di scrittura per l'account di archiviazione selezionato o gestiti disco in base alla configurazione. [Altre informazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)

### <a name="what-applications-can-i-replicate"></a>Quali applicazioni è possibile replicare?
È possibile replicare qualsiasi app o carico di lavoro in esecuzione su una macchina virtuale VMware conforme ai [requisiti di replica](vmware-physical-azure-support-matrix.md##replicated-machines).
- Site Recovery fornisce il supporto per la replica compatibile con l'applicazione per consentire il failover e il failback delle app a uno stato intelligente.
- Site Recovery si integra con le applicazioni Microsoft, ad esempio SharePoint, Exchange, Dynamics, SQL Server e Active Directory, e opera a stretto contatto con importanti fornitori, tra cui Oracle, SAP, IBM e Red Hat.
- [Altre informazioni](site-recovery-workload.md) sulla protezione del carico di lavoro.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>È possibile usare una licenza di server del sistema operativo guest in Azure?
Sì, i clienti di Microsoft Software Assurance possono usare il [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) per risparmiare sui costi di licenza per i **computer Windows Server** di cui viene eseguita la migrazione ad Azure o usare Azure per il ripristino di emergenza.

## <a name="security"></a>Security

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>A quale scopo Site Recovery deve accedere ai server VMware?
Site Recovery richiede l'accesso ai server VMware per:

- Configurare una VM VMware in esecuzione il server di configurazione di Site Recovery,
- Individuare automaticamente le macchine virtuali per la replica. 


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>A quale scopo Site Recovery deve accedere alle macchine virtuali VMware?

- Per eseguire la replica, il servizio Mobility di Site Recovery deve essere installato e in esecuzione su una macchina virtuale VMware. È possibile distribuire lo strumento manualmente o specificare che Site Recovery esegua un'installazione push del servizio quando si abilita la replica per una macchina virtuale. 
- Durante la replica, le macchine virtuali comunicano con Site Recovery nel modo seguente:
    - Le macchine virtuali comunicano con il server di configurazione sulla porta HTTPS 443 per la gestione delle repliche.
    - Le macchine virtuali inviano i dati di replica al server di elaborazione sulla porta HTTPS 9443 (può essere modificata).
    - Se si abilita la coerenza tra più macchine virtuali, le macchine virtuali comunicano tra loro sulla porta 20004.


### <a name="is-replication-data-sent-to-site-recovery"></a>I dati di replica vengono inviati a Site Recovery?
No, Site Recovery non intercetta i dati replicati né raccoglie informazioni su ciò che è in esecuzione sulle macchine virtuali. I dati di replica vengono scambiati tra gli hypervisor VMware e Archiviazione di Azure. Site Recovery non è in grado di intercettare i dati. Al servizio Site Recovery vengono inviati solo i metadati necessari per gestire la replica e il failover.  

Site Recovery è certificato ISO 27001:2013, 27018, HIPAA e DPA e le valutazioni SOC2 e FedRAMP JAB sono in fase di completamento.


## <a name="pricing"></a>Prezzi
### <a name="how-can-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Come è possibile calcolare i costi approssimativi per il ripristino di emergenza di VMware?

È possibile usare la [calcolatore dei prezzi](https://aka.ms/asr_pricing_calculator) per stimare i costi quando si usa Site Recovery.

Per la stima dettagliata sui costi, eseguire lo strumento deployment planner per [VMware](https://aka.ms/siterecovery_deployment_planner)e utilizzare il [report di stima dei costi](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>È disponibile alcuna differenza nel costo tra la replica di archiviazione o direttamente al servizio managed disks?

I dischi gestiti vengono addebitati leggermente diverso rispetto agli account di archiviazione. [Altre informazioni](https://azure.microsoft.com/pricing/details/managed-disks/) sui prezzi di disco gestito.

## <a name="mobility-service"></a>Servizio Mobility

### <a name="where-can-i-find-the-mobility-service-installers"></a>Dove è possibile trovare i programmi di installazione del servizio Mobility?
Inclusi tra i programmi di installazione di **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** cartella nel server di configurazione.

## <a name="how-do-i-install-the-mobility-service"></a>Come si installa il servizio Mobility?
Installare in ogni macchina virtuale da replicare, usando una serie di metodi:
- [Installazione push](vmware-physical-mobility-service-overview.md#push-installation)
- [Installazione manuale](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui) dall'interfaccia utente o Powershell.
- Distribuzione tramite uno strumento di distribuzione, ad esempio [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md).

## <a name="managed-disks"></a>Dischi gestiti

### <a name="where-does-site-recovery-replicate-data-to"></a>In Site Recovery replica i dati a?

Site Recovery replica le macchine virtuali VMware locali e server fisici in dischi gestiti in Azure.
- Il server di elaborazione di Site Recovery scrive i log di replica in un account di archiviazione della cache nell'area di destinazione.
- Questi log vengono utilizzati per creare i punti di ripristino in Azure managed disks che hanno prefisso asrseeddisk.
- Quando si verifica il failover, si seleziona il punto di ripristino consente di creare un nuovo disco gestito di destinazione. Questo disco gestito è collegato alla macchina virtuale in Azure.
- Le macchine virtuali che erano in precedenza ha replicate in un account di archiviazione (prima di marzo 2019) non sono interessate.


### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>È possibile replicare le macchine di nuovo agli account di archiviazione?

No, a partire da marzo 2019, il portale, è possibile eseguire la replica solo in Azure i dischi gestiti. 

Replica di nuove macchine virtuali a un account di archiviazione è disponibile solo tramite PowerShell o l'API REST (versione 2018-01-10 o 2016-08-10).

### <a name="what-are-the-benefits-in-replicating-to-managed-disks"></a>Quali sono i vantaggi inclusi la replica in dischi gestiti?

[Informazioni su come](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery semplifica il ripristino di emergenza con dischi gestiti.


### <a name="can-i-change-the-managed-disk-type-after-machine-is-protected"></a>Dopo la protezione di computer, si può modificare il tipo di disco gestito?

Sì, è possibile facilmente [modificare il tipo di disco gestito](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) per le repliche in corso. Prima di modificare il tipo, assicurarsi che non venga generato alcun URL di firma di accesso condiviso del disco gestito. Passare alla risorsa disco gestito nel portale di Azure e verificare la presenza di un banner di URL di firma di accesso condiviso nel pannello Panoramica. Se è presente, fare clic per annullare l'esportazione in corso. Al termine, modificare il tipo del disco entro pochi minuti. Tuttavia, se si modifica il tipo di disco gestito, attendere i punti di ripristino aggiornato deve essere generato da Azure Site Recovery. Usare i nuovi punti di ripristino per qualsiasi failover di test o il failover in futuro.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>È possibile passare la replica da dischi gestiti a dischi non gestiti?

No, il passaggio da gestito a codice non gestito non è supportato.

## <a name="replication"></a>Replica


### <a name="what-are-the-replicated-vm-requirements"></a>Quali sono i requisiti delle macchine virtuali replicate?

[Altre informazioni](vmware-physical-azure-support-matrix.md##replicated-machines) sui requisiti del server fisico/VM VMware e il supporto.

### <a name="how-often-can-i-replicate-to-azure"></a>Con quale frequenza è possibile eseguire la replica in Azure?
La replica di macchine virtuali VMware in Azure è continua.


### <a name="can-i-extend-replication"></a>È possibile estendere la replica?
No, la replica concatenata o estesa non è supportata. Richiedere questa funzionalità nel [forum dei commenti](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>È possibile eseguire una replica iniziale offline?
Questa funzionalità non è supportata. Richiedere questa funzionalità nel [forum dei commenti](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).


### <a name="what-is-asrseeddisk"></a>Che cos'è asrseeddisk?
Per ogni disco di origine, i dati vengono replicati in un disco gestito in Azure. Questo disco ha il prefisso asrseeddisk. Archivia la copia del disco di origine e di tutti gli snapshot del punto di ripristino.

### <a name="can-i-exclude-disks-from-replication"></a>È possibile escludere dischi dalla replica?
Sì, è possibile escludere i dischi.

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>È possibile eseguire la replica delle macchine virtuali con i dischi dinamici?
I dischi dinamici possono essere replicati. Il disco del sistema operativo deve essere un disco di base.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Se si usano i gruppi di replica per la coerenza tra più macchine virtuali, è possibile aggiungere una nuova macchina virtuale a un gruppo di replica esistente?
Sì, è possibile aggiungere nuove macchine virtuali a un gruppo di replica esistente quando si abilita la replica per le suddette macchine.
- Dopo aver avviata la replica, è possibile aggiungere una macchina virtuale a un gruppo di replica esistente.
- È possibile creare un gruppo di replica per le macchine virtuali esistenti.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>È possibile modificare le macchine virtuali che eseguono la replica mediante l'aggiunta o il ridimensionamento dei dischi?

Per eseguire la replica VMware in Azure, è possibile modificare le dimensioni del disco. Se si desidera aggiungere nuovi dischi, è necessario aggiungere il disco e riabilitare la protezione per la macchina virtuale.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>È possibile migrare le macchine locali di un nuovo vCenter Server senza influire sulla replica in corso?
No, le modifiche o migrazioni al server Vcenter influiranno sulla replica in corso. È necessario configurare il ripristino del sito con il nuovo vCenter Server e abilitare nuovamente la replica per le macchine.

### <a name="can-i-replicate-to-a-cachetarget-storage-account-which-has-a-vnet-with-azure-storage-firewalls-configured-on-it"></a>È possibile eseguire la replica a un account di archiviazione della cache e la destinazione che dispone di una rete virtuale (con i firewall di archiviazione di Azure) configurata su di esso?
No, Site Recovery non supporta la replica all'archiviazione in una rete virtuale.





## <a name="component-upgrade"></a>Aggiornamento del componente

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>La versione del server di configurazione o l'agente di servizi Mobility è precedente e l'aggiornamento non riuscito. Cosa devo fare?  

Site Recovery segue il modello di supporto n-4. [Altre informazioni](https://aka.ms/asr_support_statement) su come eseguire l'aggiornamento da versioni molto obsolete.

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-azure-site-recovery"></a>Dove si possono trovare le note sulla versione e gli aggiornamenti cumulativi di Azure Site Recovery?

[Scopri](site-recovery-whats-new.md) sui nuovi aggiornamenti, e [ottenere informazioni rollup](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Dove posso reperire informazioni sull'aggiornamento per il ripristino di emergenza in Azure?

[Informazioni su](https://aka.ms/asr_vmware_upgrades) l'aggiornamento.

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>È necessario riavviare il computer di origine per ogni aggiornamento?

Anche se consigliato, non è obbligatorio per ogni aggiornamento. [Altre informazioni](https://aka.ms/asr_vmware_upgrades)


## <a name="configuration-server"></a>Server di configurazione

### <a name="what-does-the-configuration-server-do"></a>Qual è la funzione del server di configurazione?

Il server di configurazione esegue i componenti di Site Recovery locali, tra cui:
- Il server di configurazione stessa che coordina le comunicazioni tra origini locali e Azure e gestisce la replica dei dati.
- Il server di elaborazione che funge da gateway di replica. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia alla risorsa di archiviazione di Azure. Il server di elaborazione anche un'installazione push del servizio Mobility nelle macchine virtuali, esegue e l'individuazione automatica delle macchine virtuali VMware locali.
- Il server di destinazione master che gestisce i dati di replica durante il failback da Azure.

[Altre informazioni](vmware-azure-architecture.md) sui processi e i componenti del server di configurazione.

### <a name="where-do-i-set-up-the-configuration-server"></a>Dove si configura il server di configurazione?
Per il server di configurazione è necessaria una macchina virtuale VMware locale a disponibilità elevata. Per il ripristino di emergenza di server fisici, è possibile installare il server di configurazione di un computer fisico.

### <a name="what-do-i-need-for-the-configuration-server"></a>Cosa occorre per il server di configurazione?

Esaminare i [prerequisiti](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>È possibile configurare manualmente il server di configurazione invece di usare un modello?
È consigliabile che si [creare la VM del server di configurazione](vmware-azure-deploy-configuration-server.md) con la versione più recente del modello OVF. Se per qualche motivo non è possibile, ad esempio si ha accesso al server VMware, è possibile [scaricare](physical-azure-set-up-source.md) il file di installazione dal portale e configurare il server di configurazione.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Un server di configurazione può eseguire la replica in più aree?
 No. A tale scopo, è necessario un server di configurazione in ogni area.

### <a name="can-i-host-a-configuration-server-in-azure"></a>È possibile ospitare un server di configurazione in Azure?
È possibile, ma a questo scopo la macchina virtuale di Azure che esegue il server di configurazione dovrebbe comunicare con l'infrastruttura VMware locale e con le macchine virtuali. Questo aggiunge la latenza e influisce sulla replica in corso.

### <a name="how-do-i-update-the-configuration-server"></a>Come si aggiorna il server di configurazione?

[Informazioni su](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) come aggiornare i server di configurazione.
- È possibile trovare le informazioni sugli aggiornamenti più recenti sul [pagina degli aggiornamenti di Azure](https://azure.microsoft.com/updates/?product=site-recovery).
- È possibile scaricare la versione più recente dal portale. In alternativa, è possibile scaricare direttamente la versione più recente del server di configurazione dal [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Se la versione è precedente alla versione corrente di più di quattro versioni, fare riferimento a nostro [supporta istruzione](https://aka.ms/asr_support_statement) per indicazioni sull'aggiornamento.

### <a name="should-i-back-up-the-configuration-server"></a>È consigliabile eseguire il backup del server di configurazione?
Si consiglia di eseguire backup pianificati regolari del server di configurazione.
- Per il failback ha esito positivo, la macchina virtuale viene eseguito il failback deve esistere nel database del server di configurazione.
- Il server di configurazione deve essere in esecuzione e in uno stato connesso.
- [Altre informazioni](vmware-azure-manage-configuration-server.md) sulle attività di gestione di server di configurazione comuni.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Durante l'impostazione del server di configurazione è possibile scaricare e installare MySQL manualmente?

Sì. Scaricare MySQL e inserirlo nella cartella **C:\Temp\ASRSetup**. Quindi installarlo manualmente. Dopo aver configurato la macchina virtuale del server di configurazione e aver accettato le condizioni, MySQL appare come **Installazione già eseguita** in **Scarica e installa**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Si può evitare di scaricare MySQL ma lasciare che venga installato da Site Recovery?

Sì. Scaricare il programma di installazione di MySQL e inserirlo nella cartella **C:\Temp\ASRSetup**.  Quando si configura il server di configurazione della macchina virtuale, accettare le condizioni e fare clic su **scaricare e installare**. Il portale userà il programma di installazione è stato aggiunto per installare MySQL.
 
### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>È possibile usare la macchina virtuale del server di configurazione per altri scopi?
No, la macchina virtuale deve essere usata solo per il server di configurazione. 

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>È possibile clonare un server di configurazione e usarlo per l'orchestrazione?
No, deve configurare un server di configurazione aggiornati per evitare problemi di registrazione.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>È possibile modificare l'insieme di credenziali in cui è registrato il server di configurazione?
 No. Dopo un insieme di credenziali è associato il server di configurazione, non può essere modificata. Revisione [questo articolo](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) per apprendere la ripetizione della registrazione.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>È possibile usare lo stesso server di configurazione per le macchine virtuali VMware e per i server fisici?
Sì, ma tenere presente che tale computer fisico può essere eseguito solo il failback in una VM VMware.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Dove è possibile scaricare la passphrase per il server di configurazione?
[Informazioni su come](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) scaricare la passphrase.

### <a name="where-can-i-download-vault-registration-keys"></a>Dove è possibile scaricare le chiavi di registrazione dell'insieme di credenziali?

Nell'insieme di credenziali di servizi di ripristino, fare clic su **server di configurazione** nelle **infrastruttura di Site Recovery** > **Gestisci**. Quindi nella **i server**, selezionare **Scarica chiave di registrazione** per scaricare il file delle credenziali dell'insieme di credenziali.

## <a name="process-server"></a>Server di elaborazione

### <a name="unable-to-select-process-server-during-enable-replication"></a>Non è possibile selezionare server di elaborazione durante l'abilitazione della replica

Da versione 9.24, miglioramenti hanno scopo di fornire [elaborare avvisi server](vmware-physical-azure-monitor-process-server.md#process-server-alerts) sui casi in cui configurare un server di elaborazione scale-out. Si tratta di evitare la limitazione delle richieste di processo server ed evitare l'utilizzo del server di elaborazione non integro.

### <a name="what-should-i-do-to-obtain-accurate-health-status-of-process-server"></a>Cosa devo fare per ottenere lo stato di integrità accurata del server di elaborazione?

Aggiornare i componenti di Site Recovery per la [versioni più recenti](service-updates-how-to.md#links-to-currently-supported-update-rollups) (almeno 9.24 o versione successiva).

## <a name="failover-and-failback"></a>Failover e failback
### <a name="can-i-use-the-process-server-at-on-premises-for-failback"></a>È possibile usare il server di elaborazione in locale per il failback?
È consigliabile creare un server di elaborazione in Azure per motivi di failback, per evitare la latenza di trasferimento dei dati. Inoltre, nel caso in cui la rete VM di origine è separato con la rete di Azure con connessione nel server di configurazione, è essenziale usare il server di elaborazione creato in Azure per il failback.

### <a name="can-i-retain-the-ip-address-on-failover"></a>È possibile conservare l'indirizzo IP in caso di failover?
Sì, è possibile conservare l'indirizzo IP in caso di failover. Assicurarsi di specificare l'indirizzo IP di destinazione nelle impostazioni 'Calcolo e rete' per la macchina virtuale prima del failover. Inoltre, arrestare le macchine al momento del failover per evitare conflitti di indirizzi IP durante il failback.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>È possibile modificare la dimensione di VM di destinazione o il tipo di macchina virtuale prima del failover?
Sì, è possibile modificare il tipo o le dimensioni della macchina virtuale in qualsiasi momento prima del failover alle impostazioni calcolo e rete della macchina virtuale replicata, nel portale.

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
