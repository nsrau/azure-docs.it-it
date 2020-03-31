---
title: Novità di Azure Site Recovery
description: Fornisce un riepilogo delle nuove funzionalità e degli aggiornamenti più recenti nel servizio Azure Site Recovery.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: abb1592bcacf025e9a052d7a9222f6fb3d2b72d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257433"
---
# <a name="whats-new-in-site-recovery"></a>Novità di Site Recovery

Il servizio [Azure Site Recovery](site-recovery-overview.md) viene aggiornato e migliorato in modo continuativo. Per consentire ai clienti di restare sempre aggiornati, in questo articolo vengono fornite informazioni sulle versioni più recenti, le nuove funzionalità e i nuovi contenuti. Questa pagina viene aggiornata regolarmente.

È possibile seguire e sottoscrivere le notifiche di aggiornamento di Site Recovery nel canale degli aggiornamenti di Azure.You can follow and subscribe to Site Recovery update notifications in the [Azure updates](https://azure.microsoft.com/updates/?product=site-recovery) channel.

## <a name="supported-updates"></a>Aggiornamenti supportati

Per i componenti di Site Recovery, sono supportate le versioni N-4, dove N è l'ultima versione rilasciata. Questi report sono riepilogati nella tabella seguente.

**Aggiornamento** |  **Installazione unificata** | **Server di configurazione ovuli** | **Agente del servizio di mobilità** | **Site Recovery** | **Agente di Servizi di ripristino**
--- | --- | --- | --- | --- | ---
[Rollup 45](https://support.microsoft.com/help/4550047/) | 9.32.5487.1 | 5.1.5400.0 | 9.32.5487.1 | 5.1.5400.0 | 2.0.9165.0
[Rollup 43](https://support.microsoft.com/help/4537047/) | 9.31.5449.1 | 5.1.5300.0 | 9.31.5449.1 | 5.1.5300.0 | 2.0.9165.0
[Rollup 42](https://support.microsoft.com/help/4531426/) | 9.30.5407.1 | 5.1.5200.0 | 9.30.5407.1 | 5.1.5200.0 | 2.0.9165.0
[Rollup 41](https://support.microsoft.com/help/4528026/) | 9.29.5367.1 | 5.1.5000.0 | 9.29.5367.1 | 5.1.5000.0 | 2.0.9165.0
[Rollup 40](https://support.microsoft.com/help/4521530/) | 9.28.5345.1 | 5.1.4800.0 | 9.28.5345.1 | 5.1.4800.0 | 2.0.9165.0

[Ulteriori informazioni](service-updates-how-to.md) sull'installazione e il supporto degli aggiornamenti.

> [!NOTE]
> Aggiornamento cumulativo 44 non viene visualizzato nella tabella perché non include gli aggiornamenti per i provider di Site Recovery e gli agenti.

## <a name="updates-march-2020"></a>Aggiornamenti (marzo 2020)

### <a name="update-rollup-45"></a>Aggiornamento cumulativo 45

[Aggiornamento cumulativo 45](https://support.microsoft.com/help/4550047/update-rollup-45-for-azure-site-recovery) fornisce i seguenti aggiornamenti:

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamenti agli agenti e ai provider di Site Recovery come descritto nel rollup.
**Correzioni/miglioramenti dei problemi** | Una serie di correzioni e miglioramenti come descritto nel rollup.

## <a name="updates-january-2020"></a>Aggiornamenti (gennaio 2020)

### <a name="update-rollup-44"></a>Aggiornamento cumulativo 44

[Aggiornamento cumulativo 44](https://support.microsoft.com/help/4538187/update-rollup-44-for-azure-site-recovery) fornisce i seguenti aggiornamenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Non sono stati disponibili aggiornamenti per i provider e gli agenti di Site Recovery.
**Correzioni/miglioramenti dei problemi** | Una serie di correzioni e miglioramenti come descritto nel rollup.

### <a name="azure-vmware-disaster-recovery"></a>Ripristino di emergenza di Azure VMwareAzure VMware disaster recovery

Le macchine virtuali di Azure supportano ora le macchine virtuali abilitate per la crittografia inattivi con chiavi gestite dal cliente. [Scopri di più](azure-to-azure-how-to-enable-replication-cmk-disks.md).


### <a name="update-rollup-43"></a>Aggiornamento cumulativo 43

[Aggiornamento cumulativo 43](https://support.microsoft.com/help/4537047/update-rollup-43-for-azure-site-recovery) fornisce i seguenti aggiornamenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamenti agli agenti e ai provider di Site Recovery (come descritto nel rollup)
**Correzioni/miglioramenti dei problemi** | Una serie di correzioni e miglioramenti (come descritto nel rollup)


## <a name="updates-november-2019"></a>Aggiornamenti (novembre 2019)

### <a name="update-rollup-42"></a>Aggiornamento cumulativo 42

[L'aggiornamento cumulativo 42](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) fornisce i seguenti aggiornamenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamenti agli agenti e ai provider di Site Recovery (come descritto nel rollup)
**Correzioni/miglioramenti dei problemi** | Una serie di correzioni e miglioramenti (come descritto nel rollup)


### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza della macchina virtuale di AzureAzure VM disaster recovery

Le nuove funzionalità per il ripristino di emergenza della macchina virtuale di Azure sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**UEFI** | Site Recovery supporta ora il ripristino di emergenza per le macchine virtuali di Azure con l'architettura di avvio basata su UEFI.
**Linux** | Site Recovery supporta ora le macchine virtuali di Azure che eseguono Linux con Azure Disk Encryption (ADE).
**Generazione 2** | Tutte le macchine virtuali di Azure di generazione 2 sono ora supportate per il ripristino di emergenza.
**Regioni** | È ora possibile abilitare il ripristino di emergenza per le macchine virtuali di Azure nell'area geografica di Norvegia.You can now enable disaster recovery for Azure VMs in the Norway geo.

### <a name="vmware-to-azure-disaster-recovery"></a>Ripristino di emergenza da VMware ad Azure

Le nuove funzionalità per il ripristino di emergenza da VMware ad Azure sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**UEFI** | Site Recovery supporta ora il ripristino di emergenza per le macchine virtuali VMware con l'architettura di avvio basata su UEFI.<br/><br/> I sistemi operativi supportati includono Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, SLES 12 SP4, RHEL 8.

## <a name="update-to-servicing-stack-updatesha-2"></a>Aggiornamento per la manutenzione dell'aggiornamento dello stack/SHA-2

Per il ripristino di emergenza delle macchine virtuali di Azure in un'area secondaria o per le macchine virtuali VMware locali o i server fisici in Azure, tenere presente quanto segue:For disaster recovery of Azure VMs to a secondary region, or on-premises VMware VMs or physical servers to Azure, note the following:

- Dalla versione 9.30.5407.1 dell'estensione del servizio Mobility (per le macchine virtuali di Azure) e dall'agente del servizio Mobility (per VMware/macchine virtuali), alcuni sistemi operativi del computer devono eseguire l'aggiornamento dello stack di manutenzione e SHA-2. I dettagli sono riportati nella tabella seguente.
- Installare l'aggiornamento e SHA-2 in base alla KB collegata. SHA-1 non è supportato da settembre 2019 e se la firma del codice SHA-2 non è abilitata l'estensione dell'agente non installerà/aggiorna come previsto.
- Ulteriori informazioni [sull'aggiornamento e sui requisiti SHA-2](https://aka.ms/SHA-2KB).

**Sistema operativo** | **Macchina virtuale di Azure** | **VM/macchina fisica VMware**
--- | --- | ---
**Windows 2008 R2 SP1** | [Aggiornamento dello stack di manutenzione](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Aggiornamento dello stack di manutenzione](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 2008 SP2** | [Aggiornamento dello stack di manutenzione](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Aggiornamento dello stack di manutenzione](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 7 SP1** | [Aggiornamento dello stack di manutenzione](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Aggiornamento dello stack di manutenzione](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419).



## <a name="updates-october-2019"></a>Aggiornamenti (ottobre 2019)

### <a name="update-rollup-41"></a>Aggiornamento cumulativo 41

[Aggiornamento cumulativo 41](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) fornisce i seguenti aggiornamenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamenti agli agenti e ai provider di Site Recovery (come descritto nel rollup)
**Correzioni/miglioramenti dei problemi** | Una serie di correzioni e miglioramenti (come descritto nel rollup)



### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza della macchina virtuale di AzureAzure VM disaster recovery

Le nuove funzionalità per il ripristino di emergenza della macchina virtuale di Azure sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Testare le impostazioni di failoverTest failover settings** | Quando si configura un failover di test, è ora possibile configurare le impostazioni per la macchina virtuale e la rete di failover di test, inclusi l'indirizzo IP, il gruppo di sicurezza di rete, il bilanciamento del carico interno e l'indirizzo IP pubblico per ogni scheda di interfaccia di rete del computer. Queste impostazioni sono facoltative e non modificano il comportamento corrente. Se non si configurano queste impostazioni, è possibile scegliere una rete virtuale di Azure al momento del failover di test. [Scopri di più](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/).
**Piani di recupero** | I piani di ripristino sono ora limitati a 100 macchine virtuali, per garantire l'affidabilità del failover.

### <a name="vmware-to-azure-disaster-recovery"></a>Ripristino di emergenza da VMware ad Azure

Le nuove funzionalità per il ripristino di emergenza da VMware ad Azure sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Piani di recupero** | I piani di ripristino sono ora limitati a 100 macchine virtuali, per garantire l'affidabilità del failover.


## <a name="updates-september-2019"></a>Aggiornamenti (settembre 2019)

### <a name="update-rollup-40"></a>Aggiornamento cumulativo 40

[Aggiornamento cumulativo 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) fornisce i seguenti aggiornamenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamenti agli agenti e ai provider di Site Recovery (come descritto nel rollup)
**Correzioni/miglioramenti dei problemi** | Una serie di correzioni e miglioramenti (come descritto nel rollup)




### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza della macchina virtuale di AzureAzure VM disaster recovery

Le nuove funzionalità per il ripristino di emergenza della macchina virtuale di Azure sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Pulizia dopo il failbackCleanup after failback** | Dopo il failover nell'Azure secondario e quindi il failback nell'area primaria, Site Recovery pulisce automaticamente i computer nell'area secondaria. Non è necessario eliminare manualmente macchine virtuali e schede di interfaccia di rete.
**Il failover di test mantiene l'indirizzo IP** | È ora possibile mantenere l'indirizzo IP della macchina virtuale di origine durante un'esercitazione sul ripristino di emergenza e selezionare un indirizzo IP statico per un failover di test.

### <a name="vmwarephysical-server-disaster-recovery"></a>Ripristino di emergenza del server virtuale di VMware/fisico

Le funzionalità aggiunte questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
Nuovi avvisi del server di elaborazione | Sono stati aggiunti nuovi avvisi del server di elaborazione. [Scopri di più](vmware-physical-azure-monitor-process-server.md).

### <a name="hyper-v-disaster-recovery"></a>Ripristino di emergenza Hyper-V

Le funzionalità aggiunte questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
Account di archiviazione | Site Recovery supporta ora l'uso di account di archiviazione con firewall abilitato per il ripristino di emergenza da Hyper-V ad Azure.Site Recovery now supports the use of storage accounts with firewall enabled for Hyper-V to Azure disaster recovery.  È possibile selezionare gli account di archiviazione abilitati al firewall come account di destinazione o per l'archiviazione della cache. Se si utilizza un account abilitato per il firewall, assicurarsi di abilitare l'opzione per consentire i servizi Microsoft attendibili.<br/><br/> Questa funzionalità è supportata per le macchine virtuali Hyper-V con o senza System Center VMM.


## <a name="updates-august-2019"></a>Aggiornamenti (agosto 2019)

### <a name="update-rollup-39"></a>Aggiornamento cumulativo 39

[Aggiornamento cumulativo 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) fornisce i seguenti aggiornamenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamenti agli agenti e ai provider di Site Recovery (come descritto nel rollup)
**Correzioni/miglioramenti dei problemi** | Una serie di correzioni e miglioramenti (come descritto nel rollup)


### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza della macchina virtuale di AzureAzure VM disaster recovery

Le nuove funzionalità per il ripristino di emergenza della macchina virtuale di Azure sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Crittografia senza Azure ADEncryption without Azure AD** | La crittografia senza un'app Azure AD è ora supportata per la replica delle macchine virtuali di Azure in dischi gestiti che eseguono Windows.Encryption without an Azure AD app is now supported for Azure VM replication to managed disks running Windows.
**Risorse di rete per il failoverNetwork resources for failover** | Quando si esegue il failover in un'altra area, è ora possibile collegare le impostazioni delle risorse di rete (NSG, bilanciamento del carico, indirizzo IP pubblico) a una macchina virtuale.

## <a name="updates-july-2019"></a>Aggiornamenti (luglio 2019)

### <a name="update-rollup-38"></a>Aggiornamento cumulativo 38

[Aggiornamento cumulativo 38](https://support.microsoft.com/help/4513507/) fornisce i seguenti aggiornamenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamenti agli agenti e ai provider di Site Recovery (come descritto nel rollup)
**Correzioni/miglioramenti dei problemi** | Una serie di correzioni e miglioramenti (come descritto nel rollup)


### <a name="general"></a>Generale

Site Recovery supporta ora l'uso di account di archiviazione generici v2 per l'archiviazione della cache o l'archiviazione di destinazione. In precedenza era supportata solo la versione 1.

### <a name="vmware-to-azure-disaster-recovery"></a>Ripristino di emergenza da VMware ad Azure

È ora possibile replicare dischi fino a 8 TB durante la replica in una macchina virtuale di Azure con dischi gestiti.


## <a name="updates-june-2019"></a>Aggiornamenti (giugno 2019)

### <a name="update-rollup-37"></a>Aggiornamento cumulativo 37

[L'aggiornamento cumulativo 37](https://support.microsoft.com/help/4508614/) fornisce i seguenti aggiornamenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamenti agli agenti e ai provider di Site Recovery (come descritto nel rollup)
**Correzioni/miglioramenti dei problemi** | Una serie di correzioni e miglioramenti (come descritto nel rollup)


### <a name="vmwarephysical-server-disaster-recovery"></a>Ripristino di emergenza del server virtuale di VMware/fisico

Le funzionalità aggiunte questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Partizioni GPT** | Dall'aggiornamento cumulativo 37 in poi (versione 9.25.5241.1 del servizio Mobility), in UEFI sono supportate fino a cinque partizioni GPT. Prima di questo aggiornamento, quattro erano supportati.



## <a name="updates-may-2019"></a>Aggiornamenti (maggio 2019)

### <a name="update-rollup-36"></a>Aggiornamento cumulativo 36

[Aggiornamento cumulativo 36](https://support.microsoft.com/help/4503156) fornisce i seguenti aggiornamenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamento per gli agenti e i provider di Site Recovery (come descritto nel rollup)
**Correzioni/miglioramenti dei problemi** | Una serie di correzioni e miglioramenti (come descritto nel rollup)

### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza della macchina virtuale di AzureAzure VM disaster recovery

Le funzionalità aggiunte questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Replica redei aggiunti** | Abilitare la replica per i dischi dati aggiunti a una macchina virtuale di Azure già abilitata per il ripristino di emergenza. [Scopri di più](azure-to-azure-enable-replication-added-disk.md).
**Aggiornamenti automatici** | Quando si configurano gli aggiornamenti automatici per l'estensione del servizio Mobility che viene eseguita nelle macchine virtuali di Azure abilitate per il ripristino di emergenza, è ora possibile selezionare un account di automazione esistente da usare, anziché usare l'account predefinito creato da Site Recovery.When configuring automatic updates for the Mobility service extension that runs on Azure VMs enabled for disaster recovery, you can now select an existing automation account to use, instead of using the default account created by Site Recovery. [Scopri di più](azure-to-azure-autoupdate.md).


### <a name="vmwarephysical-server-disaster-recovery"></a>Ripristino di emergenza del server virtuale di VMware/fisico

Le funzionalità aggiunte questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Monitoraggio del server di elaborazione** | Per il ripristino di emergenza delle macchine virtuali VMware e dei server fisici locali, monitorare e risolvere i problemi del server di elaborazione con avvisi e report di integrità dei server migliorati. [Scopri di più](vmware-physical-azure-monitor-process-server.md).





## <a name="updates-march-2019"></a>Aggiornamenti (marzo 2019)

### <a name="update-rollup-35"></a>Aggiornamento cumulativo 35

[Aggiornamento cumulativo 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) fornisce i seguenti aggiornamenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamento per gli agenti e i provider di Site Recovery (come descritto nel rollup)
**Correzioni/miglioramenti dei problemi** | Una serie di correzioni e miglioramenti (come descritto nel rollup)

### <a name="vmwarephysical-server-disaster-recovery"></a>Ripristino di emergenza del server virtuale di VMware/fisico

Le funzionalità aggiunte questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Dischi gestiti** | La replica delle macchine virtuali VMware e dei server fisici locali è ora direttamente ai dischi gestiti in Azure.Replication of on-premises VMware VMs and physical servers is now directly to managed disks in Azure. I dati locali vengono inviati a un account di archiviazione della cache in Azure e i punti di ripristino vengono creati nei dischi gestiti nel percorso di destinazione. In questo modo non è necessario gestire più account di archiviazione di destinazione.
**Server di configurazione** | Site Recovery ora supporta i server di configurazione con più schede di interfaccia di rete. Aggiungere ulteriori adapter alla macchina virtuale del server di configurazione prima di registrare il server di configurazione nell'insieme di credenziali. Se si aggiunge in seguito, è necessario registrare nuovamente il server nell'insieme di credenziali.


## <a name="updates-february-2019"></a>Aggiornamenti (febbraio 2019)

### <a name="update-rollup-34"></a>Aggiornamento cumulativo 34

[Aggiornamento cumulativo 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) fornisce i seguenti aggiornamenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamento per gli agenti e i provider di Site Recovery (come descritto nel rollup).
**Correzioni/miglioramenti dei problemi** | Una serie di correzioni e miglioramenti (come descritto nel rollup).


### <a name="update-rollup-33"></a>Aggiornamento cumulativo 33

[Aggiornamento cumulativo 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) fornisce i seguenti aggiornamenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamento per gli agenti e i provider di Site Recovery (come descritto nel rollup).
**Correzioni/miglioramenti dei problemi** | Una serie di correzioni e miglioramenti (come descritto nel rollup).


### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza della macchina virtuale di AzureAzure VM disaster recovery

Le funzionalità aggiunte questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Mapping di rete** | Per il ripristino di emergenza della macchina virtuale di Azure, è ora possibile usare qualsiasi rete di destinazione disponibile quando si abilita la replica.
**SSD Standard** | È ora possibile configurare il ripristino di emergenza per le macchine virtuali di Azure usando [i dischi SSD standard.](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)
**Spazi di archiviazione diretta** | È possibile configurare il ripristino di emergenza per le app in esecuzione nelle app di macchine virtuali di Azure usando [Spazi di archiviazione diretti](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) per la disponibilità elevata.  L'uso di Storage Spaces Direct (S2D) insieme a Site Recovery offre una protezione completa dei carichi di lavoro delle macchine virtuali di Azure.Using Storage Spaces Direct (S2D) together with Site Recovery provides comprehensive protection of Azure VM workloads. S2D lets you host a guest cluster in Azure. Ciò è particolarmente utile quando una macchina virtuale ospita un'applicazione critica, ad esempio il livello SAP ASCS, SQL Server o il file server di scalabilità orizzontale.


### <a name="vmwarephysical-server-disaster-recovery"></a>Ripristino di emergenza del server virtuale di VMware/fisico

Le funzionalità aggiunte questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**File system LINUX BRTFS** | Site Recovery supporta ora la replica delle macchine virtuali VMware con il file system BRTFS. La replica non è supportata se:Replication isn't supported if:<br/><br/>- Il sottovolume del file system BTRFS viene modificato dopo l'abilitazione della replica.<br/><br/>- Il file system è distribuito su più dischi.<br/><br/>- Il file system BTRFS supporta RAID.
**Windows Server 2019** | Supporto aggiunto per i computer che eseguono Windows Server 2019.


## <a name="updates-january-2019"></a>Aggiornamenti (gennaio 2019)


### <a name="accelerated-networking-azure-vms"></a>Rete accelerata (macchine virtuali di Azure)Accelerated networking (Azure VMs)

La rete accelerata consente la virtualizzazione SR-IOV (Single Root I/O) a una macchina virtuale, migliorando le prestazioni di rete. Quando si abilita la replica per una macchina virtuale di Azure, Site Recovery rileva se è abilitata la rete accelerata. In caso affermativo, dopo il failover Site Recovery configura automaticamente la rete accelerata nella macchina virtuale di Azure di replica di destinazione, sia per [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) che per [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

[Scopri di più](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>Aggiornamento cumulativo 32

[Aggiornamento cumulativo 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) fornisce i seguenti aggiornamenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamento per gli agenti e i provider di Site Recovery (come descritto nel rollup).
**Correzioni/miglioramenti dei problemi** | Una serie di correzioni e miglioramenti (come descritto nel rollup).

### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza della macchina virtuale di AzureAzure VM disaster recovery

Le funzionalità aggiunte questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto di Linux** | È stato aggiunto il supporto per RedHat Workstation 6/7 e nuove versioni del kernel per Ubuntu, Debian e SUSE.
**Spazi di archiviazione diretta** | Site Recovery supporta le macchine virtuali di Azure usando Spazi di archiviazione diretta (S2D).

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>Ripristino di emergenza di macchine virtuali/server fisici VMware

Le funzionalità aggiunte questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto di Linux** | È stato aggiunto il supporto per Redhat Enterprise Linux 7.6, RedHat Workstation 6/7, Oracle Linux 6.10 e Oracle Linux 7.6 e nuove versioni del kernel per Ubuntu, Debian e SUSE.


### <a name="update-rollup-31"></a>Aggiornamento cumulativo 31

[Aggiornamento cumulativo 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) fornisce i seguenti aggiornamenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamento per gli agenti e i provider di Site Recovery (come descritto nel rollup).
**Correzioni/miglioramenti dei problemi** | Una serie di correzioni e miglioramenti (come descritto nel rollup).

### <a name="vmware-vmsphysical-servers-replication"></a>Replica di macchine virtuali VMware/server fisici

Le funzionalità aggiunte questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto di Linux** |  È stato aggiunto il supporto per Oracle Linux 6.8, Oracle Linux 6.9 e Oracle Linux 7.0 con il kernel compatibile Red Hat e per la Versione 5 di Unbreakable Enterprise Kernel (UEK).
**LVM** | Supporto aggiunto per i volumi LVM e LVM2.<br/><br/> La directory /boot in una partizione del disco e nei volumi LVM è ora supportata.
**Directory** | È stato aggiunto il supporto per queste directory impostate come partizioni separate o file system che non si trovavano sullo stesso disco di sistema:<br/><br/> /(radice), /boot, /usr, /usr/local, /var, /etc.
**Windows Server 2008 (informazioni in due)** | Supporto aggiunto per i dischi dinamici.
**Failover** | Tempi di failover migliorati per le macchine virtuali VMware in cui storvsc e vsbus non sono driver di avvio.
**Sostegno UEFI** | Le macchine virtuali di Azure non supportano il tipo di avvio UEFI. È ora possibile eseguire la migrazione dei server fisici locali con UEFI ad Azure con Site Recovery.You can now migrate on-premises physical servers with UEFI to Azure with Site Recovery. Site Recovery esegue la migrazione del server convertendo il tipo di avvio in BIOS prima della migrazione. Site Recovery in precedenza supportava questa conversione solo per le macchine virtuali. Il supporto è disponibile per i server fisici che eseguono Windows Server 2012 o versione successiva.

### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza della macchina virtuale di AzureAzure VM disaster recovery

Le funzionalità aggiunte questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto di Linux** | È stato aggiunto il supporto per Oracle Linux 6.8, Oracle Linux 6.9 e Oracle Linux 7.0 con il kernel compatibile Red Hat e per la Versione 5 di Unbreakable Enterprise Kernel (UEK).
**File system LINUX BRTFS** | Supportato per le macchine virtuali di Azure.Supported for Azure VMs.
**Macchine virtuali di Azure nelle zone di disponibilitàAzure VMs in availability zones** | È possibile abilitare la replica in un'altra area per le macchine virtuali di Azure distribuite nelle zone di disponibilità. È ora possibile abilitare la replica per una macchina virtuale di Azure e impostare la destinazione per il failover su una singola istanza di macchina virtuale, una macchina virtuale in un set di disponibilità o una macchina virtuale in una zona di disponibilità. L'impostazione non influisce sulla replica. [Leggere](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) l'annuncio.
**Archiviazione abilitata per il firewall (portale/PowerShell)Firewall-enabled storage (portal/PowerShell)** | Supporto aggiunto per [gli account di archiviazione abilitati al firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> È possibile replicare le macchine virtuali di Azure con dischi non gestiti negli account di archiviazione abilitati al firewall in un'altra area di Azure per il ripristino di emergenza.<br/><br/> È possibile usare account di archiviazione abilitati al firewall come account di archiviazione di destinazione per i dischi non gestiti.<br/><br/> Supportato nel portale e tramite PowerShell.Supported in portal and using PowerShell.

## <a name="updates-december-2018"></a>Aggiornamenti (dicembre 2018)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Aggiornamenti automatici per il servizio Mobility (macchine virtuali di Azure)Automatic updates for the Mobility service (Azure VMs)

In Site Recovery è stata aggiunta un'opzione per gli aggiornamenti automatici per l'estensione del servizio Mobility. L'estensione del servizio Mobility viene installata in ogni macchina virtuale di Azure replicata da Site Recovery. Quando si abilita la replica, occorre selezionare se si vuole consentire a Site Recovery di gestire gli aggiornamenti per l'estensione.

Gli aggiornamenti non richiedono un riavvio delle macchine virtuali e non influiscono sulla replica. [Scopri di più](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Calcolatore dei prezzi per il ripristino di emergenza delle macchine virtuali di Azure

Il ripristino di emergenza delle macchine virtuali di Azure comporta costi di licenza per le macchine virtuali e costi di rete e archiviazione. Azure offre un [calcolatore dei prezzi](https://aka.ms/a2a-cost-estimator) per stimare questi costi. Site Recovery offre ora una [stima dei prezzi di esempio](https://aka.ms/a2a-cost-estimator) per una distribuzione di esempio basata su un'app a tre livelli con sei macchine virtuali con 12 dischi HDD Standard e 6 dischi SSD Premium.

- L'esempio presuppone una modifica dei dati di 10 GB al giorno per lo standard e di 20 GB per premium.
- È possibile modificare le variabili per stimare i costi per una distribuzione specifica.
- È possibile specificare il numero di macchine virtuali, il numero e il tipo di dischi gestiti e la frequenza di modifica dei dati prevista per tutte le macchine virtuali.
- È anche possibile applicare un fattore di compressione per stimare i costi della larghezza di banda.

[Leggere](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) l'annuncio.


## <a name="updates-october-2018"></a>Aggiornamenti (ottobre 2018)

### <a name="update-rollup-30"></a>Aggiornamento cumulativo 30

[Aggiornamento cumulativo 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) fornisce i seguenti aggiornamenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamento per gli agenti e i provider di Site Recovery (come descritto nel rollup).
**Correzioni/miglioramenti dei problemi** | Una serie di correzioni e miglioramenti (come descritto nel rollup).

### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza della macchina virtuale di AzureAzure VM disaster recovery
Le funzionalità aggiunte questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto di area** | Supporto per Site Recovery aggiunto per Australia Central 1 e Australia Central 2.
**Supporto per la crittografia del disco** | Supporto aggiunto per il ripristino di emergenza delle macchine virtuali di Azure crittografate con Azure Disk Encryption (ADE) con l'app Azure AD. [Scopri di più](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Esclusione disco** | I dischi non inizializzati vengono ora esclusi automaticamente durante la replica della macchina virtuale di Azure.Uninitialized disks are now automatically excluded during Azure VM replication.
**Archiviazione abilitata per il firewall (PowerShell)Firewall-enabled storage (PowerShell)** | Supporto aggiunto per [gli account di archiviazione abilitati al firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> È possibile replicare le macchine virtuali di Azure con dischi non gestiti negli account di archiviazione abilitati al firewall in un'altra area di Azure per il ripristino di emergenza.<br/><br/> È possibile usare account di archiviazione abilitati al firewall come account di archiviazione di destinazione per i dischi non gestiti.<br/><br/> Supportato solo tramite PowerShell.Supported using PowerShell only.


### <a name="update-rollup-29"></a>Aggiornamento cumulativo 29

[Aggiornamento cumulativo 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) fornisce i seguenti aggiornamenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamento per gli agenti e i provider di Site Recovery (come descritto nel rollup).
**Correzioni/miglioramenti dei problemi** | Una serie di correzioni e miglioramenti (come descritto nel rollup).


## <a name="updates-august-2018"></a>Aggiornamenti (agosto 2018)

### <a name="update-rollup-28"></a>Aggiornamento cumulativo 28

[Aggiornamento cumulativo 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) fornisce i seguenti aggiornamenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamento per gli agenti e i provider di Site Recovery (come descritto nel rollup).
**Correzioni/miglioramenti dei problemi** | Una serie di correzioni e miglioramenti (come descritto nel rollup).

### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza della macchina virtuale di AzureAzure VM disaster recovery
Le funzionalità aggiunte questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto di Linux** | Aggiunto supportato per RedHat Enterprise Linux 6.10; CentOS 6.10.<br/><br/>
**Supporto cloud** | Ripristino di emergenza supportato per le macchine virtuali di Azure nel cloud in Germania.Supported disaster recovery for Azure VMs in the Germany cloud.
**Ripristino di emergenza tra sottoscrizioniCross-subscription disaster recovery** | Supporto per la replica delle macchine virtuali di Azure in un'area in un'altra area in una sottoscrizione diversa, all'interno dello stesso tenant di Azure Active Directory.Support for replicating Azure VMs in one region to another region in a different subscription, within the same Azure Active Directory tenant. [Scopri di più](https://aka.ms/cross-sub-blog).

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Ripristino di emergenza di VM/server fisici VMware
Le funzionalità aggiunte questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto di Linux** | Supporto aggiunto per RedHat Enterprise Linux 6.10, CentOS 6.10.<br/><br/> Le macchine virtuali basate su Linux che utilizzano lo stile di partizione della tabella di partizione GUID (GPT) in modalità di compatibilità del BIOS legacy sono ora supportate. Per altre informazioni, vedere le domande frequenti sulla macchina virtuale di Azure.Review the [Azure VM FAQ](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) for more information.
**Ripristino di emergenza per le macchine virtuali dopo la migrazioneDisaster recovery for VMs after migration** | Supporto per l'abilitazione del ripristino di emergenza in un'area secondaria per una macchina virtuale VMware locale migrata in Azure, senza la necessità di disinstallare il servizio Mobility nella macchina virtuale prima di abilitare la replica.
**Windows Server 2008 (informazioni in due)** | Supporto per la migrazione di macchine che eseguono Windows Server 2008 R2/2008 a 64 bit e 32 bit.<br/><br/> Solo migrazione (replica e failover). Il failback non è supportato.

## <a name="updates-july-2018"></a>Aggiornamenti (luglio 2018)

### <a name="update-rollup-27-july-2018"></a>Aggiornamento cumulativo 27 (luglio 2018)

[L'aggiornamento cumulativo 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) fornisce i seguenti aggiornamenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamento per gli agenti e i provider di Site Recovery (come descritto nel rollup).
**Correzioni/miglioramenti dei problemi** | Una serie di correzioni e miglioramenti (come descritto nel rollup).

### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza della macchina virtuale di AzureAzure VM disaster recovery

Le funzionalità aggiunte questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto di Linux** | Supporto aggiunto per Red Hat Enterprise Linux 7.5.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Ripristino di emergenza di VM/server fisici VMware

Le funzionalità aggiunte questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto di Linux** | Supporto aggiunto per Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12.



## <a name="next-steps"></a>Passaggi successivi

Mantenersi aggiornati nella pagina [Aggiornamenti di Azure](https://azure.microsoft.com/updates/?product=site-recovery).
