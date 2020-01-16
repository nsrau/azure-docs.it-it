---
title: Supporto per la migrazione di VMware in Azure Migrate
description: Informazioni sul supporto per la migrazione di macchine virtuali VMware in Azure Migrate.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: e33811563063c0f8eb94b9927d07596d51cd45e4
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030224"
---
# <a name="support-matrix-for-vmware-migration"></a>Matrice di supporto per la migrazione a VMware

Questo articolo riepiloga le impostazioni di supporto e le limitazioni per la migrazione di macchine virtuali VMware con [Azure migrate: migrazione del server](migrate-services-overview.md#azure-migrate-server-migration-tool) . Per informazioni sulla valutazione delle macchine virtuali VMware per la migrazione ad Azure, vedere la matrice di [supporto](migrate-support-matrix-vmware.md)per la valutazione.


## <a name="migration-options"></a>Opzioni di migrazione

È possibile eseguire la migrazione di macchine virtuali VMware in due modi:

- Con la migrazione senza agente: eseguire la migrazione di macchine virtuali senza dover installare alcun elemento. Si distribuisce il [dispositivo Azure migrate](migrate-appliance.md) per la migrazione senza agenti.
- Con la migrazione basata su agenti: installare un agente nella macchina virtuale per la replica. Per la migrazione basata su agenti, è necessario distribuire un [appliance di replica](migrate-replication-appliance.md).

Leggere [questo articolo](server-migrate-overview.md) per determinare il metodo che si vuole usare.

## <a name="migration-limitations"></a>Limitazioni della migrazione

- È possibile selezionare fino a 10 macchine virtuali contemporaneamente per la replica. Se si desidera eseguire la migrazione di più computer, eseguire la replica in gruppi di 10.
- Per la migrazione senza agente VMware, è possibile eseguire fino a 100 repliche simultanee.

## <a name="agentless-vmware-servers"></a>Server VMware senza agenti

**VMware** | **Dettagli**
--- | ---
**server VMware vCenter** | Versione 5,5, 6,0, 6,5 o 6,7.
**VMware vSphere Host ESXI** | Versione 5,5, 6,0, 6,5 o 6,7.
**autorizzazioni server vCenter** | La migrazione senza agenti usa l' [appliance di migrazione](migrate-appliance.md). Il dispositivo richiede le autorizzazioni seguenti:<br/><br/> - **datastore. browse**: consente l'esplorazione dei file di log della macchina virtuale per risolvere i problemi di creazione ed eliminazione di snapshot.<br/><br/> **Datastore. LowLevelFileOperations**: consente operazioni di lettura/scrittura/eliminazione/ridenominazione nel browser dell'archivio dati per risolvere i problemi di creazione ed eliminazione di snapshot.<br/><br/> - **virtualmachine. Configuration. DiskChangeTracking**: consente di abilitare o disabilitare il rilevamento delle modifiche dei dischi delle macchine virtuali per eseguire il pull dei blocchi di dati modificati tra gli snapshot.<br/><br/> - **virtualmachine. Configuration. DiskLease**: consente le operazioni di lease del disco per una macchina virtuale, per leggere il disco utilizzando VMware vSphere Virtual Disk Development Kit (VDDK).<br/><br/> - **virtualmachine. provisioning. AllowReadOnlyDiskAccess**: consente di aprire un disco in una macchina virtuale per leggere il disco usando VDDK.<br/><br/> - **virtualmachine. provisioning. AllowVirtualMachineDownload**: consente operazioni di lettura nei file associati a una macchina virtuale, per scaricare i log e risolvere eventuali errori.<br/><br/> -* * VirtualMachine. SnapshotManagement. * * *: consente la creazione e la gestione di snapshot VM per la replica.<br/><br/> - **Virtual Machine. Interaction.** spegnimento: consente di spegnere la macchina virtuale durante la migrazione ad Azure.



## <a name="agentless-vmware-vms"></a>Macchine virtuali VMware senza agente

**Supporto** | **Dettagli**
--- | ---
**Sistemi operativi supportati** | È possibile eseguire la migrazione dei sistemi operativi [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) supportati da Azure con la migrazione senza agenti.
**Modifiche necessarie per Azure** | Alcune macchine virtuali potrebbero richiedere alcune modifiche per la corretta esecuzione in Azure. Azure Migrate apporta automaticamente queste modifiche per i sistemi operativi seguenti:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> Per altri sistemi operativi, è necessario apportare modifiche manualmente prima della migrazione. Gli articoli pertinenti contengono istruzioni su come eseguire questa operazione.
**Avvio di Linux** | Se/boot si trova in una partizione dedicata, deve risiedere nel disco del sistema operativo e non essere distribuito tra più dischi.<br/> Se/boot fa parte della partizione radice (/), la partizione '/' deve trovarsi nel disco del sistema operativo e non si estende su altri dischi.
**Avvio UEFI** | Le macchine virtuali con avvio UEFI non sono supportate per la migrazione.
**Dimensioni disco** | disco del sistema operativo da 2 TB; 4 TB per i dischi dati.
**Limiti del disco** |  Fino a 60 dischi per macchina virtuale.
**Dischi/volumi crittografati** | Le macchine virtuali con dischi/volumi crittografati non sono supportate per la migrazione.
**Cluster di dischi condivisi** | Non supportato.
**Dischi indipendenti** | Non supportato.
**Dischi RDM/passthrough** | Se le VM contengono dischi RDM o passthrough, questi dischi non verranno replicati in Azure.
**NFS** | I volumi NFS montati come volumi nelle macchine virtuali non verranno replicati.
**destinazioni iSCSI** | Le macchine virtuali con destinazioni iSCSI non sono supportate per la migrazione senza agenti.
**I/o a percorsi multipli** | Non supportato.
**Storage vMotion** | Non supportato. La replica non funziona se una macchina virtuale usa Storage vMotion.
**NIC raggruppate** | Non supportato.
**IPv6** | Non supportato.
**Disco di destinazione** | È possibile eseguire la migrazione delle macchine virtuali solo a Managed Disks (standard HDD, unità SSD Premium) in Azure.
**Replica simultanea** | 100 VM per ogni server vCenter. Se si dispone di altro, eseguirne la migrazione in batch di 100.


## <a name="agentless-azure-migrate-appliance"></a>Appliance senza agenti Azure Migrate 
La migrazione senza agente usa il dispositivo Azure Migrate, distribuito in una VM VMware.

- Informazioni sui [requisiti degli appliance](migrate-appliance.md#appliance---vmware) per VMware.
- Informazioni sugli [URL](migrate-appliance.md#url-access) a cui l'appliance deve accedere.

## <a name="agentless-ports"></a>Porte senza agenti

**Dispositivo** | **Connection**
--- | ---
Elettrodomestici | Connessioni in uscita sulla porta 443 per caricare i dati replicati in Azure e per comunicare con Azure Migrate Services che orchestrano la replica e la migrazione.
Server vCenter | Connessioni in ingresso sulla porta 443 per consentire all'appliance di orchestrare la replica: creare snapshot, copiare dati, rilasciare snapshot
host vSphere/EXSI | In ingresso sulla porta TCP 902 per l'appliance per replicare i dati dagli snapshot.


## <a name="agent-based-vmware-servers"></a>Server VMware basati su agenti
Questa tabella riepiloga il supporto per la valutazione e le limitazioni per i server di virtualizzazione VMware.

**Requisiti di VMware** | **Dettagli**
--- | ---
**server VMware vCenter** | Versione 5,5, 6,0, 6,5 o 6,7.
**VMware vSphere Host ESXI** | Versione 5,5, 6,0, 6,5 o 6,7.
**autorizzazioni server vCenter** | Account di sola lettura per server vCenter.

## <a name="agent-based-vmware-vms"></a>Macchine virtuali VMware basate su agenti

La tabella riepiloga il supporto delle VM VMware per le macchine virtuali VMware di cui si vuole eseguire la migrazione tramite la migrazione basata su agenti.

**Supporto** | **Dettagli**
--- | ---
**Carico di lavoro del computer** | Azure Migrate supporta la migrazione di qualsiasi carico di lavoro (ad indicare Active Directory, SQL Server e così via) in esecuzione in un computer supportato.
**Sistemi operativi** | Per informazioni aggiornate, vedere il [supporto del sistema operativo](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) per Site Recovery. Azure Migrate offre un supporto identico per il sistema operativo della macchina virtuale.
**File system Linux/archiviazione Guest** | Per informazioni aggiornate, vedere il [supporto di Linux file System](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) per Site Recovery. Azure Migrate dispone di un supporto di Linux file system identico.
**Rete/archiviazione** | Per le informazioni più aggiornate, esaminare i prerequisiti di [rete](../site-recovery/vmware-physical-azure-support-matrix.md#network) e di [archiviazione](../site-recovery/vmware-physical-azure-support-matrix.md#storage) per Site Recovery. Azure Migrate fornisce requisiti di rete/archiviazione identici.
**Requisiti di Azure** | Per informazioni aggiornate, vedere i requisiti di rete, [archiviazione](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)e [calcolo](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) di [Azure](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)per Site Recovery. Azure Migrate presenta requisiti identici per la migrazione di VMware.
**Servizio Mobility** | L'agente del servizio Mobility deve essere installato in ogni macchina virtuale di cui si vuole eseguire la migrazione.
**Avvio UEFI** | La macchina virtuale di cui è stata eseguita la migrazione in Azure verrà automaticamente convertita in una macchina virtuale di avvio BIOS.<br/><br/> Il disco del sistema operativo deve avere fino a quattro partizioni e i volumi devono essere formattati con NTFS.
**Disco di destinazione** | È possibile eseguire la migrazione delle macchine virtuali solo a Managed Disks (standard HDD, unità SSD Premium) in Azure.
**Dimensioni disco** | disco del sistema operativo da 2 TB; 8 TB per i dischi dati.
**Limiti del disco** |  Fino a 63 dischi per macchina virtuale.
**Dischi/volumi crittografati** | Le macchine virtuali con dischi/volumi crittografati non sono supportate per la migrazione.
**Cluster di dischi condivisi** | Non supportato.
**Dischi indipendenti** | Supportato.
**Dischi pass-through** | Supportato.
**NFS** | I volumi NFS montati come volumi nelle macchine virtuali non verranno replicati.
**destinazioni iSCSI** | Le macchine virtuali con destinazioni iSCSI non sono supportate per la migrazione senza agenti.
**I/o a percorsi multipli** | Non supportato.
**Storage vMotion** | Supportato
**NIC raggruppate** | Non supportato.
**IPv6** | Non supportato.




## <a name="agent-based-replication-appliance"></a>Appliance di replica basata su agenti 

Quando si configura l'appliance di replica usando il modello OVA fornito nell'hub Azure Migrate, l'Appliance esegue Windows Server 2016 e soddisfa i requisiti di supporto. Se si configura manualmente l'appliance di replica in un server fisico, assicurarsi che sia conforme ai requisiti.

- Informazioni sui [requisiti del dispositivo di replica](migrate-replication-appliance.md#appliance-requirements) per VMware.
- MySQL deve essere installato nell'appliance. Informazioni sulle [Opzioni di installazione](migrate-replication-appliance.md#mysql-installation).
- Informazioni sugli [URL](migrate-replication-appliance.md#url-access) a cui l'appliance di replica deve accedere.

## <a name="azure-vm-requirements"></a>Requisiti per le VM di Azure

Tutte le macchine virtuali locali replicate in Azure devono soddisfare i requisiti della macchina virtuale di Azure riepilogati in questa tabella. Quando Site Recovery esegue un controllo dei prerequisiti per la replica, il controllo ha esito negativo se alcuni dei requisiti non vengono soddisfatti.

**Componente** | **Requisiti** | **Dettagli**
--- | --- | ---
Sistema operativo guest | Verifica i sistemi operativi della macchina virtuale VMware supportati per la migrazione.<br/> È possibile eseguire la migrazione di qualsiasi carico di lavoro in esecuzione in un sistema operativo supportato. | Il controllo ha esito negativo se non supportato.
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
Connetti dopo la migrazione-Windows | Per connettersi alle macchine virtuali di Azure che eseguono Windows dopo la migrazione:<br/> -Prima della migrazione Abilita RDP nella macchina virtuale locale. Assicurarsi che siano aggiunte regole TCP e UDP per il profilo **Pubblico** e che RDP sia consentito in **Windows Firewall** > **App consentite** per tutti i profili.<br/> Per l'accesso VPN da sito a sito, abilitare RDP e consentire il protocollo RDP in **Windows Firewall** -> **le app e le funzionalità consentite** per le reti di **dominio e private** . Verificare inoltre che il criterio SAN del sistema operativo sia impostato su onlineal **.** [Altre informazioni](prepare-for-migration.md) |
Connetti dopo la migrazione-Linux | Per connettersi alle macchine virtuali di Azure dopo la migrazione tramite SSH:<br/> Prima della migrazione, nel computer locale controllare che il servizio Secure Shell sia impostato su avvio e che le regole del firewall consentano una connessione SSH.<br/> Dopo il failover, nella macchina virtuale di Azure, consentire le connessioni in ingresso alla porta SSH per le regole del gruppo di sicurezza di rete nella macchina virtuale sottoposta a failover e per la subnet di Azure a cui è connessa. Aggiungere inoltre un indirizzo IP pubblico per la macchina virtuale. |  


## <a name="next-steps"></a>Passaggi successivi

[Selezionare](server-migrate-overview.md) un'opzione di migrazione VMware.
