---
title: Supporto per la migrazione di VMware in Azure MigrateSupport for VMware migration in Azure Migrate
description: Informazioni sul supporto per la migrazione delle macchine virtuali VMware in Azure Migrate.Learn about support for VMware VM migration in Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: eee16b244ae4f9d517bdd42a0b7f37b1494ac480
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538138"
---
# <a name="support-matrix-for-vmware-migration"></a>Matrice di supporto per la migrazione VMwareSupport matrix for VMware migration

Questo articolo riepiloga le impostazioni di supporto e le limitazioni per la migrazione delle macchine virtuali VMware con [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) . Per informazioni sulla valutazione delle macchine virtuali VMware per la migrazione ad Azure, esaminare la matrice di supporto della [valutazione.](migrate-support-matrix-vmware.md)


## <a name="migration-options"></a>Opzioni di migrazione

È possibile eseguire la migrazione delle macchine virtuali VMware in due modi:You can migrate VMware VMware VMs in a couple ways:

- Con la migrazione senza agente: eseguire la migrazione delle macchine virtuali senza dover installare nulla su di esse. Distribuire [l'appliance di Azure Migrate](migrate-appliance.md) per la migrazione senza agenti.
- Con la migrazione basata su agente: installare un agente nella macchina virtuale per la replica. Per la migrazione basata su agenti, è necessario distribuire [un'appliance](migrate-replication-appliance.md)di replica.

Leggere [questo articolo](server-migrate-overview.md) per capire quale metodo si desidera utilizzare.

## <a name="migration-limitations"></a>Limitazioni della migrazione

- È possibile selezionare fino a 10 macchine virtuali contemporaneamente per la replica. Se si desidera eseguire la migrazione di più computer, eseguire la replica in gruppi di 10.If you want to migrate more machines, then replicate in groups of 10.
- Per la migrazione senza agente VMware, è possibile eseguire fino a 100 repliche simultanee.

## <a name="agentless-vmware-servers"></a>Server Agentless-VMware

**VMware** | **Dettagli**
--- | ---
**VMware vCenter Server** | Versione 5.5, 6.0, 6.5 o 6.7.
**VMware vSphere ESXI host** | Versione 5.5, 6.0, 6.5 o 6.7.
**Autorizzazioni del server vCenter** | La migrazione senza agente utilizza [l'appliance Migrate](migrate-appliance.md). L'appliance necessita di queste autorizzazioni:<br/><br/> - **Datastore.Browse:** consente l'esplorazione dei file di log della macchina virtuale per la creazione e l'eliminazione dello snapshot.<br/><br/> - **Datastore.LowLevelFileOperations**: consente le operazioni di lettura/scrittura/eliminazione/ridenominazione nel browser dell'archivio dati per la risoluzione dei problemi relativi alla creazione e all'eliminazione di snapshot.<br/><br/> - **VirtualMachine.Configuration.DiskChangeTracking:** consente di abilitare o disabilitare il rilevamento delle modifiche dei dischi delle macchine virtuali per eseguire il pull di blocchi di dati modificati tra snapshot.<br/><br/> - **VirtualMachine.Configuration.DiskLease:** consente alle operazioni di lease del disco per una macchina virtuale di leggere il disco utilizzando VMware vSphere Virtual Disk Development Kit (VDDK).<br/><br/> - **VirtualMachine.Provisioning.AllowDiskAccess**: (in particolare per vSphere 6.0 e versioni successive) Consentire l'apertura di un disco in una macchina virtuale per l'accesso in lettura casuale sul disco tramite VDDK.<br/><br/> - **VirtualMachine.Provisioning.AllowReadOnlyDiskAccess:** consente l'apertura di un disco in una macchina virtuale per leggere il disco utilizzando VDDK.<br/><br/> - **VirtualMachine.Provisioning.AllowDiskRandomAccess:** consente l'apertura di un disco in una macchina virtuale per leggere il disco utilizzando VDDK.<br/><br/> - **VirtualMachine.Provisioning.AllowVirtualMachineDownload:** consente operazioni di lettura sui file associati a una macchina virtuale, per scaricare i log e risolvere i problemi se si verifica un errore.<br/><br/> - Per la replica, è possibile consentire la creazione e la gestione di snapshot di macchine virtuali per la replica.<br/><br/> - Virtual Machine.Interaction.Power Off: consente di spegnere la macchina virtuale durante la migrazione ad **Azure.Virtual Machine.Interaction.Power Off**: Allow the VM to be spent during migration to Azure.



## <a name="agentless-vmware-vms"></a>Macchine virtuali VMware senza agenteAgentless-VMware VMware VMs

**Supporto** | **Dettagli**
--- | ---
**Sistemi operativi supportati** | [I](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) sistemi operativi Windows e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) supportati da Azure possono essere migrati usando la migrazione senza agente.
**Modifiche necessarie per AzureRequired changes for Azure** | Alcune macchine virtuali potrebbero richiedere alcune modifiche per la corretta esecuzione in Azure. Azure Migrate apporta automaticamente queste modifiche per i sistemi operativi seguenti:Azure Migrate makes these changes automatically for the following operating systems:<br/> - Red Hat Enterprise Linux 6.5, 7.0<br/> - CentOS 6,5</br> - SUSE Linux Enterprise Server 12 SP1<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7, 8<br/><br/> Per altri sistemi operativi, è necessario apportare le modifiche manualmente prima della migrazione. Gli articoli pertinenti contengono istruzioni su come eseguire questa operazione.
**Avvio Linux** | Se /boot si trova in una partizione dedicata, deve risiedere sul disco del sistema operativo e non essere distribuito su più dischi.<br/> Se /boot fa parte della partizione radice (/), la partizione '/' deve trovarsi sul disco del sistema operativo e non estendersi su altri dischi.
**Stivale UEFI** | Le macchine virtuali con avvio UEFI non sono supportate per la migrazione.
**Dimensioni disco** | Disco oS da 2 TB; 8 TB per i dischi dati.
**Limiti del disco** |  Fino a 60 dischi per macchina virtuale.
**Dischi/volumi crittografati** | Le macchine virtuali con dischi/volumi crittografati non sono supportate per la migrazione.
**Cluster di dischi condivisi** | Non supportato.
**Dischi indipendenti** | Non supportato.
**Dischi RDM/passthrough** | Se le macchine virtuali dispongono di dischi RDM o pass-through, questi dischi non verranno replicati in Azure.If VMs have RDM or passthrough disks, these disks won't be replicated to Azure.
**NFS** | I volumi NFS montati come volumi nelle macchine virtuali non verranno replicati.
**Obiettivi iSCSI** | Le macchine virtuali con destinazioni iSCSI non sono supportate per la migrazione senza agente.
**I/O multipercorso** | Non supportato.
**Archiviazione vMotion** | Non supportato. La replica non funzionerà se una macchina virtuale usa vMotion di archiviazione.
**Schede di rete in team** | Non supportato.
**IPv6** | Non supportato.
**Disco di destinazione** | Le macchine virtuali possono essere migrate solo su dischi gestiti (HDD standard, SSD premium) in Azure.VMs can only be migrated to managed disks (standard HDD, premium SSD) in Azure.
**Replica simultanea** | 100 macchine virtuali per vCenter Server. Se ne hai di più, eseguila la migrazione in batch di 100.If you have more, migrate them in batches of 100.


## <a name="agentless-azure-migrate-appliance"></a>Appliance Agentless-Azure Migrate Agentless-Azure Migrate appliance 

La migrazione senza agente usa [l'appliance di Azure Migrate.](migrate-appliance.md) È possibile distribuire l'appliance come VMWare VMWare usando un modello OVA, importato in vCenter Server o uno script di [PowerShell.](deploy-appliance-script.md)

- Informazioni sui [requisiti dell'appliance](migrate-appliance.md#appliance---vmware) per VMware.
- Informazioni sugli URL a cui l'appliance deve accedere nei cloud [pubblici](migrate-appliance.md#public-cloud-urls) e [governativi.](migrate-appliance.md#government-cloud-urls)
- In Azure per enti pubblici è necessario distribuire l'appliance usando lo script.

## <a name="agentless-ports"></a>Porte senza agente

**Dispositivo** | **Connessione**
--- | ---
Elettrodomestici | Connessioni in uscita sulla porta 443 per caricare i dati replicati in Azure e per comunicare con i servizi di Azure Migrate che orchestrano la replica e la migrazione.
Server vCenter | Connessioni in ingresso sulla porta 443 per consentire all'appliance di orchestrare la replica: creare snapshot, copiare dati, rilasciare snapshot
Host vSphere/EXSI | In ingresso sulla porta TCP 902 per l'appliance per replicare i dati dagli snapshot.


## <a name="agent-based-vmware-servers"></a>Server VMware basati su agenti
In questa tabella sono riepilogati il supporto di valutazione e le limitazioni per i server di virtualizzazione VMware.

**Requisiti di VMware** | **Dettagli**
--- | ---
**VMware vCenter Server** | Versione 5.5, 6.0, 6.5 o 6.7.
**VMware vSphere ESXI host** | Versione 5.5, 6.0, 6.5 o 6.7.
**Autorizzazioni del server vCenter** | Account di sola lettura per vCenter Server.

## <a name="agent-based-vmware-vms"></a>Macchine virtuali VMware basate su agenteAgent-based-VMware VMs

La tabella riepiloga il supporto delle macchine virtuali VMware per le macchine virtuali VMware di cui si vuole eseguire la migrazione tramite la migrazione basata su agenti.

**Supporto** | **Dettagli**
--- | ---
**Carico di lavoro del computer** | Azure Migrate supporta la migrazione di qualsiasi carico di lavoro (ad esempio Active Directory, SQL Server e così via) in esecuzione in un computer supportato.
**Sistemi operativi** | Per informazioni aggiornate, esaminare il [supporto del sistema operativo](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) per Site Recovery. Azure Migrate offre un supporto identico per il sistema operativo vm.
**File system Linux/archiviazione guest** | Per le informazioni più recenti, esaminare il supporto del [file system Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) per Site Recovery. Azure Migrate ha il supporto del file system Linux identico.
**Rete/Archiviazione** | Per informazioni aggiornate, esaminare i prerequisiti di [rete](../site-recovery/vmware-physical-azure-support-matrix.md#network) e [archiviazione](../site-recovery/vmware-physical-azure-support-matrix.md#storage) per Site Recovery. Azure Migrate offre requisiti di rete/archiviazione identici.
**Requisiti di Azure** | Per informazioni aggiornate, esaminare la rete di [Azure,](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)i requisiti [di archiviazione](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)e [di calcolo](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) per Site Recovery. Azure Migrate ha requisiti identici per la migrazione di VMware.Azure Migrate has identical requirements for VMware migration.
**Servizio Mobility** | L'agente del servizio Mobility deve essere installato in ogni macchina virtuale di cui si vuole eseguire la migrazione.
**Stivale UEFI** | La macchina virtuale migrata in Azure verrà convertita automaticamente in una macchina virtuale di avvio del BIOS.<br/><br/> Il disco del sistema operativo deve avere fino a quattro partizioni e i volumi devono essere formattati con NTFS.
**Disco di destinazione** | Le macchine virtuali possono essere migrate solo su dischi gestiti (HDD standard, SSD premium) in Azure.VMs can only be migrated to managed disks (standard HDD, premium SSD) in Azure.
**Dimensioni disco** | Disco oS da 2 TB; 8 TB per i dischi dati.
**Limiti del disco** |  Fino a 63 dischi per macchina virtuale.
**Dischi/volumi crittografati** | Le macchine virtuali con dischi/volumi crittografati non sono supportate per la migrazione.
**Cluster di dischi condivisi** | Non supportato.
**Dischi indipendenti** | Supportato.
**Dischi pass-through** | Supportato.
**NFS** | I volumi NFS montati come volumi nelle macchine virtuali non verranno replicati.
**Obiettivi iSCSI** | Le macchine virtuali con destinazioni iSCSI non sono supportate per la migrazione senza agente.
**I/O multipercorso** | Non supportato.
**Archiviazione vMotion** | Supportato
**Schede di rete in team** | Non supportato.
**IPv6** | Non supportato.




## <a name="agent-based-replication-appliance"></a>Appliance di replica basata su agenti 

Quando si configura l'appliance di replica utilizzando il modello OVA fornito nell'hub di Azure Migrate, l'appliance esegue Windows Server 2016 ed è conforme ai requisiti di supporto. Se si configura l'appliance di replica manualmente in un server fisico, assicurarsi che sia conforme ai requisiti.

- Informazioni sui [requisiti dell'appliance](migrate-replication-appliance.md#appliance-requirements) di replica per VMware.
- MySQL deve essere installato nell'appliance. Ulteriori informazioni sulle opzioni di [installazione](migrate-replication-appliance.md#mysql-installation).
- Informazioni sugli URL a cui l'appliance di replica deve accedere nei cloud [pubblici](migrate-replication-appliance.md#url-access) e [governativi.](migrate-replication-appliance.md#azure-government-url-access)
- Esaminare le [porte](migrate-replication-appliance.md#port-access) a cui l'appliance di replica deve accedere.

## <a name="agent-based-ports"></a>Porte basate su agenti

**Dispositivo** | **Connessione**
--- | ---
VM | Il servizio Mobility in esecuzione nelle macchine virtuali comunica con l'appliance di replica locale (server di configurazione) sulla porta HTTPS 443 in ingresso, per la gestione della replica.<br/><br/> Le macchine virtuali inviano i dati della replica al server di elaborazione (in esecuzione sul computer del server di configurazione) sulla porta HTTPS 9443 in ingresso. La porta può essere modificata.
Appliance di replica | L'appliance di replica orchestra la replica con Azure sulla porta HTTPS 443 in uscita.
Server di elaborazione | Il server di elaborazione riceve i dati di replica, li ottimizza e li crittografa e li invia all'archiviazione di Azure tramite la porta 443 in uscita.<br/> Per impostazione predefinita, il server di elaborazione viene eseguito nell'appliance di replica.

## <a name="azure-vm-requirements"></a>Requisiti per le VM di Azure

Tutte le macchine virtuali locali replicate in Azure devono soddisfare i requisiti delle macchine virtuali di Azure riepilogati in questa tabella. Quando Site Recovery esegue un controllo dei prerequisiti per la replica, il controllo avrà esito negativo se alcuni dei requisiti non vengono soddisfatti.

**Componente** | **Requisiti** | **Dettagli**
--- | --- | ---
Sistema operativo guest | Verifica i sistemi operativi VMware VM supportati per la migrazione.<br/> È possibile eseguire la migrazione di qualsiasi carico di lavoro in esecuzione in un sistema operativo supportato. | Il controllo ha esito negativo se non supportato.
Architettura del sistema operativo guest | 64 bit. | Il controllo ha esito negativo se non supportato.
Dimensioni disco del sistema operativo | Fino a 2.048 GB. | Il controllo ha esito negativo se non supportato.
Conteggio dischi del sistema operativo | 1 | Il controllo ha esito negativo se non supportato.
Conteggio dischi dati | 64 o un numero inferiore. | Il controllo ha esito negativo se non supportato.
Dimensioni del disco dati | Fino a 4.095 GB | Il controllo ha esito negativo se non supportato.
Schede di rete | Sono supportate più schede. |
VHD condiviso | Non supportato. | Il controllo ha esito negativo se non supportato.
Disco FC | Non supportato. | Il controllo ha esito negativo se non supportato.
BitLocker | Non supportato. | Prima di abilitare la replica per un computer, occorre disabilitare BitLocker.
Nome della VM. | Da 1 a 63 caratteri.<br/> Limitato a lettere, numeri e trattini.<br/><br/> Il nome del computer deve iniziare e terminare con una lettera o un numero. |  Aggiornare il valore nelle proprietà del computer in Site Recovery.
Connettersi dopo la migrazione di Windows | Per connettersi alle macchine virtuali di Azure che eseguono Windows dopo la migrazione:To connect to Azure VMs running Windows after migration:<br/> - Prima che la migrazione abiliti RDP nella macchina virtuale locale.- Before migration enables RDP on the on-premises VM. Assicurarsi che le regole TCP e UDP vengano aggiunte per il profilo **pubblico** e che RDP sia consentito in **Windows Firewall** > **Allowed Apps**per tutti i profili.<br/> Per l'accesso VPN da sito a sito, abilitare RDP e consentire RDP in **Windows Firewall** -> **Allowed apps and features** for Domain **and Private** networks. Verificare inoltre che il criterio SAN del sistema operativo sia impostato su **OnlineAll**. [Altre informazioni](prepare-for-migration.md) |
Connessione dopo la migrazione Linux | To connect to Azure VMs after migration using SSH:<br/> Prima della migrazione, nel computer locale, verificare che il servizio Secure Shell sia impostato su Avvia e che le regole del firewall consentano una connessione SSH.<br/> Dopo il failover, nella macchina virtuale di Azure, consentire le connessioni in ingresso alla porta SSH per le regole del gruppo di sicurezza di rete nella macchina virtuale di cui è stato eseguito il failover e per la subnet di Azure a cui è connessa. Aggiungere inoltre un indirizzo IP pubblico per la macchina virtuale. |  


## <a name="next-steps"></a>Passaggi successivi

[Selezionare](server-migrate-overview.md) un'opzione di migrazione VMware.
