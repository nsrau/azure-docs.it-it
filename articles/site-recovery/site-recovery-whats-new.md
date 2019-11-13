---
title: Novità di Azure Site Recovery
description: Fornisce un riepilogo delle nuove funzionalità introdotte in Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: raynew
ms.openlocfilehash: 19bf79cdb6bcdc0c20d47c698647383b758338db
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961326"
---
# <a name="whats-new-in-site-recovery"></a>Novità di Site Recovery

Il servizio [Azure Site Recovery](site-recovery-overview.md) viene aggiornato e migliorato in modo continuativo. Per consentire ai clienti di restare sempre aggiornati, in questo articolo vengono fornite informazioni sulle versioni più recenti, le nuove funzionalità e i nuovi contenuti. Questa pagina viene aggiornata regolarmente.

È possibile seguire e sottoscrivere le notifiche di aggiornamento Site Recovery nel canale degli [aggiornamenti di Azure](https://azure.microsoft.com/updates/?product=site-recovery) .



## <a name="supported-updates"></a>Aggiornamenti supportati

Per Site Recovery componenti, sono supportate le versioni N-4, dove N è la versione rilasciata più recente. Questi sono riepilogati nella tabella seguente.

**Aggiornare** |  **Configurazione unificata** | **OVA del server di configurazione** | **Agente del servizio Mobility** | **Provider di Site Recovery** | **Agente di Servizi di ripristino**
--- | --- | --- | --- | --- | ---
[Rollup 42](https://support.microsoft.com/help/4531426/) | 9.30.5407.1 | 5.1.5200.0 | 9.30.5407.1 | 5.1.5200.0 | 2.0.9165.0
[Rollup 41](https://support.microsoft.com/help/4528026/) | 9.29.5367.1 | 5.1.5000.0 | 9.29.5367.1 | 5.1.5000.0 | 2.0.9165.0
[Rollup 40](https://support.microsoft.com/help/4521530/) | 9.28.5345.1 | 5.1.4800.0 | 9.28.5345.1 | 5.1.4800.0 | 2.0.9165.0
[Rollup 39](https://support.microsoft.com/help/4517283/) | 9.27.5308.1 | 5.1.4600.0 | 9.27.5308.1 | 5.1.4600.0 | 2.0.9165.0
[Rollup 38](https://support.microsoft.com/help/4513507/) | 9.26.5269.1 | 5.1.4500.0 | 9.26.5269.1 | 5.1.4500.0 | 2.0.9165.0
        

[Altre](service-updates-how-to.md) informazioni sull'installazione e il supporto degli aggiornamenti.


## <a name="updates-november-2019"></a>Aggiornamenti (2019 novembre)

### <a name="update-rollup-42"></a>Aggiornamento cumulativo 42

L' [aggiornamento cumulativo 42](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornare** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamenti per Site Recovery agenti e provider (come descritto in dettaglio nel rollup)
**Correzioni di problemi/miglioramenti** | Una serie di correzioni e miglioramenti (come descritto in dettaglio nel rollup)


## <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza per macchine virtuali di Azure

Le nuove funzionalità per il ripristino di emergenza delle macchine virtuali di Azure sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**UEFI** | Site Recovery supporta ora il ripristino di emergenza per macchine virtuali di Azure con architettura di avvio basata su UEFI.
**Linux** | Site Recovery supporta ora le VM di Azure che eseguono Linux con crittografia dischi di Azure (ADE).
**Generazione 2** | Tutte le macchine virtuali di Azure di seconda generazione sono ora supportate per il ripristino di emergenza.
**Aree** | È ora possibile abilitare il ripristino di emergenza per le macchine virtuali di Azure in Norvegia geografica.

### <a name="vmware-to-azure-disaster-recovery"></a>Ripristino di emergenza da VMware ad Azure

Le nuove funzionalità per il ripristino di emergenza da VMware ad Azure sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**UEFI** | Site Recovery supporta ora il ripristino di emergenza per le macchine virtuali VMware con architettura di avvio basata su UEFI.<br/><br/> I sistemi operativi supportati includono Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, SLES 12 SP4, RHEL 8.

## <a name="update-to-servicing-stack-updatesha-2"></a>Aggiornamento ad aggiornamento dello stack di manutenzione/SHA-2

Per il ripristino di emergenza di macchine virtuali di Azure in un'area secondaria o macchine virtuali VMware locali o server fisici in Azure, tenere presente quanto segue:

- Dalla versione 9.30.5407.1 dell'estensione del servizio Mobility (per le VM di Azure) e dell'agente del servizio Mobility (per i computer VMware/fisici), alcuni sistemi operativi dei computer devono eseguire l'aggiornamento dello stack di manutenzione e SHA-2. I dettagli sono riportati nella tabella seguente.
- Installare l'aggiornamento e SHA-2 in base alla KB collegata. SHA-1 non è supportato dal 2019 settembre e se la firma del codice SHA-2 non è abilitata, l'estensione dell'agente non verrà installata o aggiornata come previsto.
- Scopri di più sull'[aggiornamento e sui requisiti per SHA-2](https://aka.ms/SHA-2KB).

**Sistema operativo** | **Macchina virtuale di Azure** | **VM VMware/computer fisico**
--- | --- | ---
**Windows 2008 R2 SP1** | [Aggiornamento dello stack di manutenzione](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Aggiornamento dello stack di manutenzione](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 2008 SP2** | [Aggiornamento dello stack di manutenzione](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Aggiornamento dello stack di manutenzione](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 7 SP1** | [Aggiornamento dello stack di manutenzione](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Aggiornamento dello stack di manutenzione](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419).



## <a name="updates-october-2019"></a>Aggiornamenti (2019 ottobre)

### <a name="update-rollup-41"></a>Aggiornamento cumulativo 41

L' [aggiornamento cumulativo 41](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornare** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamenti per Site Recovery agenti e provider (come descritto in dettaglio nel rollup)
**Correzioni di problemi/miglioramenti** | Una serie di correzioni e miglioramenti (come descritto in dettaglio nel rollup)



### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza per macchine virtuali di Azure

Le nuove funzionalità per il ripristino di emergenza delle macchine virtuali di Azure sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Impostazioni del failover di test** | Quando si configura un failover di test, è ora possibile configurare le impostazioni per la rete e la macchina virtuale del failover di test, tra cui indirizzo IP, NSG, bilanciamento del carico interno e indirizzo IP pubblico per ogni scheda di interfaccia di rete del computer. Queste impostazioni sono facoltative e non modificano il comportamento corrente. Se queste impostazioni non vengono configurate, è possibile scegliere una VNet di Azure al momento del failover di test. [Altre informazioni](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/).
**Piani di ripristino** | I piani di ripristino sono ora limitati alle VM 100, per garantire l'affidabilità del failover.

### <a name="vmware-to-azure-disaster-recovery"></a>Ripristino di emergenza da VMware ad Azure

Le nuove funzionalità per il ripristino di emergenza da VMware ad Azure sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Piani di ripristino** | I piani di ripristino sono ora limitati alle VM 100, per garantire l'affidabilità del failover.


## <a name="updates-september-2019"></a>Aggiornamenti (2019 settembre)

### <a name="update-rollup-40"></a>Aggiornamento cumulativo 40

L' [aggiornamento cumulativo 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornare** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamenti per Site Recovery agenti e provider (come descritto in dettaglio nel rollup)
**Correzioni di problemi/miglioramenti** | Una serie di correzioni e miglioramenti (come descritto in dettaglio nel rollup)




### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza per macchine virtuali di Azure

Le nuove funzionalità per il ripristino di emergenza delle macchine virtuali di Azure sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Pulitura dopo il failback** | Dopo aver eseguito il failover nell'area secondaria di Azure e dopo aver eseguito il failback nell'area primaria, Site Recovery pulisce automaticamente i computer nell'area secondaria. Non è necessario eliminare manualmente le macchine virtuali e le schede di rete.
**Il failover di test mantiene l'indirizzo IP** | È ora possibile mantenere l'indirizzo IP della VM di origine durante un'esercitazione sul ripristino di emergenza e selezionare un indirizzo IP statico per un failover di test.

### <a name="vmwarephysical-server-disaster-recovery"></a>Ripristino di emergenza di VMware/server fisici

Le funzionalità aggiunte a questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
Nuovi avvisi del server di elaborazione | Sono stati aggiunti nuovi avvisi del server di elaborazione. [Altre informazioni](vmware-physical-azure-monitor-process-server.md). 

### <a name="hyper-v-disaster-recovery"></a>Ripristino di emergenza di Hyper-V

Le funzionalità aggiunte a questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
Account di archiviazione | Site Recovery supporta ora l'uso di account di archiviazione con firewall abilitato per il ripristino di emergenza da Hyper-V ad Azure.  È possibile selezionare account di archiviazione abilitati per il firewall come account di destinazione o per l'archiviazione nella cache. Se si usa l'account abilitato per il firewall, assicurarsi di abilitare l'opzione per consentire i servizi Microsoft attendibili.<br/><br/> Questa operazione è supportata per le VM Hyper-V con o senza System Center VMM.


## <a name="updates-august-2019"></a>Aggiornamenti (2019 agosto)

### <a name="update-rollup-39"></a>Aggiornamento cumulativo 39

L' [aggiornamento cumulativo 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornare** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamenti per Site Recovery agenti e provider (come descritto in dettaglio nel rollup)
**Correzioni di problemi/miglioramenti** | Una serie di correzioni e miglioramenti (come descritto in dettaglio nel rollup)


### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza per macchine virtuali di Azure

Le nuove funzionalità per il ripristino di emergenza delle macchine virtuali di Azure sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Crittografia senza Azure AD** | La crittografia senza un'app Azure AD è ora supportata per la replica di macchine virtuali di Azure in dischi gestiti che eseguono Windows.
**Risorse di rete per il failover** | Quando si esegue il failover in un'altra area, è ora possibile alleghi le impostazioni delle risorse di rete (gruppi, bilanciamento del carico, indirizzo IP pubblico) a una macchina virtuale. 

## <a name="updates-july-2019"></a>Aggiornamenti (2019 luglio)

### <a name="update-rollup-38"></a>Aggiornamento cumulativo 38

L' [aggiornamento cumulativo 38](https://support.microsoft.com/help/4513507/) fornisce gli aggiornamenti seguenti.

**Aggiornare** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamenti per Site Recovery agenti e provider (come descritto in dettaglio nel rollup)
**Correzioni di problemi/miglioramenti** | Una serie di correzioni e miglioramenti (come descritto in dettaglio nel rollup)


### <a name="general"></a>General

Site Recovery supporta ora l'uso di account di archiviazione per utilizzo generico V2 per l'archiviazione della cache o l'archiviazione di destinazione. In precedenza era supportato solo V1.

### <a name="vmware-to-azure-disaster-recovery"></a>Ripristino di emergenza da VMware ad Azure

È ora possibile replicare dischi fino a 8 TB quando si esegue la replica in una macchina virtuale di Azure con dischi gestiti.


## <a name="updates-june-2019"></a>Aggiornamenti (2019 giugno)

### <a name="update-rollup-37"></a>Aggiornamento cumulativo 37

L' [aggiornamento cumulativo 37](https://support.microsoft.com/help/4508614/) fornisce gli aggiornamenti seguenti.

**Aggiornare** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamenti per Site Recovery agenti e provider (come descritto in dettaglio nel rollup)
**Correzioni di problemi/miglioramenti** | Una serie di correzioni e miglioramenti (come descritto in dettaglio nel rollup)


### <a name="vmwarephysical-server-disaster-recovery"></a>Ripristino di emergenza di VMware/server fisici

Le funzionalità aggiunte a questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Partizioni GPT** | Dall'aggiornamento cumulativo 37 in poi (servizio Mobility versione 9.25.5241.1), fino a cinque partizioni GPT sono supportate in UEFI. Prima di questo aggiornamento, quattro erano supportati.



## <a name="updates-may-2019"></a>Aggiornamenti (maggio 2019)

### <a name="update-rollup-36"></a>Aggiornamento cumulativo 36

L' [aggiornamento cumulativo 36](https://support.microsoft.com/help/4503156) fornisce gli aggiornamenti seguenti.

**Aggiornare** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamento per Site Recovery agenti e provider (come descritto in dettaglio nel rollup)
**Correzioni di problemi/miglioramenti** | Una serie di correzioni e miglioramenti (come descritto in dettaglio nel rollup)

### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza per macchine virtuali di Azure

Le funzionalità aggiunte a questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Replicare dischi aggiunti** | Abilitare la replica per i dischi dati aggiunti a una macchina virtuale di Azure già abilitata per il ripristino di emergenza. [Altre informazioni](azure-to-azure-enable-replication-added-disk.md).
**Aggiornamenti automatici** | Quando si configurano gli aggiornamenti automatici per l'estensione del servizio Mobility eseguita in macchine virtuali di Azure abilitate per il ripristino di emergenza, è ora possibile selezionare un account di automazione esistente da usare, invece di usare l'account predefinito creato da Site Recovery. [Altre informazioni](azure-to-azure-autoupdate.md).


### <a name="vmwarephysical-server-disaster-recovery"></a>Ripristino di emergenza di VMware/server fisici

Le funzionalità aggiunte a questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Monitoraggio del server di elaborazione** | Per il ripristino di emergenza di server fisici e VM VMware locali, monitorare e risolvere i problemi relativi al server di elaborazione con report e avvisi di integrità del server migliorati. [Altre informazioni](vmware-physical-azure-monitor-process-server.md). 





## <a name="updates-march-2019"></a>Aggiornamenti (2019 marzo)

### <a name="update-rollup-35"></a>Aggiornamento cumulativo 35

L' [aggiornamento cumulativo 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornare** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamento per Site Recovery agenti e provider (come descritto in dettaglio nel rollup)
**Correzioni di problemi/miglioramenti** | Una serie di correzioni e miglioramenti (come descritto in dettaglio nel rollup)

### <a name="vmwarephysical-server-disaster-recovery"></a>Ripristino di emergenza di VMware/server fisici

Le funzionalità aggiunte a questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Dischi gestiti** | La replica di server fisici e VM VMware locali è ora direttamente nei dischi gestiti in Azure. I dati locali vengono inviati a un account di archiviazione della cache in Azure e i punti di ripristino vengono creati nei dischi gestiti nel percorso di destinazione. Ciò garantisce che non sia necessario gestire più account di archiviazione di destinazione.
**Server di configurazione** | Site Recovery supporta ora i server di configurazione con più schede di rete. Aggiungere altri adapter alla VM del server di configurazione prima di registrare il server di configurazione nell'insieme di credenziali. Se si aggiunge in seguito, è necessario registrare di nuovo il server nell'insieme di credenziali.


## <a name="updates-february-2019"></a>Aggiornamenti (febbraio 2019)

### <a name="update-rollup-34"></a>Aggiornamento cumulativo 34 

L' [aggiornamento cumulativo 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornare** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamento per Site Recovery agenti e provider (come descritto in dettaglio nel rollup).
**Correzioni di problemi/miglioramenti** | Una serie di correzioni e miglioramenti (come descritto in dettaglio nel rollup).


### <a name="update-rollup-33"></a>Aggiornamento cumulativo 33 

L' [aggiornamento cumulativo 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornare** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamento per Site Recovery agenti e provider (come descritto in dettaglio nel rollup).
**Correzioni di problemi/miglioramenti** | Una serie di correzioni e miglioramenti (come descritto in dettaglio nel rollup).


### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza per macchine virtuali di Azure 
Le funzionalità aggiunte a questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Mapping di rete** | Per il ripristino di emergenza di macchine virtuali di Azure, è ora possibile usare qualsiasi rete di destinazione disponibile quando si Abilita la replica. 
**Standard SSD** | È ora possibile configurare il ripristino di emergenza per le macchine virtuali di Azure con [SDD standard dischi](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Spazi di archiviazione diretta** | È possibile configurare il ripristino di emergenza per le app in esecuzione in app VM di Azure usando [spazi di archiviazione diretta](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) per la disponibilità elevata.  L'uso di Spazi di archiviazione diretta (S2D) insieme a Site Recovery offre una protezione completa dei carichi di lavoro delle macchine virtuali di Azure. S2D consente di ospitare un cluster guest in Azure. Questa operazione è particolarmente utile quando una macchina virtuale ospita un'applicazione critica, ad esempio SAP ASC layer, SQL Server o file server con scalabilità orizzontale.


### <a name="vmwarephysical-server-disaster-recovery"></a>Ripristino di emergenza di VMware/server fisici
Le funzionalità aggiunte a questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**file system Linux BRTFS** | Site Recovery supporta ora la replica di macchine virtuali VMware con il file system BRTFS. La replica non è supportata se:<br/><br/>-Il sottovolume file system BTRFS viene modificato dopo l'abilitazione della replica.<br/><br/>-La file system viene distribuita su più dischi.<br/><br/>-Il file system BTRFS supporta RAID.
**Windows Server 2019** | Aggiunto il supporto per i computer che eseguono Windows Server 2019.


## <a name="updates-january-2019"></a>Aggiornamenti (2019 gennaio)


### <a name="accelerated-networking-azure-vms"></a>Rete accelerata (VM di Azure)

La funzionalità rete accelerata abilita Single Root I/O Virtualization (SR-IOV) in una macchina virtuale, migliorando le prestazioni di rete. Quando si abilita la replica per una macchina virtuale di Azure, Site Recovery rileva se è abilitata la rete accelerata. In caso affermativo, dopo il failover Site Recovery configura automaticamente la rete accelerata nella macchina virtuale di Azure di replica di destinazione, sia per [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) che per [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

[Altre informazioni](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>Aggiornamento cumulativo 32 

L' [aggiornamento cumulativo 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornare** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamento per Site Recovery agenti e provider (come descritto in dettaglio nel rollup).
**Correzioni di problemi/miglioramenti** | Una serie di correzioni e miglioramenti (come descritto in dettaglio nel rollup).

### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza per macchine virtuali di Azure

Le funzionalità aggiunte a questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto Linux** | È stato aggiunto il supporto per RedHat workstation 6/7 e le nuove versioni del kernel per Ubuntu, Debian e SUSE.
**Spazi di archiviazione diretta** | Site Recovery supporta macchine virtuali di Azure con Spazi di archiviazione diretta (S2D).

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>Ripristino di emergenza di macchine virtuali VMware/server fisici

Le funzionalità aggiunte a questo mese sono riepilogate nella tabella.
 
**Funzionalità** | **Dettagli**
--- | ---
**Supporto Linux** | È stato aggiunto il supporto per RedHat Enterprise Linux 7,6, RedHat workstation 6/7, Oracle Linux 6.10/7.6 e le nuove versioni del kernel per Ubuntu, Debian e SUSE.


### <a name="update-rollup-31"></a>Aggiornamento cumulativo 31 

L' [aggiornamento cumulativo 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornare** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamento per Site Recovery agenti e provider (come descritto in dettaglio nel rollup).
**Correzioni di problemi/miglioramenti** | Una serie di correzioni e miglioramenti (come descritto in dettaglio nel rollup).

### <a name="vmware-vmsphysical-servers-replication"></a>Replica di VM VMware/server fisici 
Le funzionalità aggiunte a questo mese sono riepilogate nella tabella.
**Funzionalità** | **Dettagli**
--- | ---
**Supporto Linux** | È stato aggiunto il supporto per Oracle Linux 6,8 e 6.9/7.0 e per il kernel UEK5.
**LVM** | Aggiunto il supporto per i volumi LVM e LVM2.<br/><br/> La directory/boot in una partizione disco e nei volumi LVM è ora supportata.
**Directories** | È stato aggiunto il supporto per queste directory configurate come partizioni separate o file System che non si trovano sullo stesso disco di sistema:<br/><br/> /(root), /boot, /usr, /usr/local, /var, /etc.
**Windows Server 2008** | Aggiunto il supporto per i dischi dinamici.
**Failover** | Miglioramento del tempo di failover per le macchine virtuali VMware in cui storvsc e vsbus non sono driver di avvio.
**Supporto UEFI** | Le macchine virtuali di Azure non supportano il tipo di avvio UEFI. È ora possibile eseguire la migrazione di server fisici locali con UEFI ad Azure con Site Recovery. Site Recovery esegue la migrazione del server convertendo il tipo di avvio in BIOS prima della migrazione. Site Recovery supportata in precedenza questa conversione solo per le macchine virtuali. Il supporto è disponibile per i server fisici che eseguono Windows Server 2012 o versioni successive.

### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza per macchine virtuali di Azure
Le funzionalità aggiunte a questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto Linux** | È stato aggiunto il supporto per Oracle Linux 6,8 e 6.9/7.0; e per il kernel UEK5.
**file system Linux BRTFS** | Supportato per le macchine virtuali di Azure.
**Macchine virtuali di Azure nelle zone di disponibilità** | È possibile abilitare la replica in un'altra area per le macchine virtuali di Azure distribuite in zone di disponibilità. È ora possibile abilitare la replica per una macchina virtuale di Azure e impostare la destinazione per il failover su una singola istanza di macchina virtuale, una macchina virtuale in un set di disponibilità o una macchina virtuale in una zona di disponibilità. L'impostazione non influisce sulla replica. [Leggere](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) l'annuncio.
**Archiviazione abilitata al firewall (portale/PowerShell)** | Aggiunto il supporto per gli [account di archiviazione abilitati](https://docs.microsoft.com/azure/storage/common/storage-network-security)per il firewall.<br/><br/> È possibile replicare le macchine virtuali di Azure con dischi non gestiti in account di archiviazione abilitati per il firewall in un'altra area di Azure per il ripristino di emergenza.<br/><br/> È possibile usare account di archiviazione abilitati per il firewall come account di archiviazione di destinazione per i dischi non gestiti.<br/><br/> Supportato nel portale e tramite PowerShell.

## <a name="updates-december-2018"></a>Aggiornamenti (2018 dicembre)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Aggiornamenti automatici per il servizio Mobility (VM di Azure)

In Site Recovery è stata aggiunta un'opzione per gli aggiornamenti automatici per l'estensione del servizio Mobility. L'estensione del servizio Mobility viene installata in ogni macchina virtuale di Azure replicata da Site Recovery. Quando si abilita la replica, occorre selezionare se si vuole consentire a Site Recovery di gestire gli aggiornamenti per l'estensione.

Gli aggiornamenti non richiedono un riavvio delle macchine virtuali e non influiscono sulla replica. [Altre informazioni](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Calcolatore dei prezzi per il ripristino di emergenza delle macchine virtuali di Azure

Il ripristino di emergenza di macchine virtuali di Azure comporta costi di licenza delle VM, costi di rete e di archiviazione. Azure offre un [calcolatore dei prezzi](https://aka.ms/a2a-cost-estimator) per stimare questi costi. Site Recovery offre ora una [stima dei prezzi di esempio](https://aka.ms/a2a-cost-estimator) per una distribuzione di esempio basata su un'app a tre livelli con sei macchine virtuali con 12 dischi HDD Standard e 6 dischi SSD Premium.

- Nell'esempio si presuppone una modifica dei dati di 10 GB al giorno per il livello standard e 20 GB per Premium.
- È possibile modificare le variabili per stimare i costi per una distribuzione specifica.
- È possibile specificare il numero di macchine virtuali, il numero e il tipo di dischi gestiti e la frequenza di modifica dei dati prevista per tutte le macchine virtuali.
- È anche possibile applicare un fattore di compressione per stimare i costi della larghezza di banda.

[Leggere](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) l'annuncio.


## <a name="updates-october-2018"></a>Aggiornamenti (2018 ottobre)

### <a name="update-rollup-30"></a>Aggiornamento cumulativo 30 

L' [aggiornamento cumulativo 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) fornisce gli aggiornamenti seguenti.

**Aggiornare** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamento per Site Recovery agenti e provider (come descritto in dettaglio nel rollup).
**Correzioni di problemi/miglioramenti** | Una serie di correzioni e miglioramenti (come descritto in dettaglio nel rollup).

### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza per macchine virtuali di Azure
Le funzionalità aggiunte a questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto per aree** | È stato aggiunto il supporto Site Recovery per l'Australia centrale 1 e l'Australia centrale 2.
**Supporto per la crittografia del disco** | Aggiunto il supporto per il ripristino di emergenza di VM di Azure crittografate con crittografia dischi di Azure (ADE) con l'app Azure AD. [Altre informazioni](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Esclusione disco** | I dischi non inizializzati vengono ora esclusi automaticamente durante la replica delle macchine virtuali di Azure.
**Archiviazione abilitata al firewall (PowerShell)** | Aggiunto il supporto per gli [account di archiviazione abilitati](https://docs.microsoft.com/azure/storage/common/storage-network-security)per il firewall.<br/><br/> È possibile replicare le macchine virtuali di Azure con dischi non gestiti in account di archiviazione abilitati per il firewall in un'altra area di Azure per il ripristino di emergenza.<br/><br/> È possibile usare account di archiviazione abilitati per il firewall come account di archiviazione di destinazione per i dischi non gestiti.<br/><br/> Supportato solo con PowerShell.


### <a name="update-rollup-29"></a>Aggiornamento cumulativo 29 

L' [aggiornamento cumulativo 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornare** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamento per Site Recovery agenti e provider (come descritto in dettaglio nel rollup).
**Correzioni di problemi/miglioramenti** | Una serie di correzioni e miglioramenti (come descritto in dettaglio nel rollup).


## <a name="updates-august-2018"></a>Aggiornamenti (2018 agosto)

### <a name="update-rollup-28"></a>Aggiornamento cumulativo 28 

L' [aggiornamento cumulativo 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornare** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamento per Site Recovery agenti e provider (come descritto in dettaglio nel rollup).
**Correzioni di problemi/miglioramenti** | Una serie di correzioni e miglioramenti (come descritto in dettaglio nel rollup).

### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza per macchine virtuali di Azure 
Le funzionalità aggiunte a questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto Linux** | Aggiunta supportata per RedHat Enterprise Linux 6,10; CentOS 6,10.<br/><br/>
**Supporto Cloud** | Ripristino di emergenza supportato per le macchine virtuali di Azure nel cloud Germania.
**Ripristino di emergenza tra sottoscrizioni** | Supporto per la replica di VM di Azure in un'area in un'altra area in una sottoscrizione diversa, all'interno dello stesso tenant Azure Active Directory. [Altre informazioni](https://aka.ms/cross-sub-blog).

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Ripristino di emergenza di macchine virtuali VMware/server fisici 
Le funzionalità aggiunte a questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto Linux** | È stato aggiunto il supporto per RedHat Enterprise Linux 6,10, CentOS 6,10.<br/><br/> Sono ora supportate le macchine virtuali basate su Linux che usano lo stile di partizione GPT (tabella di partizione GUID) in modalità di compatibilità BIOS legacy. Per ulteriori informazioni, vedere le [domande frequenti sulle VM di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) . 
**Ripristino di emergenza per le VM dopo la migrazione** | Supporto per l'abilitazione del ripristino di emergenza in un'area secondaria per una VM VMware locale migrata in Azure, senza la necessità di disinstallare il servizio Mobility nella macchina virtuale prima di abilitare la replica.
**Windows Server 2008** | Supporto per la migrazione di computer che eseguono Windows Server 2008 R2/2008 a 64 bit e 32 bit.<br/><br/> Solo migrazione (replica e failover). Il failback non è supportato.

## <a name="updates-july-2018"></a>Aggiornamenti (2018 luglio)

### <a name="update-rollup-27-july-2018"></a>Aggiornamento cumulativo 27 (2018 luglio)

L' [aggiornamento cumulativo 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornare** | **Dettagli**
--- | ---
**Provider e agenti** | Aggiornamento per Site Recovery agenti e provider (come descritto in dettaglio nel rollup).
**Correzioni di problemi/miglioramenti** | Una serie di correzioni e miglioramenti (come descritto in dettaglio nel rollup).

### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza per macchine virtuali di Azure 

Le funzionalità aggiunte a questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto Linux** | È stato aggiunto il supporto per Red Hat Enterprise Linux 7,5.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Ripristino di emergenza di macchine virtuali VMware/server fisici 

Le funzionalità aggiunte a questo mese sono riepilogate nella tabella.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto Linux** | È stato aggiunto il supporto per Red Hat Enterprise Linux 7,5, SUSE Linux Enterprise Server 12.



## <a name="next-steps"></a>Passaggi successivi

Mantenersi aggiornati nella pagina [Aggiornamenti di Azure](https://azure.microsoft.com/updates/?product=site-recovery).
