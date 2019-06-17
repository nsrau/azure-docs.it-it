---
title: Domande frequenti su VMware per il ripristino di emergenza in Azure con Azure Site Recovery | Microsoft Docs
description: Ottenere risposte alle domande comuni sul ripristino di emergenza di macchine virtuali VMware locali in Azure usando Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 05/30/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 59be8e0585f0bedcafc868ee42f5113509c9c4ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66417767"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>Domande frequenti su VMware per la replica di Azure

Questo articolo risponde a domande comuni che potrebbero essere applicata quando si distribuisce il ripristino di emergenza di macchine virtuali di VMware in locale (VM) in Azure.

## <a name="general"></a>Generale

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>Cosa occorre per il ripristino di emergenza di VM VMware?

[Informazioni sui componenti coinvolti](vmware-azure-architecture.md) nel ripristino di emergenza di macchine virtuali VMware.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>È possibile usare Site Recovery per eseguire la migrazione di macchine virtuali VMware in Azure?

Sì. Oltre a usare Site Recovery per configurare il ripristino di emergenza completa per le macchine virtuali VMware, è anche possibile usare Site Recovery per eseguire la migrazione di macchine virtuali VMware locali in Azure. In questo scenario è replicare le macchine virtuali VMware locali in archiviazione di Azure. Quindi si esegue il failover dal sito locale in Azure. Dopo il failover, le app e i carichi di lavoro sono disponibili e in esecuzione all'interno di macchine virtuali di Azure. Il processo è simile a configurazione di ripristino di emergenza completo, ad eccezione del fatto che la migrazione non è possibile eseguire il failback da Azure.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>L'account Azure deve avere le autorizzazioni per creare macchine virtuali?

L'amministratore della sottoscrizione ha le autorizzazioni di replica necessarie. Se non si ha un amministratore, sono necessarie autorizzazioni per eseguire queste azioni:

- Creare una VM di Azure nella risorsa gruppo e virtuale di rete è di specificare quando si configura il ripristino del sito.
- Scrivere per l'account di archiviazione selezionato o un disco gestito in base alla configurazione.

[Altre informazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) sulle autorizzazioni necessarie.

### <a name="what-applications-can-i-replicate"></a>Quali applicazioni è possibile replicare?

È possibile replicare qualsiasi app o carico di lavoro in esecuzione in una VM VMware che soddisfi le [requisiti di replica](vmware-physical-azure-support-matrix.md#replicated-machines).

- Site Recovery supporta la replica compatibile con l'applicazione, in modo che le app possono essere eseguite il failover e non è stato possibile tornare a uno stato intelligente.
- Site Recovery si integra con le applicazioni Microsoft, ad esempio SharePoint, Exchange, Dynamics, SQL Server e Active Directory. Collabora strettamente con fornitori leader, tra cui Oracle, SAP, IBM e Red Hat.

[Altre informazioni](site-recovery-workload.md) sulla protezione del carico di lavoro.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>È possibile usare una licenza di server del sistema operativo guest in Azure?

Sì, è possono usare i clienti Microsoft Software Assurance [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) per risparmiare sui costi di licenza per le macchine Windows Server che vengono eseguita la migrazione ad Azure, o per usare Azure per il ripristino di emergenza.

## <a name="security"></a>Security

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Il tipo di accesso ai server VMware è necessario che Site Recovery?

Site Recovery richiede l'accesso ai server VMware per:

- Configurare una VM VMware che esegue il server di configurazione di Site Recovery.
- Individuare automaticamente le macchine virtuali per la replica.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Il tipo di accesso alle macchine virtuali VMware è necessario che Site Recovery?

- Per la replica, una VM VMware deve essere il servizio Mobility di Site Recovery installato e in esecuzione. È possibile distribuire lo strumento manualmente o è possibile specificare che Site Recovery eseguire un'installazione push del servizio quando si abilita la replica per una macchina virtuale.
- Durante la replica, le macchine virtuali comunicano con Site Recovery nel modo seguente:
    - Le macchine virtuali comunicano con il server di configurazione sulla porta HTTPS 443 per la gestione delle repliche.
    - Le macchine virtuali inviano i dati di replica al server di elaborazione sulla porta HTTPS 9443. (Questa impostazione può essere modificata).
    - Se si abilita la coerenza tra più macchine virtuali, le macchine virtuali comunicano tra loro sulla porta 20004.

### <a name="is-replication-data-sent-to-site-recovery"></a>I dati di replica vengono inviati a Site Recovery?

No, Site Recovery non intercetta i dati replicati e non dispone di informazioni su ciò che è in esecuzione nelle macchine virtuali. I dati di replica vengono scambiati tra gli hypervisor VMware e archiviazione di Azure. Site Recovery non è in grado di intercettare i dati. Al servizio Site Recovery vengono inviati solo i metadati necessari per gestire la replica e il failover.  

Site Recovery è certificato per 27018, HIPAA e DPA e certificato ISO 27001:2013. È in corso le valutazioni SOC2 e FedRAMP JAB.

## <a name="pricing"></a>Prezzi

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Modalità di calcolo approssimativi addebiti per il ripristino di emergenza di VMware

Usare la [calcolatore dei prezzi](https://aka.ms/asr_pricing_calculator) per stimare i costi quando si usa Site Recovery.

Per una stima dettagliata dei costi, eseguire lo strumento deployment planner per [VMware](https://aka.ms/siterecovery_deployment_planner) e utilizzare il [report di stima dei costi](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>È disponibile alcuna differenza nel costo tra la replica di archiviazione o direttamente al servizio managed disks?

I dischi gestiti vengono addebitati in modo leggermente diverso dall'account di archiviazione. [Altre informazioni](https://azure.microsoft.com/pricing/details/managed-disks/) sui prezzi di disco gestito.

## <a name="mobility-service"></a>Servizio Mobility

### <a name="where-can-i-find-the-mobility-service-installers"></a>Dove è possibile trovare i programmi di installazione del servizio Mobility?

I programmi di installazione si trovano nella cartella %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository nel server di configurazione.

## <a name="how-do-i-install-the-mobility-service"></a>Come si installa il servizio Mobility?

In ogni macchina virtuale che si desidera eseguire la replica, installare il servizio da uno dei diversi metodi:

- [Installazione push](vmware-physical-mobility-service-overview.md#push-installation)
- [Installazione manuale](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui) dall'interfaccia utente o PowerShell
- Distribuzione tramite uno strumento di distribuzione, ad esempio [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>Dischi gestiti

### <a name="where-does-site-recovery-replicate-data-to"></a>In Site Recovery replica i dati a?

Site Recovery replica le macchine virtuali VMware locali e server fisici in dischi gestiti in Azure.

- Il server di elaborazione di Site Recovery scrive i log di replica in un account di archiviazione della cache nell'area di destinazione.
- Questi log vengono usati per creare i punti di ripristino nei dischi gestiti di Azure che hanno prefisso **asrseeddisk**.
- Quando si verifica il failover, si seleziona il punto di ripristino consente di creare un nuovo disco gestito di destinazione. Questo disco gestito è collegato alla macchina virtuale in Azure.
- Le macchine virtuali che erano in precedenza ha replicate in un account di archiviazione (prima di marzo 2019) non sono interessate.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>È possibile replicare le macchine di nuovo agli account di archiviazione?

No. Partire da marzo 2019, nel portale di Azure, è possibile eseguire la replica solo in Azure i dischi gestiti.

Replica di nuove macchine virtuali a un account di archiviazione è disponibile solo tramite PowerShell o l'API REST (versione 2018-01-10 o 2016-08-10).

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Quali sono i vantaggi della replica al servizio managed disks?

[Informazioni su come](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery semplifica il ripristino di emergenza con dischi gestiti.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>Dopo aver protetto una macchina, si può modificare il tipo di disco gestito?

Sì, è possibile facilmente [modificare il tipo di disco gestito](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) per le repliche in corso. Prima di modificare il tipo, non verificare che alcuna firma di accesso condiviso che viene generato l'URL del disco gestito:

1. Andare alla **Managed Disks** risorsa nel portale di Azure e controllare se si dispone di un banner di URL di firma di accesso condiviso **Panoramica** pannello.
1. Se l'intestazione è presente, selezionarlo per annullare l'esportazione in corso.
1. Modificare il tipo del disco entro pochi minuti. Se si modifica il tipo di disco gestito, attendere che i punti di ripristino aggiornato deve essere generato da Azure Site Recovery.
1. Usare i nuovi punti di ripristino per qualsiasi failover di test o il failover in futuro.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>È possibile passare la replica da dischi gestiti a dischi non gestiti?

No. Passa da gestito a codice non gestito non è supportato.

## <a name="replication"></a>Replica

### <a name="what-are-the-replicated-vm-requirements"></a>Quali sono i requisiti delle macchine virtuali replicate?

[Altre informazioni](vmware-physical-azure-support-matrix.md#replicated-machines) sui requisiti di supporto per le macchine virtuali VMware e server fisici.

### <a name="how-often-can-i-replicate-to-azure"></a>Con quale frequenza è possibile eseguire la replica in Azure?

La replica di macchine virtuali VMware in Azure è continua.

### <a name="can-i-extend-replication"></a>È possibile estendere la replica?

No, la replica concatenata o estesa non è supportata. Richiedere questa funzionalità nel [forum dei commenti](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>È possibile eseguire una replica iniziale offline?

Replica offline non è supportata. Richiedere questa funzionalità nel [forum dei commenti](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="what-is-asrseeddisk"></a>Che cos'è asrseeddisk?

Per ogni disco di origine, i dati vengono replicati in un disco gestito in Azure. Questo disco ha il prefisso **asrseeddisk**. Archivia la copia del disco di origine e di tutti gli snapshot del punto di ripristino.

### <a name="can-i-exclude-disks-from-replication"></a>È possibile escludere dischi dalla replica?

Sì, è possibile escludere i dischi.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>È possibile replicare le macchine virtuali con dischi dinamici?

I dischi dinamici possono essere replicati. Il disco del sistema operativo deve essere un disco di base.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Se si usano i gruppi di replica per la coerenza tra più macchine virtuali, è possibile aggiungere una nuova macchina virtuale a un gruppo di replica esistente?

Sì, è possibile aggiungere nuove macchine virtuali a un gruppo di replica esistente quando si abilita la replica per le suddette macchine. Tuttavia:

- È possibile aggiungere una macchina virtuale a un gruppo di replica esistente dopo la replica è iniziata.
- È possibile creare un gruppo di replica per le macchine virtuali esistenti.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>È possibile modificare le macchine virtuali che eseguono la replica mediante l'aggiunta o il ridimensionamento dei dischi?

Per la replica VMware in Azure, è possibile modificare le dimensioni del disco. Se si desidera aggiungere nuovi dischi, è necessario aggiungere il disco e riabilitare la protezione per la macchina virtuale.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>È possibile migrare le macchine locali di un nuovo vCenter Server senza influire sulla replica in corso?

No. Una modifica di VMware Vcenter o migrazione influirà sulla replica in corso. Configurare Site Recovery con il nuovo vCenter Server e abilitare nuovamente la replica per le macchine.

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>È possibile replicare in un account di archiviazione della cache o di destinazione che sia configurata su di esso una rete virtuale (con i firewall di Azure)?

No, Site Recovery non supporta la replica in archiviazione di Azure in reti virtuali.

## <a name="component-upgrade"></a>Aggiornamento del componente

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>La versione del server di configurazione o l'agente di servizi Mobility è precedente, e l'aggiornamento non riuscito. Cosa devo fare?

Site Recovery segue il modello di supporto n-4. [Altre informazioni](https://aka.ms/asr_support_statement) su come eseguire l'aggiornamento da versioni molto obsolete.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>In cui è possibile trovare le note sulla versione e aggiornamenti cumulativi per Azure Site Recovery?

[Informazioni sui nuovi aggiornamenti](site-recovery-whats-new.md), e [ottenere informazioni rollup](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Dove posso reperire informazioni sull'aggiornamento per il ripristino di emergenza in Azure?

[Informazioni sull'aggiornamento](https://aka.ms/asr_vmware_upgrades).

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>È necessario riavviare il computer di origine per ogni aggiornamento?

Riavviare il computer è consigliata ma non obbligatorio per ogni aggiornamento. [Altre informazioni](https://aka.ms/asr_vmware_upgrades)

## <a name="configuration-server"></a>Server di configurazione

### <a name="what-does-the-configuration-server-do"></a>Qual è la funzione del server di configurazione?

Il server di configurazione esegue i componenti di Site Recovery locali, tra cui:

- Il server di configurazione stesso. Il server coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
- Il server di elaborazione, che funge da gateway di replica. Questo server:
    1. Riceve i dati di replica.
    2. Consente di ottimizzare i dati mediante memorizzazione nella cache, compressione e crittografia.
    3. Invia i dati in archiviazione di Azure.
  Il server di elaborazione, inoltre, non un'installazione push del servizio Mobility nelle macchine virtuali ed esegue l'individuazione automatica delle macchine virtuali VMware locali.
- Il server di destinazione master, che gestisce i dati di replica durante il failback da Azure.

[Altre informazioni](vmware-azure-architecture.md) sui processi e i componenti del server di configurazione.

### <a name="where-do-i-set-up-the-configuration-server"></a>Dove si configura il server di configurazione?

Una macchina virtuale VMware locale singolo e a disponibilità elevata, è necessario per il server di configurazione. Per il ripristino di emergenza di server fisici, installare il server di configurazione di un computer fisico.

### <a name="what-do-i-need-for-the-configuration-server"></a>Cosa occorre per il server di configurazione?

Esaminare i [prerequisiti](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>È possibile configurare manualmente il server di configurazione invece di usare un modello?

È consigliabile che si [creare la VM del server di configurazione](vmware-azure-deploy-configuration-server.md) usando la versione più recente del modello di macchina virtuale formato OVF (Open). Se non è possibile utilizzare il modello (ad esempio, se non si ha accesso al server VMware), [scaricare](physical-azure-set-up-source.md) il file di installazione dal portale e configurare il server di configurazione.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Un server di configurazione può eseguire la replica in più aree?

No. Per la replica in più aree, è necessario un server di configurazione in ogni area.

### <a name="can-i-host-a-configuration-server-in-azure"></a>È possibile ospitare un server di configurazione in Azure?

Sebbene sia possibile, la macchina virtuale di Azure che esegue il server di configurazione necessario comunicare con l'infrastruttura VMware locale e le VM. Questa comunicazione aggiunge latenza e influisce sulla replica in corso.

### <a name="how-do-i-update-the-configuration-server"></a>Come si aggiorna il server di configurazione?

[Informazioni su](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) come aggiornare il server di configurazione.

- È possibile trovare le informazioni sugli aggiornamenti più recenti sul [pagina degli aggiornamenti di Azure](https://azure.microsoft.com/updates/?product=site-recovery).
- È possibile scaricare la versione più recente dal portale. In alternativa, è possibile scaricare la versione più recente del server di configurazione direttamente dai [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Se la versione è precedente alla versione corrente di più di quattro versioni, vedere la [supporta istruzione](https://aka.ms/asr_support_statement) per indicazioni sull'aggiornamento.

### <a name="should-i-back-up-the-configuration-server"></a>È consigliabile eseguire il backup del server di configurazione?

Si consiglia di eseguire backup pianificati regolari del server di configurazione.

- Per il failback ha esito positivo, la macchina virtuale viene eseguito il failback deve esistere nel database del server di configurazione.
- Il server di configurazione deve essere in esecuzione e in uno stato connesso.
- [Altre informazioni](vmware-azure-manage-configuration-server.md) sulle attività di gestione di server di configurazione comuni.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Durante l'impostazione del server di configurazione è possibile scaricare e installare MySQL manualmente?

Sì. Scaricare MySQL e inserirlo nella cartella C:\Temp\ASRSetup. Quindi, installarlo manualmente. Dopo aver configurato la macchina virtuale del server di configurazione e aver accettato le condizioni, MySQL appare come **Installazione già eseguita** in **Scarica e installa**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Si può evitare di scaricare MySQL ma lasciare che venga installato da Site Recovery?

Sì. Scaricare il programma di installazione di MySQL e inserirlo nella cartella C:\Temp\ASRSetup. Quando si configura il server di configurazione della macchina virtuale, accettare le condizioni e selezionare **scaricare e installare**. Il portale userà il programma di installazione è stato aggiunto per installare MySQL.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>È possibile usare la macchina virtuale del server di configurazione per altri scopi?

No. Usare la macchina virtuale solo per il server di configurazione.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>È possibile clonare un server di configurazione e usarlo per l'orchestrazione?

No. Configurare un server di configurazione aggiornati per evitare problemi di registrazione.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>È possibile modificare l'insieme di credenziali in cui è registrato il server di configurazione?

No. Dopo un insieme di credenziali è associato il server di configurazione, non può essere modificata. [Informazioni su](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) sulla registrazione di un server di configurazione con un insieme di credenziali diverso.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>È possibile usare lo stesso server di configurazione per il ripristino di emergenza di macchine virtuali VMware e server fisici?

Sì, ma tenere presente tale computer fisico può essere sottoposto il failback solo in una VM VMware.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Dove è possibile scaricare la passphrase per il server di configurazione?

[Informazioni su](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) come scaricare la passphrase.

### <a name="where-can-i-download-vault-registration-keys"></a>Dove è possibile scaricare le chiavi di registrazione dell'insieme di credenziali?

Nell'insieme di credenziali di servizi di ripristino, selezionare **server di configurazione** nelle **infrastruttura di Site Recovery** > **Gestisci**. Quindi, nella **i server**, selezionare **Scarica chiave di registrazione** per scaricare il file delle credenziali dell'insieme di credenziali.

## <a name="process-server"></a>Server di elaborazione

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Perché non riesco a selezionare il server di elaborazione quando abilita la replica?

Gli aggiornamenti in versioni 9,24 e versioni successive ora visualizza i [integrità del server di elaborazione quando si abilita la replica](vmware-azure-enable-replication.md#enable-replication). Questa funzionalità consente di evitare la limitazione del server di elaborazione e ridurre al minimo l'uso di server di elaborazione non integro.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Come si aggiorna il server di elaborazione alla versione 9.24 o versione successiva per informazioni sull'integrità accurati?

A partire [versione 9,24](service-updates-how-to.md#links-to-currently-supported-update-rollups), più avvisi sono stati aggiunti per indicare l'integrità del server di elaborazione. [Aggiornare i componenti di Site Recovery per 9.24 o versioni successive] (service-updates-how-to.md#links-to-currently-supported-update-rollups) in modo che tutti gli avvisi vengono generati.

## <a name="failover-and-failback"></a>Failover e failback

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>È possibile usare il server di elaborazione locale per il failback?

È fortemente consigliabile creare un server di elaborazione in Azure per motivi di failback, per evitare la latenza di trasferimento dei dati. Inoltre, nel caso in cui la rete VM di origine è separato con la rete di Azure con connessione nel server di configurazione, è essenziale usare il server di elaborazione creato in Azure per il failback.

### <a name="can-i-keep-the-ip-address-on-failover"></a>È possibile mantenere l'indirizzo IP in caso di failover?

Sì, è possibile mantenere l'indirizzo IP in caso di failover. Assicurarsi di specificare l'indirizzo IP di destinazione nel **calcolo e rete** le impostazioni per la macchina virtuale prima del failover. Inoltre, arrestare le macchine al momento del failover per evitare conflitti di indirizzi IP durante il failback.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>È possibile modificare la dimensione di VM di destinazione o il tipo di macchina virtuale prima del failover?

Sì, è possibile modificare il tipo o le dimensioni della macchina virtuale in qualsiasi momento prima del failover. Nel portale, usare il **calcolo e rete** le impostazioni per la macchina virtuale replicata.

### <a name="how-far-back-can-i-recover"></a>Fino a quando può risalire il recupero?

Per VMware in Azure, il punto di ripristino meno recente, che è possibile utilizzare è 72 ore.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Come si accede alle macchine virtuali di Azure dopo il failover?

Dopo il failover, è possibile accedere macchine virtuali di Azure tramite una connessione internet sicura, tramite una VPN site-to-site o ExpressRoute di Azure. Per connettersi, è necessario preparare diverse operazioni. [Altre informazioni](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>I dati sottoposta a failover sono resilienti?

Azure è progettato nell'ottica della resilienza. Site Recovery è progettato per il failover in un Data Center Azure secondario, come richiesto dal contratto di Azure a livello di servizio (SLA). Quando si verifica il failover, si assicura che i metadati e gli insiemi di credenziali rimangano nella stessa area geografica selezionata per l'insieme di credenziali.

### <a name="is-failover-automatic"></a>Il failover è automatico?

Il [failover](site-recovery-failover.md) non è automatico. Si avvia un failover effettuando una selezione singola nel portale oppure è possibile usare [PowerShell](/powershell/module/az.recoveryservices) per attivare un failover.

### <a name="can-i-fail-back-to-a-different-location"></a>È possibile eseguire il failback in una posizione diversa?

Sì. Se il failover in Azure, è possibile eseguire il failback una posizione diversa se quella originale non è disponibile. [Altre informazioni](concepts-types-of-failback.md#alternate-location-recovery-alr)

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Il motivo per cui è necessario una VPN o ExpressRoute con peering privato per eseguire il failback?

Quando si esegue il failback da Azure, i dati da Azure vengono copiati nuovamente alla macchina virtuale in locale e l'accesso privato è obbligatorio.

### <a name="can-i-resize-the-azure-vm-after-failover"></a>È possibile ridimensionare la macchina virtuale di Azure dopo il failover?

No, è possibile modificare le dimensioni o il tipo di macchina virtuale di destinazione dopo il failover.

## <a name="automation-and-scripting"></a>Automazione e scripting

### <a name="can-i-set-up-replication-with-scripting"></a>È possibile configurare la replica con lo scripting?

Sì. È possibile automatizzare i flussi di lavoro di Site Recovery usando l'API Rest, PowerShell o il SDK di Azure. [Altre informazioni](vmware-azure-disaster-recovery-powershell.md)

## <a name="performance-and-capacity"></a>Prestazioni e capacità

### <a name="can-i-throttle-replication-bandwidth"></a>È possibile limitare la larghezza di banda per la replica?

Sì. [Altre informazioni](site-recovery-plan-capacity-vmware.md)

## <a name="next-steps"></a>Passaggi successivi

- [Esaminare](vmware-physical-azure-support-matrix.md) i requisiti di supporto.
- [Configurare](vmware-azure-tutorial.md) la replica da VMware ad Azure.
