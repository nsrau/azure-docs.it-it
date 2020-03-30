---
title: Domande comuni sul ripristino di emergenza di VMware con Azure Site Recovery
description: Risposte alle domande comuni sul ripristino di emergenza delle macchine virtuali VMware locali in Azure tramite Azure Site Recovery.Get answers to common questions about disaster recovery of on-premises VMware VMs to Azure by using Azure Site Recovery.
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: ae16138ae44262f53a8f9948d6287f0acf621244
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240033"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>Domande frequenti sulla replica da VMware ad Azure

Questo articolo risponde alle domande comuni che potrebbero venire fuori quando si distribuisce il ripristino di emergenza delle macchine virtuali VMware locali (VM) in Azure.This article answers common questions that might come up when you deploy disaster recovery of on-premises VMware virtual machines (VMs) to Azure.

## <a name="general"></a>Generale

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>Di cosa ho bisogno per il ripristino di emergenza della macchina virtuale VMware?

[Informazioni sui componenti coinvolti](vmware-azure-architecture.md) nel ripristino di emergenza delle macchine virtuali VMware.Learn about the components involved in disaster recovery of VMware VMs.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>È possibile usare Site Recovery per eseguire la migrazione delle macchine virtuali VMware in Azure?

Sì. In addition to using Site Recovery to set up full disaster recovery for VMware VMs, you can also use Site Recovery to migrate on-premises VMware VMs to Azure. In this scenario, you replicate on-premises VMware VMs to Azure Storage. Quindi si esegue il failover dal sito locale in Azure. Dopo il failover, le app e i carichi di lavoro sono disponibili e in esecuzione all'interno di macchine virtuali di Azure. Il processo è simile all'impostazione del ripristino di emergenza completo, ad eccezione del fatto che in una migrazione non è possibile eseguire il failback da Azure.The process is like setting up full disaster recovery, except that in a migration you can't fail back from Azure.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>L'account Azure deve avere le autorizzazioni per creare macchine virtuali?

L'amministratore della sottoscrizione ha le autorizzazioni di replica necessarie. Se non sei un amministratore, devi disporre delle autorizzazioni per eseguire queste azioni:

- Creare una macchina virtuale di Azure nel gruppo di risorse e nella rete virtuale specificati durante la configurazione di Ripristino sbriga.
- Scrivere nell'account di archiviazione o nel disco gestito selezionato in base alla configurazione.

[Ulteriori informazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) sulle autorizzazioni necessarie.

### <a name="what-applications-can-i-replicate"></a>Quali applicazioni è possibile replicare?

È possibile replicare qualsiasi app o carico di lavoro in esecuzione in una macchina virtuale VMware che soddisfa i requisiti di [replica.](vmware-physical-azure-support-matrix.md#replicated-machines)

- Site Recovery supporta la replica in grado di riconoscere le applicazioni, in modo che le app possano essere eseguito il failover e il failback a uno stato intelligente.
- Site Recovery si integra con le applicazioni Microsoft come SharePoint, Exchange, Dynamics, SQL Server e Active Directory. Lavora inoltre a stretto contatto con i principali fornitori, tra cui Oracle, SAP, IBM e Red Hat.

[Altre informazioni](site-recovery-workload.md) sulla protezione del carico di lavoro.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>È possibile usare una licenza server del sistema operativo guest in Azure?

Sì, i clienti di Microsoft Software Assurance possono usare il [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) per risparmiare sui costi di licenza per i computer Windows Server di cui viene eseguita la migrazione ad Azure o usare Azure per il ripristino di emergenza.

## <a name="security"></a>Security

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Di quale accesso ai server VMware è necessario Site Recovery?

Site Recovery richiede l'accesso ai server VMware per:

- Configurare una macchina virtuale VMware che esegue il server di configurazione di Site Recovery.Set up a VMware VM running the Site Recovery configuration server.
- Individuare automaticamente le macchine virtuali per la replica.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Di quale accesso alle macchine virtuali VMware è necessario?

- Per eseguire la replica, è necessario che in una macchina virtuale VMware sia installato e in esecuzione il servizio Site Recovery Mobility. È possibile distribuire lo strumento manualmente oppure specificare che Site Recovery eseri un'installazione push del servizio quando si abilita la replica per una macchina virtuale.
- Durante la replica, le macchine virtuali comunicano con Site Recovery nel modo seguente:
    - Le macchine virtuali comunicano con il server di configurazione sulla porta HTTPS 443 per la gestione della replica.
    - Le macchine virtuali inviano i dati di replica al server di elaborazione sulla porta HTTPS 9443. (Questa impostazione può essere modificata.)
    - Se si abilita la coerenza tra più macchine virtuali, le macchine virtuali comunicano tra loro sulla porta 20004.

### <a name="is-replication-data-sent-to-site-recovery"></a>I dati di replica vengono inviati a Site Recovery?

No, Site Recovery non intercetta i dati replicati e non dispone di informazioni su ciò che è in esecuzione nelle macchine virtuali. I dati di replica vengono scambiati tra gli hypervisor VMware e Archiviazione di Azure.Replication data is exchanged between VMware hypervisors and Azure Storage. Site Recovery non è in grado di intercettare i dati. Al servizio Site Recovery vengono inviati solo i metadati necessari per gestire la replica e il failover.

Site Recovery è certificato per ISO 27001:2013 e 27018, HIPAA e DPA. È in procinto di valutazioni SOC2 e FedRAMP JAB.

## <a name="pricing"></a>Prezzi

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Come si calcolano i costi approssimativi per il ripristino di emergenza di VMware?

Utilizzare il [calcolatore](https://aka.ms/asr_pricing_calculator) dei prezzi per stimare i costi durante l'utilizzo di Site Recovery.

Per una stima dettagliata dei costi, eseguire lo strumento di pianificazione della distribuzione per [VMware](https://aka.ms/siterecovery_deployment_planner) e utilizzare il report di [stima dei costi](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>C'è qualche differenza di costo tra la replica nell'archiviazione o direttamente ai dischi gestiti?

I dischi gestiti vengono addebitati in modo leggermente diverso dagli account di archiviazione. [Ulteriori informazioni](https://azure.microsoft.com/pricing/details/managed-disks/) sui prezzi dei dischi gestiti.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>C'è qualche differenza di costo durante la replica all'account di archiviazione General Purpose v2?

In genere si noterà un aumento del costo delle transazioni sostenuto negli account di archiviazione GPv2 poiché Azure Site Recovery è pesante per le transazioni. [Per saperne](../storage/common/storage-account-upgrade.md#pricing-and-billing) di più per stimare la modifica.

## <a name="mobility-service"></a>Servizio Mobility

### <a name="where-can-i-find-the-mobility-service-installers"></a>Dove è possibile trovare i programmi di installazione del servizio Mobility?

I programmi di installazione si trovano nella cartella %ProgramData%

## <a name="how-do-i-install-the-mobility-service"></a>Come si installa il servizio Mobility?

In ogni macchina virtuale che si vuole replicare, installare il servizio in uno dei diversi metodi:On each VM that you want to replicate, install the service by one of several methods:

- [Installazione push](vmware-physical-mobility-service-overview.md#push-installation)
- [Installazione manuale](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui) dall'interfaccia utente o da PowerShellManual installation from the UI or PowerShell
- Distribuzione tramite uno strumento di distribuzione come [Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>Dischi gestiti

### <a name="where-does-site-recovery-replicate-data-to"></a>Dove vengono replicati i dati di Site Recovery?

Site Recovery replicates on-premises VMware VMs and physical servers to managed disks in Azure.

- Il server di processo di Site Recovery scrive i log di replica in un account di archiviazione della cache nell'area di destinazione.
- Questi log vengono usati per creare punti di ripristino in dischi gestiti da Azure con prefisso **asrseeddisk**.
- Quando si verifica il failover, il punto di ripristino selezionato viene utilizzato per creare un nuovo disco gestito di destinazione. Questo disco gestito è collegato alla macchina virtuale in Azure.This managed disk is attached to the VM in Azure.
- Le macchine virtuali precedentemente replicate in un account di archiviazione (prima di marzo 2019) non sono interessate.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>È possibile replicare nuovi computer in account di archiviazione?

No. A partire da marzo 2019, nel portale di Azure, è possibile replicare solo i dischi gestiti di Azure.Beginning 2 2019, in the Azure portal, you can replicate only to Azure managed disks.

La replica delle nuove macchine virtuali in un account di archiviazione è disponibile solo tramite PowerShell o l'API REST (versione 2018-01-10 o 2016-08-10).

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Quali sono i vantaggi della replica su dischi gestiti?

[Scopri come](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery semplifica il ripristino di emergenza con i dischi gestiti.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>È possibile modificare il tipo di disco gestito dopo la protezione di un computer?

Sì, è possibile modificare facilmente [il tipo di disco gestito](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) per le repliche in corso. Prima di modificare il tipo, verificare che sul disco gestito non venga generato alcun URL di firma di accesso condiviso:

1. Passare alla risorsa **Disco gestito** nel portale di Azure e verificare se nel pannello **Panoramica** è possibile verificare se è necessario un banner URL della firma di accesso condiviso.
1. Se il banner è presente, selezionarlo per annullare l'esportazione in corso.
1. Modificare il tipo di disco nei minuti successivi. Se si modifica il tipo di disco gestito, attendere che nuovi punti di ripristino vengano generati da Azure Site Recovery.If you change the managed-disk type, wait for fresh recovery points be generated by Azure Site Recovery.
1. Usare i nuovi punti di ripristino per qualsiasi failover di test o failover in futuro.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>È possibile passare la replica da dischi gestiti a dischi non gestiti?

No. Il passaggio da gestito a non gestito non è supportato.

## <a name="replication"></a>Replica

### <a name="what-are-the-replicated-vm-requirements"></a>Quali sono i requisiti delle macchine virtuali replicate?

[Ulteriori informazioni](vmware-physical-azure-support-matrix.md#replicated-machines) sui requisiti di supporto per le macchine virtuali VMware e i server fisici.

### <a name="how-often-can-i-replicate-to-azure"></a>Con quale frequenza è possibile eseguire la replica in Azure?

La replica di macchine virtuali VMware in Azure è continua.

### <a name="can-i-extend-replication"></a>È possibile estendere la replica?

No, la replica concatenata o estesa non è supportata. Richiedere questa funzionalità nel [forum dei commenti](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>È possibile eseguire una replica iniziale offline?

La replica offline non è supportata. Richiedere questa funzionalità nel [forum dei commenti](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="what-is-asrseeddisk"></a>Che cos'è asrseeddisk?

Per ogni disco di origine, i dati vengono replicati su un disco gestito in Azure.For every source disk, data is replicated to a managed disk in Azure. Questo disco ha il prefisso **asrseeddisk**. Memorizza la copia del disco di origine e tutti gli snapshot del punto di ripristino.

### <a name="can-i-exclude-disks-from-replication"></a>È possibile escludere i dischi dalla replica?

Sì, è possibile escludere i dischi.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>È possibile replicare le macchine virtuali con dischi dinamici?

I dischi dinamici possono essere replicati. Il disco del sistema operativo deve essere un disco di base.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Se si usano i gruppi di replica per la coerenza tra più macchine virtuali, è possibile aggiungere una nuova macchina virtuale a un gruppo di replica esistente?

Sì, è possibile aggiungere nuove macchine virtuali a un gruppo di replica esistente quando si abilita la replica per le suddette macchine. Tuttavia:

- Non è possibile aggiungere una macchina virtuale a un gruppo di replica esistente dopo l'inizio della replica.
- Non è possibile creare un gruppo di replica per le macchine virtuali esistenti.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>È possibile modificare le macchine virtuali che eseguono la replica mediante l'aggiunta o il ridimensionamento dei dischi?

Per la replica VMware in Azure, è possibile modificare le dimensioni del disco delle macchine virtuali di origine. Se si desidera aggiungere nuovi dischi, è necessario aggiungere il disco e riattivare la protezione per la macchina virtuale.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>È possibile eseguire la migrazione dei computer locali a un nuovo server vCenter senza influire sulla replica in corso?

No. Una modifica di VMware Vcenter o la migrazione avrà un impatto sulla replica in corso. Configurare Site Recovery con il nuovo vCenter Server e abilitare nuovamente la replica per i computer.

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>È possibile replicare in una cache o in un account di archiviazione di destinazione con una rete virtuale (con Firewall di Azure) configurata?

No, Site Recovery non supporta la replica in Archiviazione di Azure nelle reti virtuali.

## <a name="component-upgrade"></a>Aggiornamento dei componenti

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>La versione dell'agente o del server di configurazione dei servizi Mobility è vecchia e l'aggiornamento non è riuscito. Cosa devo fare?

Site Recovery segue il modello di supporto N-4. [Ulteriori informazioni](https://aka.ms/asr_support_statement) su come eseguire l'aggiornamento da versioni molto vecchie.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Dove è possibile trovare le note sulla versione e gli aggiornamenti cumulativi per Azure Site Recovery?

[Informazioni sui nuovi aggiornamenti](site-recovery-whats-new.md)e informazioni sul [rollup](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Dove è possibile trovare le informazioni sull'aggiornamento per il ripristino di emergenza in Azure?

[Ulteriori informazioni sull'aggiornamento](https://aka.ms/asr_vmware_upgrades).

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>È necessario riavviare i computer di origine per ogni aggiornamento?

Un riavvio è consigliato ma non obbligatorio per ogni aggiornamento. [Scopri di più](https://aka.ms/asr_vmware_upgrades).

## <a name="configuration-server"></a>Server di configurazione

### <a name="what-does-the-configuration-server-do"></a>Qual è la funzione del server di configurazione?

Il server di configurazione esegue i componenti di Site Recovery locali, tra cui:

- Il server di configurazione stesso. Il server coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
- Server di elaborazione, che funge da gateway di replica. Questo server:
    1. Riceve i dati di replica.
    2. Consente di ottimizzare i dati mediante memorizzazione nella cache, compressione e crittografia.
    3. Invia i dati ad Archiviazione di Azure.Sends the data to Azure Storage.
  Il server di elaborazione esegue anche un'installazione push del servizio Mobility nelle macchine virtuali ed esegue l'individuazione automatica delle macchine virtuali VMware locali.
- Il server di destinazione master, che gestisce i dati di replica durante il failback da Azure.The master target server, which handles replication data during failback from Azure.

[Altre informazioni](vmware-azure-architecture.md) sui processi e i componenti del server di configurazione.

### <a name="where-do-i-set-up-the-configuration-server"></a>Dove si configura il server di configurazione?

È necessaria una singola macchina virtuale VMware locale a disponibilità elevata per il server di configurazione. Per il ripristino di emergenza del server fisico, installare il server di configurazione in un computer fisico.

### <a name="what-do-i-need-for-the-configuration-server"></a>Di cosa ho bisogno per il server di configurazione?

Esaminare i [prerequisiti](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>È possibile configurare manualmente il server di configurazione invece di usare un modello?

È consigliabile [creare la macchina virtuale del server](vmware-azure-deploy-configuration-server.md) di configurazione usando la versione più recente del modello Open Virtualization Format (OVF). Se non è possibile utilizzare il modello (ad esempio, se non si ha accesso al server VMware), [scaricare](physical-azure-set-up-source.md) il file di installazione dal portale e configurare il server di configurazione.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Un server di configurazione può eseguire la replica in più aree?

No. Per eseguire la replica in più aree, è necessario un server di configurazione in ogni area.

### <a name="can-i-host-a-configuration-server-in-azure"></a>È possibile ospitare un server di configurazione in Azure?

Sebbene sia possibile, la macchina virtuale di Azure che esegue il server di configurazione dovrebbe comunicare con l'infrastruttura VMware locale e le macchine virtuali. Questa comunicazione aggiunge latenza e influisce sulla replica in corso.

### <a name="how-do-i-update-the-configuration-server"></a>Come si aggiorna il server di configurazione?

[Informazioni su](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) come aggiornare il server di configurazione.

- Le informazioni più recenti sull'aggiornamento sono disponibili nella [pagina Aggiornamenti](https://azure.microsoft.com/updates/?product=site-recovery)di Azure .
- È possibile scaricare la versione più recente dal portale. In alternativa, è possibile scaricare la versione più recente del server di configurazione direttamente [dall'Area download Microsoft](https://aka.ms/asrconfigurationserver).
- Se la versione in uso è più di quattro versioni precedenti alla versione corrente, vedere [l'istruzione](https://aka.ms/asr_support_statement) di supporto per istruzioni sull'aggiornamento.

### <a name="should-i-back-up-the-configuration-server"></a>È necessario eseguire il backup del server di configurazione?

Si consiglia di eseguire backup pianificati regolari del server di configurazione.

- Per un failback riuscito, il failback della macchina virtuale in eseguito il failover deve esistere nel database del server di configurazione.
- Il server di configurazione deve essere in esecuzione e in uno stato connesso.
- [Ulteriori informazioni](vmware-azure-manage-configuration-server.md) sulle attività di gestione comuni del server di configurazione.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Durante l'impostazione del server di configurazione è possibile scaricare e installare MySQL manualmente?

Sì. Scaricare MySQL e inserirlo nella cartella C:\Temp\ASRSetup. Quindi, installarlo manualmente. Dopo aver configurato la macchina virtuale del server di configurazione e aver accettato le condizioni, MySQL appare come **Installazione già eseguita** in **Scarica e installa**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Si può evitare di scaricare MySQL ma lasciare che venga installato da Site Recovery?

Sì. Scaricare il programma di installazione di MySQL e inserirlo nella cartella C:\Temp\ASRSetup. Quando si configura la macchina virtuale del server di configurazione, accettare i termini e selezionare **Scarica e installa**. Il portale utilizzerà il programma di installazione aggiunto per installare MySQL.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>È possibile usare la macchina virtuale del server di configurazione per altri scopi?

No. Usare la macchina virtuale solo per il server di configurazione.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>È possibile clonare un server di configurazione e usarlo per l'orchestrazione?

No. Configurare un nuovo server di configurazione per evitare problemi di registrazione.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>È possibile modificare l'insieme di credenziali in cui è registrato il server di configurazione?

No. Dopo che un vault è associato al server di configurazione, non può essere modificato. [Informazioni](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) sulla registrazione di un server di configurazione con un vault diverso.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>È possibile usare lo stesso server di configurazione per il ripristino di emergenza delle macchine virtuali VMware e dei server fisici?

Sì, ma si noti che è possibile eseguire il failback del computer fisico solo in una macchina virtuale VMware.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Dove è possibile scaricare la passphrase per il server di configurazione?

[Scopri](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) come scaricare la passphrase.

### <a name="where-can-i-download-vault-registration-keys"></a>Dove è possibile scaricare le chiavi di registrazione dell'insieme di credenziali?

Nell'insieme di credenziali di Servizi di ripristino selezionare **Server di configurazione** in**Gestione** **infrastruttura** > di Site Recovery . Quindi, in **Server**, selezionare **Scarica chiave** di registrazione per scaricare il file delle credenziali dell'insieme di credenziali.

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>È possibile utilizzare un singolo server di configurazione per proteggere più istanze di vCenter?

Sì, un singolo server di configurazione può proteggere le macchine virtuali in più vCenter.  Non esiste un limite al numero di istanze di vCenter che possono essere aggiunte al server di configurazione, tuttavia si applicano i limiti per il numero di macchine virtuali che un singolo server di configurazione può proteggere.

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>Un singolo server di configurazione può proteggere più cluster all'interno di vCenter?

Sì, Azure Site Recovery può proteggere le macchine virtuali in cluster diversi.

## <a name="process-server"></a>Server di elaborazione

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Perché non è possibile selezionare il server di elaborazione quando si abilita la replica?

Gli aggiornamenti nelle versioni 9.24 e successive ora visualizzano l'integrità del server di [elaborazione quando si abilita la replica](vmware-azure-enable-replication.md#enable-replication). Questa funzionalità consente di evitare la limitazione dei server di processo e di ridurre al minimo l'utilizzo di server di elaborazione non integri.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Come si aggiorna il server di elaborazione alla versione 9.24 o successiva per ottenere informazioni accurate sull'integrità?

A partire dalla [versione 9.24](service-updates-how-to.md#links-to-currently-supported-update-rollups), sono stati aggiunti altri avvisi per indicare l'integrità del server di elaborazione. [Aggiornare i componenti di Site Recovery alla versione 9.24 o successiva](service-updates-how-to.md#links-to-currently-supported-update-rollups) in modo che vengano generati tutti gli avvisi.

### <a name="how-can-i-ensure-high-availability-of-the-process-server"></a>Come posso garantire un'elevata disponibilità del server di elaborazione?

Configurando più di un server di elaborazione, la progettazione offre la flessibilità necessaria per spostare i computer protetti da un server di elaborazione non integro a un server di elaborazione funzionante. Lo spostamento di una macchina da un server di elaborazione a un altro deve essere avviato in modo esplicito/manualmente tramite i passaggi definiti qui: spostamento di [macchine virtuali tra server di elaborazione](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

## <a name="failover-and-failback"></a>Failover e failback

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>È possibile utilizzare il server di elaborazione locale per il failback?

È consigliabile creare un server di elaborazione in Azure a scopo di failback, per evitare latenze di trasferimento dei dati. Inoltre, nel caso in cui sia stata separata la rete di macchine virtuali di origine con la rete con connessione di Azure nel server di configurazione, è essenziale usare il server di elaborazione creato in Azure per il failback.

### <a name="can-i-keep-the-ip-address-on-failover"></a>È possibile mantenere l'indirizzo IP in caso di failover?

Sì, è possibile mantenere l'indirizzo IP in caso di failover. Assicurarsi di specificare l'indirizzo IP di destinazione nelle impostazioni **di calcolo e rete** per la macchina virtuale prima del failover. Inoltre, arrestare i computer al momento del failover per evitare conflitti di indirizzi IP durante il failback.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>È possibile modificare le dimensioni della macchina virtuale o il tipo di macchina virtuale di destinazione prima del failover?

Sì, è possibile modificare il tipo o le dimensioni della macchina virtuale in qualsiasi momento prima del failover. Nel portale usare le impostazioni **di calcolo e rete** per la macchina virtuale replicata.

### <a name="how-far-back-can-i-recover"></a>Fino a quando può risalire il recupero?

Per VMware in Azure, il punto di ripristino meno recente che è possibile usare è 72 ore.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Come si accede alle macchine virtuali di Azure dopo il failover?

Dopo il failover, è possibile accedere alle macchine virtuali di Azure tramite una connessione Internet sicura, tramite una VPN da sito a sito o tramite Azure ExpressRoute.After failover, you can access Azure VMs over a secure internet connection, over a site-to-site VPN, or over Azure ExpressRoute. Per connettersi, è necessario preparare diverse cose. [Scopri di più](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>I dati con failover sono resilienti?

Azure è progettato nell'ottica della resilienza. Site Recovery viene progettato per il failover in un data center secondario di Azure, come richiesto dal contratto di servizio (SLA) di Azure.Site Recovery is engineered for failover to a secondary Azure datacenter, as required by the Azure service-level agreement (SLA). Quando si verifica il failover, ci assicuriamo che i metadati e gli insiemi di credenziali rimangano nella stessa area geografica scelta per l'insieme di credenziali.

### <a name="is-failover-automatic"></a>Il failover è automatico?

[Il failover](site-recovery-failover.md) non è automatico. Per avviare un failover, effettuare una singola selezione nel portale oppure usare [PowerShell](/powershell/module/az.recoveryservices) per attivare un failover.

### <a name="can-i-fail-back-to-a-different-location"></a>È possibile eseguire il failback in una posizione diversa?

Sì. Se è stato eseguito il failover in Azure, è possibile eseguire il failback in un percorso diverso se quello originale non è disponibile. [Scopri di più](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Perché è necessaria una VPN o ExpressRoute con peering privato per eseguire il failback?

Quando si esegue il failback da Azure, i dati di Azure vengono copiati nella macchina virtuale locale ed è necessario l'accesso privato.


## <a name="automation-and-scripting"></a>Automazione e scripting

### <a name="can-i-set-up-replication-with-scripting"></a>È possibile configurare la replica con lo scripting?

Sì. È possibile automatizzare i flussi di lavoro di Site Recovery usando l'API Rest, PowerShell o Azure SDK. [Scopri di più](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Prestazioni e capacità

### <a name="can-i-throttle-replication-bandwidth"></a>È possibile limitare la larghezza di banda per la replica?

Sì. [Scopri di più](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>Passaggi successivi

- [Esaminare](vmware-physical-azure-support-matrix.md) i requisiti di supporto.
- [Configurare](vmware-azure-tutorial.md) la replica da VMware ad Azure.
