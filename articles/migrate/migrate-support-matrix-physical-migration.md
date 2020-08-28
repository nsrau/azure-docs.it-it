---
title: Supporto per la migrazione di server fisici in Azure Migrate
description: Informazioni sul supporto per la migrazione di server fisici in Azure Migrate.
ms.topic: conceptual
ms.custom: fasttrack-edit
ms.date: 06/14/2020
ms.openlocfilehash: 30c96b11fb15ca1783b6ffc574d9d258dff9da06
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051135"
---
# <a name="support-matrix-for-physical-server-migration"></a>Matrice di supporto per la migrazione di server fisici

Questo articolo riepiloga le impostazioni di supporto e le limitazioni per la migrazione dei server fisici con [Azure migrate: migrazione del server](migrate-services-overview.md#azure-migrate-server-migration-tool) . Per informazioni sulla valutazione dei server fisici per la migrazione in Azure, vedere la [matrice di supporto](migrate-support-matrix-physical.md)per la valutazione.

## <a name="migrating-machines-as-physical"></a>Migrazione di computer come fisici

È possibile eseguire la migrazione di computer locali come server fisici utilizzando la replica basata su agenti. Con questo strumento è possibile eseguire la migrazione di un'ampia gamma di computer ad Azure:

- Server fisici locali.
- Macchine virtuali virtualizzate da piattaforme come Xen, KVM.
- Macchine virtuali Hyper-V o VM VMware se per qualche motivo non si vogliono usare i flussi standard di [Hyper-v](tutorial-migrate-hyper-v.md) o [VMware](server-migrate-overview.md) .
- Macchine virtuali in esecuzione in cloud privati.
- Macchine virtuali in esecuzione in cloud pubblici, ad esempio Amazon Web Services (AWS) o Google Cloud Platform (GCP).


## <a name="migration-limitations"></a>Limitazioni della migrazione

È possibile selezionare un massimo di 10 computer contemporaneamente per la replica. Se si desidera eseguire la migrazione di più computer, eseguire la replica in gruppi di 10.


## <a name="physical-server-requirements"></a>Requisiti per i server fisici

Nella tabella viene riepilogato il supporto per i server fisici di cui si desidera eseguire la migrazione mediante la migrazione basata su agenti.

**Supporto** | **Dettagli**
--- | ---
**Carico di lavoro del computer** | Azure Migrate supporta la migrazione di qualsiasi carico di lavoro (ad indicare Active Directory, SQL Server e così via) in esecuzione in un computer supportato.
**Sistemi operativi** | Per informazioni aggiornate, vedere il [supporto del sistema operativo](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) per Site Recovery. Azure Migrate offre un supporto identico per il sistema operativo.
**File system Linux/archiviazione Guest** | Per informazioni aggiornate, vedere il [supporto di Linux file System](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) per Site Recovery. Azure Migrate offre un supporto di Linux file system identico.
**Rete/archiviazione** | Per le informazioni più aggiornate, esaminare i prerequisiti di [rete](../site-recovery/vmware-physical-azure-support-matrix.md#network) e di [archiviazione](../site-recovery/vmware-physical-azure-support-matrix.md#storage) per Site Recovery. Azure Migrate fornisce requisiti di rete/archiviazione identici.
**Requisiti di Azure** | Per informazioni aggiornate, vedere i requisiti di rete, [archiviazione](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)e [calcolo](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) di [Azure](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)per Site Recovery. Azure Migrate presenta requisiti identici per la migrazione di server fisici.
**Servizio Mobility** | L'agente del servizio Mobility deve essere installato in ogni computer di cui si vuole eseguire la migrazione.
**Avvio UEFI** | Il computer migrato in Azure verrà convertito automaticamente in una macchina virtuale di Azure di avvio BIOS. Sono supportati solo i server che eseguono Windows Server 2012 e versioni successive.<br/><br/> Il disco del sistema operativo deve avere fino a quattro partizioni e i volumi devono essere formattati con NTFS.
**UEFI-avvio protetto**         | Non supportato per la migrazione.
**Disco di destinazione** | È possibile eseguire la migrazione dei computer solo a Managed Disks (standard HDD, unità SSD Premium) in Azure.
**Dimensioni disco** | disco del sistema operativo da 2 TB; 8 TB per i dischi dati.
**Limiti del disco** |  Fino a 63 dischi per computer.
**Dischi/volumi crittografati** |  I computer con volumi o dischi crittografati non sono supportati per la migrazione.
**Cluster di dischi condivisi** | Non supportata.
**Dischi indipendenti** | Supportata.
**Dischi pass-through** | Supportata.
**NFS** | I volumi NFS montati come volumi nei computer non verranno replicati.
**destinazioni iSCSI** | I computer con destinazioni iSCSI non sono supportati per la migrazione senza agenti.
**I/o a percorsi multipli** | Non supportata.
**Storage vMotion** | Supportato
**NIC raggruppate** | Non supportata.
**IPv6** | Non supportata.



## <a name="replication-appliance-requirements"></a>Requisiti dell'appliance di replica

Se si configura manualmente l'appliance di replica in un server fisico, assicurarsi che sia conforme ai requisiti riepilogati nella tabella. Quando si configura l'appliance di replica Azure Migrate come macchina virtuale VMware usando il modello OVA fornito nell'hub Azure Migrate, l'Appliance viene configurata con Windows Server 2016 e soddisfa i requisiti di supporto. 

- Informazioni sui [requisiti dell'appliance di replica](migrate-replication-appliance.md#appliance-requirements).
- MySQL deve essere installato nell'appliance. Informazioni sulle [Opzioni di installazione](migrate-replication-appliance.md#mysql-installation).
- Informazioni sugli [URL](migrate-replication-appliance.md#url-access) a cui l'appliance di replica deve accedere.

## <a name="azure-vm-requirements"></a>Requisiti per le VM di Azure

Tutte le macchine virtuali locali replicate in Azure devono soddisfare i requisiti della macchina virtuale di Azure riepilogati in questa tabella. Quando Site Recovery esegue un controllo dei prerequisiti per la replica, il controllo ha esito negativo se alcuni dei requisiti non vengono soddisfatti.

**Componente** | **Requisiti** | **Dettagli**
--- | --- | ---
Sistema operativo guest | Verifica i sistemi operativi supportati.<br/> È possibile eseguire la migrazione di qualsiasi carico di lavoro in esecuzione in un sistema operativo supportato. | Il controllo ha esito negativo se non supportato.
Architettura del sistema operativo guest | 64 bit. | Il controllo ha esito negativo se non supportato.
Dimensioni disco del sistema operativo | Fino a 2.048 GB. | Il controllo ha esito negativo se non supportato.
Conteggio dischi del sistema operativo | 1 | Il controllo ha esito negativo se non supportato.
Conteggio dischi dati | 64 o un numero inferiore. | Il controllo ha esito negativo se non supportato.
Dimensioni del disco dati | Fino a 4.095 GB | Il controllo ha esito negativo se non supportato.
Schede di rete | Sono supportate più schede. |
VHD condiviso | Non supportata. | Il controllo ha esito negativo se non supportato.
Disco FC | Non supportata. | Il controllo ha esito negativo se non supportato.
BitLocker | Non supportata. | Prima di abilitare la replica per un computer, occorre disabilitare BitLocker.
Nome della VM. | Da 1 a 63 caratteri.<br/> Limitato a lettere, numeri e trattini.<br/><br/> Il nome del computer deve iniziare e terminare con una lettera o un numero. |  Aggiornare il valore nelle proprietà del computer in Site Recovery.
Connetti dopo la migrazione-Windows | Per connettersi alle macchine virtuali di Azure che eseguono Windows dopo la migrazione:<br/> -Prima della migrazione Abilita RDP nella macchina virtuale locale. Assicurarsi che vengano aggiunte regole TCP e UDP per il profilo **pubblico** e che il protocollo RDP sia consentito in **Windows Firewall**  >  **app consentite**per tutti i profili.<br/> Per l'accesso VPN da sito a sito, abilitare RDP e consentire il protocollo RDP in **Windows Firewall**  ->  **app e funzionalità consentite** per le reti di **dominio e private** . Verificare inoltre che il criterio SAN del sistema operativo sia impostato su onlineal **.** [Altre informazioni](prepare-for-migration.md) |
Connetti dopo la migrazione-Linux | Per connettersi alle macchine virtuali di Azure dopo la migrazione tramite SSH:<br/> Prima della migrazione, nel computer locale controllare che il servizio Secure Shell sia impostato su avvio e che le regole del firewall consentano una connessione SSH.<br/> Dopo il failover, nella macchina virtuale di Azure, consentire le connessioni in ingresso alla porta SSH per le regole del gruppo di sicurezza di rete nella macchina virtuale sottoposta a failover e per la subnet di Azure a cui è connessa. Aggiungere inoltre un indirizzo IP pubblico per la macchina virtuale. |  


## <a name="next-steps"></a>Passaggi successivi

[Eseguire la migrazione](tutorial-migrate-physical-virtual-machines.md) dei server fisici.
