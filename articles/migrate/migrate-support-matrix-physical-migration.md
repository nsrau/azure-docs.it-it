---
title: Supporto per la migrazione fisica del server in Azure MigrateSupport for physical server migration in Azure Migrate
description: Informazioni sul supporto per la migrazione fisica dei server in Azure Migrate.Learn about support for physical server migration in Azure Migrate.
ms.topic: conceptual
ms.custom: fasttrack-edit
ms.date: 01/07/2020
ms.openlocfilehash: 8f8b94ab77a1eef8e771384f5d69da98a1d7ae6c
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520277"
---
# <a name="support-matrix-for-physical-server-migration"></a>Matrice di supporto per la migrazione fisica dei serverSupport matrix for physical server migration

Questo articolo riepiloga le impostazioni e le limitazioni di supporto per la migrazione dei server fisici con [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) . Per informazioni sulla valutazione dei server fisici per la migrazione ad Azure, esaminare la matrice di supporto della [valutazione.](migrate-support-matrix-physical.md)


## <a name="overview"></a>Panoramica

È possibile eseguire la migrazione di computer locali come server fisici, utilizzando la replica basata su agente. Con questo strumento è possibile eseguire la migrazione di un'ampia gamma di computer ad Azure:

- Server fisici locali.
- Macchine virtuali virtualizzate da piattaforme come Xen, KVM.
- Macchine virtuali Hyper-V o macchine virtuali VMware se per qualche motivo non si desidera utilizzare i flussi standard di [Hyper-V](tutorial-migrate-hyper-v.md) o [VMware.](server-migrate-overview.md)
- Macchine virtuali in esecuzione in cloud privati.
- Macchine virtuali in esecuzione in cloud pubblici come Amazon Web Services (AWS) o Google Cloud Platform (GCP).


## <a name="migration-limitations"></a>Limitazioni della migrazione

È possibile selezionare fino a 10 computer contemporaneamente per la replica. Se si desidera eseguire la migrazione di più computer, eseguire la replica in gruppi di 10.If you want to migrate more machines, then replicate in groups of 10.


## <a name="physical-server-requirements"></a>Requisiti per i server fisici

Nella tabella viene riepilogato il supporto per i server fisici di cui si desidera eseguire la migrazione tramite la migrazione basata su agenti.

**Supporto** | **Dettagli**
--- | ---
**Carico di lavoro del computer** | Azure Migrate supporta la migrazione di qualsiasi carico di lavoro (ad esempio Active Directory, SQL Server e così via) in esecuzione in un computer supportato.
**Sistemi operativi** | Per informazioni aggiornate, esaminare il [supporto del sistema operativo](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) per Site Recovery. Azure Migrate offre un supporto identico per il sistema operativo.
**File system Linux/archiviazione guest** | Per le informazioni più recenti, esaminare il supporto del [file system Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) per Site Recovery. Azure Migrate offre un supporto del file system Linux identico.
**Rete/Archiviazione** | Per informazioni aggiornate, esaminare i prerequisiti di [rete](../site-recovery/vmware-physical-azure-support-matrix.md#network) e [archiviazione](../site-recovery/vmware-physical-azure-support-matrix.md#storage) per Site Recovery. Azure Migrate offre requisiti di rete/archiviazione identici.
**Requisiti di Azure** | Per informazioni aggiornate, esaminare la rete di [Azure,](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)i requisiti [di archiviazione](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)e [di calcolo](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) per Site Recovery. Azure Migrate ha requisiti identici per la migrazione del server fisico.
**Servizio Mobility** | L'agente del servizio Mobility deve essere installato in ogni computer di cui si desidera eseguire la migrazione.
**Stivale UEFI** | La macchina migrata in Azure verrà automaticamente convertita in una macchina virtuale di Azure di avvio del BIOS. Solo il server che esegue Windows Server 2012 e versioni successive supportato.<br/><br/> Il disco del sistema operativo deve avere fino a quattro partizioni e i volumi devono essere formattati con NTFS.
**Disco di destinazione** | I computer possono essere migrati solo su dischi gestiti (HDD standard, SSD premium) in Azure.Machines can only be migrated to managed disks (standard HDD, premium SSD) in Azure.
**Dimensioni disco** | Disco oS da 2 TB; 8 TB per i dischi dati.
**Limiti del disco** |  Fino a 63 dischi per macchina.
**Dischi/volumi crittografati** |  I computer con dischi/volumi crittografati non sono supportati per la migrazione.
**Cluster di dischi condivisi** | Non supportato.
**Dischi indipendenti** | Supportato.
**Dischi pass-through** | Supportato.
**NFS** | I volumi NFS montati come volumi sui computer non verranno replicati.
**Obiettivi iSCSI** | I computer con destinazioni iSCSI non sono supportati per la migrazione senza agente.
**I/O multipercorso** | Non supportato.
**Archiviazione vMotion** | Supportato
**Schede di rete in team** | Non supportato.
**IPv6** | Non supportato.



## <a name="replication-appliance-requirements"></a>Requisiti dell'appliance di replica

Se l'appliance di replica viene configurata manualmente in un server fisico, assicurarsi che sia conforme ai requisiti riepilogati nella tabella. Quando si configura l'appliance di replica di Azure Migrate come VM VMware usando il modello OVA fornito nell'hub di Azure Migrate, l'appliance viene configurata con Windows Server 2016 ed è conforme ai requisiti di supporto. 

- Informazioni sui [requisiti dell'appliance](migrate-replication-appliance.md#appliance-requirements)di replica .
- MySQL deve essere installato nell'appliance. Ulteriori informazioni sulle opzioni di [installazione](migrate-replication-appliance.md#mysql-installation).
- Informazioni sugli [URL](migrate-replication-appliance.md#url-access) a cui l'appliance di replica deve accedere.

## <a name="azure-vm-requirements"></a>Requisiti per le VM di Azure

Tutte le macchine virtuali locali replicate in Azure devono soddisfare i requisiti delle macchine virtuali di Azure riepilogati in questa tabella. Quando Site Recovery esegue un controllo dei prerequisiti per la replica, il controllo avrà esito negativo se alcuni dei requisiti non vengono soddisfatti.

**Componente** | **Requisiti** | **Dettagli**
--- | --- | ---
Sistema operativo guest | Verifica i sistemi operativi supportati.<br/> È possibile eseguire la migrazione di qualsiasi carico di lavoro in esecuzione in un sistema operativo supportato. | Il controllo ha esito negativo se non supportato.
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

[Eseguire la migrazione di](tutorial-migrate-physical-virtual-machines.md) server fisici.
