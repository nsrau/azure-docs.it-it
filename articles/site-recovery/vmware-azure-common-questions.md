---
title: Domande comuni sul ripristino di emergenza da VMware ad Azure con Azure Site Recovery
description: Per ottenere risposte alle domande comuni sul ripristino di emergenza di macchine virtuali VMware locali in Azure, usare Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 10/29/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: f09acb0110f436e7af936d79da9db1bab4ea23a9
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053688"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>Domande frequenti sulla replica da VMware ad Azure

Questo articolo risponde a domande comuni che potrebbero emergere quando si distribuisce il ripristino di emergenza di macchine virtuali VMware locali in Azure.

## <a name="general"></a>Informazioni di carattere generale

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>Cosa è necessario per il ripristino di emergenza delle macchine virtuali VMware?

Informazioni [sui componenti necessari per il](vmware-azure-architecture.md) ripristino di emergenza di macchine virtuali VMware.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>È possibile usare Site Recovery per eseguire la migrazione di macchine virtuali VMware in Azure?

Sì. Oltre a usare Site Recovery per configurare il ripristino di emergenza completo per le macchine virtuali VMware, è anche possibile usare Site Recovery per eseguire la migrazione di macchine virtuali VMware locali in Azure. In questo scenario viene eseguita la replica di macchine virtuali VMware locali in archiviazione di Azure. Quindi si esegue il failover dal sito locale in Azure. Dopo il failover, le app e i carichi di lavoro sono disponibili e in esecuzione all'interno di macchine virtuali di Azure. Il processo è simile alla configurazione del ripristino di emergenza completo, ad eccezione del fatto che in una migrazione non è possibile eseguire il failback da Azure.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>L'account Azure deve avere le autorizzazioni per creare macchine virtuali?

L'amministratore della sottoscrizione ha le autorizzazioni di replica necessarie. Se non si è un amministratore, è necessario disporre delle autorizzazioni per eseguire le operazioni seguenti:

- Creare una macchina virtuale di Azure nel gruppo di risorse e nella rete virtuale specificata quando si configura Site Recovery.
- Consente di scrivere nell'account di archiviazione o nel disco gestito selezionato in base alla configurazione.

[Altre](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) informazioni sulle autorizzazioni necessarie.

### <a name="what-applications-can-i-replicate"></a>Quali applicazioni è possibile replicare?

È possibile replicare qualsiasi app o carico di lavoro in esecuzione in una macchina virtuale VMware che soddisfi i [requisiti di replica](vmware-physical-azure-support-matrix.md#replicated-machines).

- Site Recovery supporta la replica compatibile con l'applicazione, in modo che sia possibile eseguire il failover e il failback delle app in uno stato intelligente.
- Site Recovery si integra con le applicazioni Microsoft, ad esempio SharePoint, Exchange, Dynamics, SQL Server e Active Directory. Funziona inoltre a stretto contatto con fornitori leader, tra cui Oracle, SAP, IBM e Red Hat.

[Altre informazioni](site-recovery-workload.md) sulla protezione del carico di lavoro.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>È possibile usare una licenza del server del sistema operativo guest in Azure?

Sì, i clienti di Microsoft Software Assurance possono usare [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) per risparmiare sui costi di licenza per i computer Windows Server di cui viene eseguita la migrazione in Azure o per usare Azure per il ripristino di emergenza.

## <a name="security"></a>Sicurezza

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Quale accesso ai server VMware Site Recovery necessario?

Site Recovery richiede l'accesso ai server VMware per:

- Configurare una macchina virtuale VMware che esegue il server di configurazione Site Recovery.
- Individuare automaticamente le macchine virtuali per la replica.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Quale accesso alle macchine virtuali VMware Site Recovery necessario?

- Per eseguire la replica, è necessario che in una macchina virtuale VMware sia installato e in esecuzione il servizio Mobility Site Recovery. È possibile distribuire lo strumento manualmente oppure è possibile specificare che Site Recovery eseguire un'installazione push del servizio quando si Abilita la replica per una macchina virtuale.
- Durante la replica, le macchine virtuali comunicano con Site Recovery nel modo seguente:
    - Le macchine virtuali comunicano con il server di configurazione sulla porta HTTPS 443 per la gestione della replica.
    - Le macchine virtuali inviano i dati di replica al server di elaborazione sulla porta HTTPS 9443. Questa impostazione può essere modificata.
    - Se si abilita la coerenza tra più macchine virtuali, le macchine virtuali comunicano tra loro sulla porta 20004.

### <a name="is-replication-data-sent-to-site-recovery"></a>I dati di replica vengono inviati a Site Recovery?

No, Site Recovery non intercetta i dati replicati e non contiene informazioni su ciò che è in esecuzione nelle macchine virtuali. I dati di replica vengono scambiati tra hypervisor VMware e archiviazione di Azure. Site Recovery non è in grado di intercettare i dati. Al servizio Site Recovery vengono inviati solo i metadati necessari per gestire la replica e il failover.  

Site Recovery è certificata per ISO 27001:2013 e 27018, HIPAA e DPA. È in fase di valutazione di SOC2 e FedRAMP JAB.

## <a name="pricing"></a>Prezzi

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Ricerca per categorie calcolare le tariffe approssimative per il ripristino di emergenza di VMware?

Utilizzare il [calcolatore dei prezzi](https://aka.ms/asr_pricing_calculator) per stimare i costi durante l'utilizzo di Site Recovery.

Per una stima dettagliata dei costi, eseguire lo strumento di pianificazione della distribuzione per [VMware](https://aka.ms/siterecovery_deployment_planner) e utilizzare il [report di stima dei costi](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Esiste una differenza di costi tra la replica in archiviazione o direttamente nei dischi gestiti?

I dischi gestiti vengono addebitati in modo leggermente diverso rispetto agli account di archiviazione. [Scopri di più](https://azure.microsoft.com/pricing/details/managed-disks/) sui prezzi dei dischi gestiti.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Esiste una differenza di costi durante la replica nell'account di archiviazione per utilizzo generico V2?

Viene in genere visualizzato un aumento del costo delle transazioni per gli account di archiviazione GPv2, poiché Azure Site Recovery le transazioni sono pesanti. [Altre](../storage/common/storage-account-upgrade.md#pricing-and-billing) informazioni per stimare la modifica.

## <a name="mobility-service"></a>Servizio Mobility

### <a name="where-can-i-find-the-mobility-service-installers"></a>Dove è possibile trovare i programmi di installazione del servizio Mobility?

I programmi di installazione si trovano nella cartella%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository nel server di configurazione.

## <a name="how-do-i-install-the-mobility-service"></a>Come si installa il servizio Mobility?

In ogni macchina virtuale che si vuole replicare installare il servizio con uno dei diversi metodi seguenti:

- [Installazione push](vmware-physical-mobility-service-overview.md#push-installation)
- [Installazione manuale](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui) dall'interfaccia utente o da PowerShell
- Distribuzione tramite uno strumento di distribuzione come [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>Managed Disks

### <a name="where-does-site-recovery-replicate-data-to"></a>Dove Site Recovery replicare i dati?

Site Recovery replica le VM VMware locali e i server fisici in dischi gestiti in Azure.

- Il server di elaborazione Site Recovery scrive i log di replica in un account di archiviazione della cache nell'area di destinazione.
- Questi log vengono usati per creare punti di ripristino nei dischi gestiti di Azure con prefisso **asrseeddisk**.
- Quando si verifica il failover, il punto di ripristino selezionato viene usato per creare un nuovo disco gestito di destinazione. Questo disco gestito è collegato alla macchina virtuale in Azure.
- Le macchine virtuali replicate in precedenza in un account di archiviazione (prima del 2019 marzo) non sono interessate.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>È possibile replicare nuovi computer in account di archiviazione?

No. A partire da marzo 2019, nella portale di Azure, è possibile eseguire la replica solo in Azure Managed Disks.

La replica di nuove macchine virtuali in un account di archiviazione è disponibile solo tramite PowerShell o l'API REST (versione 2018-01-10 o 2016-08-10).

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Quali sono i vantaggi della replica nei dischi gestiti?

[Scopri come](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery semplifica il ripristino di emergenza con Managed Disks.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>È possibile modificare il tipo di disco gestito dopo che un computer è stato protetto?

Sì, è possibile [modificare facilmente il tipo di disco gestito per le](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) repliche in corso. Prima di modificare il tipo, verificare che nel disco gestito non venga generato alcun URL di firma di accesso condiviso:

1. Passare alla risorsa **disco gestito** nella portale di Azure e verificare se è presente un banner URL di firma di accesso condiviso nel pannello **Panoramica** .
1. Se il banner è presente, selezionarlo per annullare l'esportazione in corso.
1. Modificare il tipo di disco entro i prossimi minuti. Se si modifica il tipo di disco gestito, attendere che i punti di ripristino nuovi vengano generati da Azure Site Recovery.
1. Usare i nuovi punti di ripristino per eventuali failover di test o failover in futuro.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>È possibile cambiare la replica da dischi gestiti a dischi non gestiti?

No. Il cambio da gestito a non gestito non è supportato.

## <a name="replication"></a>Replica

### <a name="what-are-the-replicated-vm-requirements"></a>Quali sono i requisiti delle macchine virtuali replicate?

[Altre](vmware-physical-azure-support-matrix.md#replicated-machines) informazioni sui requisiti di supporto per le macchine virtuali VMware e i server fisici.

### <a name="how-often-can-i-replicate-to-azure"></a>Con quale frequenza è possibile eseguire la replica in Azure?

La replica di macchine virtuali VMware in Azure è continua.

### <a name="can-i-extend-replication"></a>È possibile estendere la replica?

No, la replica concatenata o estesa non è supportata. Richiedere questa funzionalità nel [forum dei commenti](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>È possibile eseguire una replica iniziale offline?

La replica offline non è supportata. Richiedere questa funzionalità nel [forum dei commenti](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="what-is-asrseeddisk"></a>Che cos'è asrseeddisk?

Per ogni disco di origine, i dati vengono replicati in un disco gestito in Azure. Questo disco ha il prefisso **asrseeddisk**. Archivia la copia del disco di origine e di tutti gli snapshot dei punti di ripristino.

### <a name="can-i-exclude-disks-from-replication"></a>È possibile escludere I dischi dalla replica?

Sì, è possibile escludere i dischi.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>È possibile replicare le macchine virtuali con dischi dinamici?

I dischi dinamici possono essere replicati. Il disco del sistema operativo deve essere un disco di base.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Se si usano i gruppi di replica per la coerenza tra più macchine virtuali, è possibile aggiungere una nuova macchina virtuale a un gruppo di replica esistente?

Sì, è possibile aggiungere nuove macchine virtuali a un gruppo di replica esistente quando si abilita la replica per le suddette macchine. Tuttavia

- Non è possibile aggiungere una macchina virtuale a un gruppo di replica esistente dopo che la replica è iniziata.
- Non è possibile creare un gruppo di replica per le macchine virtuali esistenti.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>È possibile modificare le macchine virtuali che eseguono la replica mediante l'aggiunta o il ridimensionamento dei dischi?

Per la replica VMware in Azure, è possibile modificare le dimensioni del disco. Se si desidera aggiungere nuovi dischi, è necessario aggiungere il disco e riabilitare la protezione per la macchina virtuale.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>È possibile eseguire la migrazione di computer locali a un nuovo server vCenter senza influisca sulla replica in corso?

No. Una modifica di VMware vCenter o della migrazione influirà sulla replica in corso. Configurare Site Recovery con la nuova server vCenter e abilitare di nuovo la replica per le macchine virtuali.

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>È possibile eseguire la replica in una cache o in un account di archiviazione di destinazione in cui è configurata una rete virtuale (con i firewall di Azure)?

No, Site Recovery non supporta la replica nell'archiviazione di Azure nelle reti virtuali.

## <a name="component-upgrade"></a>Aggiornamento componenti

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>La versione dell'agente di servizi Mobility o del server di configurazione è obsoleta e l'aggiornamento non è riuscito. Che cosa occorre fare?

Site Recovery segue il modello di supporto N-4. [Altre](https://aka.ms/asr_support_statement) informazioni su come eseguire l'aggiornamento da versioni molto obsolete.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Dove è possibile trovare le note sulla versione e gli aggiornamenti cumulativi per Azure Site Recovery?

[Scopri di più sui nuovi aggiornamenti](site-recovery-whats-new.md)e [Ottieni informazioni di rollup](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Dove è possibile trovare informazioni sull'aggiornamento per il ripristino di emergenza in Azure?

Informazioni [sull'aggiornamento di](https://aka.ms/asr_vmware_upgrades).

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>È necessario riavviare I computer di origine per ogni aggiornamento?

Un riavvio è consigliato ma non obbligatorio per ogni aggiornamento. [Altre informazioni](https://aka.ms/asr_vmware_upgrades).

## <a name="configuration-server"></a>Server di configurazione

### <a name="what-does-the-configuration-server-do"></a>Qual è la funzione del server di configurazione?

Il server di configurazione esegue i componenti di Site Recovery locali, tra cui:

- Il server di configurazione. Il server coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
- Il server di elaborazione, che funge da gateway di replica. Questo server:
    1. Riceve i dati di replica.
    2. Consente di ottimizzare i dati mediante memorizzazione nella cache, compressione e crittografia.
    3. Invia i dati ad archiviazione di Azure.
  Il server di elaborazione esegue anche un'installazione push del servizio Mobility nelle macchine virtuali ed esegue l'individuazione automatica delle macchine virtuali VMware locali.
- Il server di destinazione master, che gestisce i dati di replica durante il failback da Azure.

[Altre informazioni](vmware-azure-architecture.md) sui processi e i componenti del server di configurazione.

### <a name="where-do-i-set-up-the-configuration-server"></a>Dove si configura il server di configurazione?

È necessaria una singola macchina virtuale VMware locale a disponibilità elevata per il server di configurazione. Per il ripristino di emergenza del server fisico, installare il server di configurazione in un computer fisico.

### <a name="what-do-i-need-for-the-configuration-server"></a>Cosa è necessario per il server di configurazione?

Esaminare i [prerequisiti](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>È possibile configurare manualmente il server di configurazione invece di usare un modello?

Si consiglia di [creare la macchina virtuale del server di configurazione](vmware-azure-deploy-configuration-server.md) utilizzando la versione più recente del modello di Open VIRTUALIZATION Format (OVF). Se non è possibile usare il modello (ad esempio, se non si ha accesso al server VMware), [scaricare](physical-azure-set-up-source.md) il file di installazione dal portale e configurare il server di configurazione.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Un server di configurazione può eseguire la replica in più aree?

No. Per eseguire la replica in più aree, è necessario un server di configurazione in ogni area.

### <a name="can-i-host-a-configuration-server-in-azure"></a>È possibile ospitare un server di configurazione in Azure?

Sebbene sia possibile, è necessario che la macchina virtuale di Azure che esegue il server di configurazione comunichi con l'infrastruttura e le macchine virtuali VMware locali. Questa comunicazione aggiunge latenza e influisca sulla replica in corso.

### <a name="how-do-i-update-the-configuration-server"></a>Come si aggiorna il server di configurazione?

[Informazioni](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) su come aggiornare il server di configurazione.

- È possibile trovare le informazioni più recenti sull'aggiornamento nella [pagina aggiornamenti di Azure](https://azure.microsoft.com/updates/?product=site-recovery).
- È possibile scaricare la versione più recente dal portale. In alternativa, è possibile scaricare la versione più recente del server di configurazione direttamente dall' [area download Microsoft](https://aka.ms/asrconfigurationserver).
- Se la versione in uso è superiore a quattro versioni precedenti alla versione corrente, vedere l' [istruzione di supporto](https://aka.ms/asr_support_statement) per informazioni aggiuntive sull'aggiornamento.

### <a name="should-i-back-up-the-configuration-server"></a>È necessario eseguire il backup del server di configurazione?

Si consiglia di eseguire backup pianificati regolari del server di configurazione.

- Per eseguire correttamente il failback, la macchina virtuale di cui è stato eseguito il failback deve esistere nel database del server di configurazione.
- Il server di configurazione deve essere in esecuzione e in stato connesso.
- [Altre](vmware-azure-manage-configuration-server.md) informazioni sulle attività comuni di gestione del server di configurazione.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Durante l'impostazione del server di configurazione è possibile scaricare e installare MySQL manualmente?

Sì. Scaricare MySQL e inserirlo nella cartella C:\Temp\ASRSetup. Quindi, installarlo manualmente. Dopo aver configurato la macchina virtuale del server di configurazione e aver accettato le condizioni, MySQL appare come **Installazione già eseguita** in **Scarica e installa**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Si può evitare di scaricare MySQL ma lasciare che venga installato da Site Recovery?

Sì. Scaricare il programma di installazione di MySQL e inserirlo nella cartella C:\Temp\ASRSetup. Quando si configura la macchina virtuale del server di configurazione, accettare le condizioni e selezionare **Scarica e installa**. Il portale userà il programma di installazione aggiunto per installare MySQL.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>È possibile usare la macchina virtuale del server di configurazione per altri scopi?

No. Usare la macchina virtuale solo per il server di configurazione.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>È possibile clonare un server di configurazione e usarlo per l'orchestrazione?

No. Configurare un nuovo server di configurazione per evitare problemi di registrazione.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>È possibile modificare l'insieme di credenziali in cui è registrato il server di configurazione?

No. Una volta associato un insieme di credenziali al server di configurazione, non è possibile modificarlo. [Informazioni sulla registrazione](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) di un server di configurazione con un insieme di credenziali diverso.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>È possibile usare lo stesso server di configurazione per il ripristino di emergenza di macchine virtuali VMware e server fisici?

Sì, ma si noti che è possibile eseguire il failback del computer fisico solo in una VM VMware.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Dove è possibile scaricare la passphrase per il server di configurazione?

[Informazioni](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) su come scaricare la passphrase.

### <a name="where-can-i-download-vault-registration-keys"></a>Dove è possibile scaricare le chiavi di registrazione dell'insieme di credenziali?

Nell'insieme di credenziali di servizi di ripristino selezionare **server di configurazione** in **infrastruttura Site Recovery** > **Gestisci**. Quindi, in **Server**selezionare **Scarica chiave di registrazione** per scaricare il file dell'insieme di credenziali.

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>È possibile usare un singolo server di configurazione per proteggere più istanze di vCenter?

Sì, un unico server di configurazione può proteggere le macchine virtuali tra più vCenter.  Non è previsto un limite per il numero di istanze di vCenter che è possibile aggiungere al server di configurazione. Tuttavia, vengono applicati i limiti per il numero di macchine virtuali che possono essere protette da un singolo server di configurazione.

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>Un server di configurazione singolo può proteggere più cluster all'interno di vCenter?

Sì, Azure Site Recovery possibile proteggere le macchine virtuali in cluster diversi.

## <a name="process-server"></a>Server di elaborazione

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Perché non è possibile selezionare il server di elaborazione quando si Abilita la replica?

Gli aggiornamenti nelle versioni 9,24 e successive ora visualizzano l' [integrità del server di elaborazione quando si Abilita la replica](vmware-azure-enable-replication.md#enable-replication). Questa funzionalità consente di evitare la limitazione del server di elaborazione e di ridurre al minimo l'utilizzo di server di elaborazione non integri.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Ricerca per categorie aggiornare il server di elaborazione alla versione 9,24 o successiva per ottenere informazioni accurate sull'integrità?

A partire dalla [versione 9,24](service-updates-how-to.md#links-to-currently-supported-update-rollups), sono stati aggiunti altri avvisi per indicare l'integrità del server di elaborazione. [Aggiornare i componenti Site Recovery alla versione 9,24 o successiva in](service-updates-how-to.md#links-to-currently-supported-update-rollups) modo che tutti gli avvisi vengano generati.

## <a name="failover-and-failback"></a>Failover e failback

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>È possibile usare il server di elaborazione locale per il failback?

È consigliabile creare un server di elaborazione in Azure a scopo di failback, per evitare latenze di trasferimento dei dati. Inoltre, nel caso in cui sia stata separata la rete VM di origine con la rete di Azure nel server di configurazione, è essenziale usare il server di elaborazione creato in Azure per il failback.

### <a name="can-i-keep-the-ip-address-on-failover"></a>È possibile gestire l'indirizzo IP in failover?

Sì, è possibile gestire l'indirizzo IP in failover. Assicurarsi di specificare l'indirizzo IP di destinazione nelle impostazioni di **calcolo e di rete** per la macchina virtuale prima del failover. Arrestare inoltre i computer al momento del failover per evitare conflitti di indirizzi IP durante il failback.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>È possibile modificare le dimensioni della macchina virtuale di destinazione o il tipo di macchina virtuale prima del failover?

Sì, è possibile modificare il tipo o le dimensioni della macchina virtuale in qualsiasi momento prima del failover. Nel portale usare le impostazioni di **calcolo e di rete** per la macchina virtuale replicata.

### <a name="how-far-back-can-i-recover"></a>Fino a quando può risalire il recupero?

Per VMware in Azure, il punto di ripristino meno recente che è possibile usare è di 72 ore.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Come si accede alle macchine virtuali di Azure dopo il failover?

Dopo il failover, è possibile accedere alle macchine virtuali di Azure tramite una connessione Internet sicura, una VPN da sito a sito o una ExpressRoute di Azure. Per connettersi, è necessario preparare diversi elementi. [Altre informazioni](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>Il failover dei dati è stato superato?

Azure è progettato nell'ottica della resilienza. Site Recovery è progettato per il failover in un data center secondario di Azure, come richiesto dal contratto di servizio (SLA) di Azure. Quando si verifica il failover, si assicura che i metadati e gli insiemi di credenziali rimangano nella stessa area geografica selezionata per l'insieme di credenziali.

### <a name="is-failover-automatic"></a>Il failover è automatico?

Il [failover](site-recovery-failover.md) non è automatico. Per avviare un failover, è possibile eseguire una singola selezione nel portale oppure usare [PowerShell](/powershell/module/az.recoveryservices) per attivare un failover.

### <a name="can-i-fail-back-to-a-different-location"></a>È possibile eseguire il failback in una posizione diversa?

Sì. Se è stato eseguito il failover in Azure, è possibile eseguire il failback in un percorso diverso se quello originale non è disponibile. [Altre informazioni](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Perché è necessaria una VPN o ExpressRoute con peering privato per eseguire il failback?

Quando si esegue il failback da Azure, i dati di Azure vengono copiati di nuovo nella macchina virtuale locale e l'accesso privato è necessario.

### <a name="can-i-resize-the-azure-vm-after-failover"></a>È possibile ridimensionare la macchina virtuale di Azure dopo il failover?

No, non è possibile modificare la dimensione o il tipo della VM di destinazione dopo il failover.

## <a name="automation-and-scripting"></a>Automazione e scripting

### <a name="can-i-set-up-replication-with-scripting"></a>È possibile configurare la replica con lo scripting?

Sì. È possibile automatizzare i flussi di lavoro Site Recovery usando l'API REST, PowerShell o Azure SDK. [Altre informazioni](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Prestazioni e capacità

### <a name="can-i-throttle-replication-bandwidth"></a>È possibile limitare la larghezza di banda per la replica?

Sì. [Altre informazioni](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>Passaggi successivi

- [Esaminare](vmware-physical-azure-support-matrix.md) i requisiti di supporto.
- [Configurare](vmware-azure-tutorial.md) la replica da VMware ad Azure.
