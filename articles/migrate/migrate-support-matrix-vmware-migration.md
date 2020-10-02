---
title: Supporto per la migrazione di VMware in Azure Migrate
description: Informazioni sul supporto per la migrazione di macchine virtuali VMware in Azure Migrate.
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 87733cac23d0336e4b9319f2a325e8d844e6e5b2
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91651956"
---
# <a name="support-matrix-for-vmware-migration"></a>Matrice di supporto per la migrazione a VMware

Questo articolo riepiloga le impostazioni di supporto e le limitazioni per la migrazione di macchine virtuali VMware con [Azure migrate: migrazione del server](migrate-services-overview.md#azure-migrate-server-migration-tool) . Per informazioni sulla valutazione delle macchine virtuali VMware per la migrazione ad Azure, vedere la matrice di [supporto](migrate-support-matrix-vmware.md)per la valutazione.


## <a name="migration-options"></a>Opzioni di migrazione

È possibile eseguire la migrazione di macchine virtuali VMware in due modi:

- **Uso della migrazione senza agenti**: eseguire la migrazione di macchine virtuali senza dover installare alcun elemento. Si distribuisce il [dispositivo Azure migrate](migrate-appliance.md) per la migrazione senza agenti.
- **Uso della migrazione basata su agenti**: installare un agente nella macchina virtuale per la replica. Per la migrazione basata su agenti, si distribuisce un' [appliance di replica](migrate-replication-appliance.md).

Leggere [questo articolo](server-migrate-overview.md) per determinare il metodo che si vuole usare.

## <a name="migration-limitations"></a>Limitazioni della migrazione

- È possibile selezionare fino a 10 macchine virtuali contemporaneamente per la replica. Se si desidera eseguire la migrazione di più computer, eseguire la replica in gruppi di 10.
- Per la migrazione senza agenti VMware, è possibile eseguire contemporaneamente fino a 300 repliche.

## <a name="agentless-migration"></a>Migrazione senza agenti 

In questa sezione vengono riepilogati i requisiti per la migrazione senza agenti.

### <a name="vmware-requirements-agentless"></a>Requisiti VMware (senza agenti)

La tabella riepiloga i requisiti di hypervisor VMware.

**VMware** | **Dettagli**
--- | ---
**server VMware vCenter** | Versione 5,5, 6,0, 6,5, 6,7, 7,0.
**VMware vSphere Host ESXI** | Versione 5,5, 6,0, 6,5, 6,7, 7,0.
**autorizzazioni server vCenter** | La migrazione senza agenti usa l' [appliance di migrazione](migrate-appliance.md). Il dispositivo richiede queste autorizzazioni in server vCenter:<br/><br/> - **Archivio dati. browse**: consente l'esplorazione dei file di log della macchina virtuale per risolvere i problemi di creazione ed eliminazione di snapshot.<br/><br/> - **Datastore. filemanagement**: consente operazioni di lettura/scrittura/eliminazione/ridenominazione nel browser dell'archivio dati per risolvere i problemi di creazione ed eliminazione di snapshot.<br/><br/> - **VirtualMachine.Config. Rilevamento modifiche**: consente di abilitare o disabilitare il rilevamento delle modifiche dei dischi delle macchine virtuali per eseguire il pull dei blocchi di dati modificati tra gli snapshot.<br/><br/> - **VirtualMachine.Config. DiskLease**: consente operazioni di lease del disco per una macchina virtuale, per leggere il disco usando il VMware vSphere Virtual Disk Development Kit (VDDK).<br/><br/> - **Virtualmachine. provisioning. DiskAccess**: (in particolare per vSphere 6,0 e versioni successive) consentire l'apertura di un disco in una macchina virtuale per l'accesso in lettura casuale al disco usando VDDK.<br/><br/> - **Virtualmachine. provisioning. DiskRandomRead**: consente di aprire un disco in una macchina virtuale per leggere il disco usando VDDK.<br/><br/> - **Virtualmachine. provisioning. DiskRandomAccess**: consente di aprire un disco in una macchina virtuale per leggere il disco usando VDDK.<br/><br/> - **Virtualmachine. provisioning. GetVmFiles**: consente operazioni di lettura nei file associati a una VM, per scaricare i log e risolvere eventuali errori.<br/><br/> - **Virtualmachine. state. \* **: consente la creazione e la gestione di snapshot VM per la replica.<br/><br/> - **Virtualmachine. Interact. spento**: consente di spegnere la macchina virtuale durante la migrazione ad Azure.



### <a name="vm-requirements-agentless"></a>Requisiti per le macchine virtuali (senza agenti)

La tabella riepiloga i requisiti di migrazione senza agenti per le macchine virtuali VMware.

**Supporto** | **Dettagli**
--- | ---
**Sistemi operativi supportati** | È possibile eseguire la migrazione dei sistemi operativi [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](../virtual-machines/linux/endorsed-distros.md) supportati da Azure.
**Macchine virtuali Windows in Azure** | Potrebbe essere necessario [apportare alcune modifiche](prepare-for-migration.md#verify-required-changes-before-migrating) alle macchine virtuali prima della migrazione. 
**VM Linux in Azure** | Alcune macchine virtuali potrebbero richiedere alcune modifiche per la corretta esecuzione in Azure.<br/><br/> Per Linux, Azure Migrate apporta automaticamente le modifiche per questi sistemi operativi:<br/> -Red Hat Enterprise Linux 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x<br/> -Cent OS 7,7, 7,6, 7,5, 7,4, 6. x</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -SUSE Linux Enterprise Server 15 SP1 <br/>-Ubuntu 19,04, 19,10, 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8 <br/> Oracle Linux 7.7, 7.7-CI<br/> Per gli altri sistemi operativi apportare manualmente le [modifiche necessarie](prepare-for-migration.md#verify-required-changes-before-migrating) .
**Avvio di Linux** | Se/boot si trova in una partizione dedicata, deve risiedere nel disco del sistema operativo e non essere distribuito tra più dischi.<br/> Se/boot fa parte della partizione radice (/), la partizione '/' deve trovarsi nel disco del sistema operativo e non si estende su altri dischi.
**Avvio UEFI** | Supportata. Verrà eseguita la migrazione delle VM basate su UEFI alle macchine virtuali di Azure di seconda generazione. 
**Dimensioni disco** | disco del sistema operativo da 2 TB (avvio BIOS); disco del sistema operativo da 4 TB (avvio UEFI); 8 TB per i dischi dati.
**Limiti del disco** |  Fino a 60 dischi per macchina virtuale.
**Dischi/volumi crittografati** | Le macchine virtuali con dischi/volumi crittografati non sono supportate per la migrazione.
**Cluster di dischi condivisi** | Non supportata.
**Dischi indipendenti** | Non supportata.
**Dischi RDM/passthrough** | Se le VM contengono dischi RDM o passthrough, questi dischi non verranno replicati in Azure.
**NFS** | I volumi NFS montati come volumi nelle macchine virtuali non verranno replicati.
**destinazioni iSCSI** | Le macchine virtuali con destinazioni iSCSI non sono supportate per la migrazione senza agenti.
**I/o a percorsi multipli** | Non supportata.
**Storage vMotion** | Non supportata. La replica non funziona se una macchina virtuale usa Storage vMotion.
**NIC raggruppate** | Non supportata.
**IPv6** | Non supportata.
**Disco di destinazione** | È possibile eseguire la migrazione delle macchine virtuali solo a Managed Disks (standard HDD, SSD standard, unità SSD Premium) in Azure.
**Replica simultanea** | 300 VM per ogni server vCenter. Se si dispone di altro, eseguirne la migrazione in batch di 300.


### <a name="appliance-requirements-agentless"></a>Requisiti del dispositivo (senza agenti)

La migrazione senza agente usa il [dispositivo Azure migrate](migrate-appliance.md). È possibile distribuire l'appliance come macchina virtuale VMware usando un modello OVA, importato in server vCenter o usando uno [script di PowerShell](deploy-appliance-script.md).

- Informazioni sui [requisiti dell'appliance](migrate-appliance.md#appliance---vmware) per VMware.
- Informazioni sugli URL a cui l'appliance deve accedere nei cloud [pubblico](migrate-appliance.md#public-cloud-urls) e [per enti pubblici](migrate-appliance.md#government-cloud-urls).
- In Azure per enti pubblici è necessario distribuire l'appliance [tramite lo script](deploy-appliance-script-government.md).

### <a name="port-requirements-agentless"></a>Requisiti per le porte (senza agenti)

**Dispositivo** | **Connection**
--- | ---
Appliance | Connessioni in uscita sulla porta 443 per caricare i dati replicati in Azure e per comunicare con Azure Migrate Services che orchestrano la replica e la migrazione.
Server vCenter | Connessioni in ingresso sulla porta 443 per consentire all'appliance di orchestrare la replica: creare snapshot, copiare dati, rilasciare snapshot
host vSphere/ESXI | In ingresso sulla porta TCP 902 per l'appliance per replicare i dati dagli snapshot.

## <a name="agent-based-migration"></a>Migrazione basata su agenti 


In questa sezione vengono riepilogati i requisiti per la migrazione basata su agenti.


### <a name="vmware-requirements-agent-based"></a>Requisiti VMware (basati su agenti)

Questa tabella riepiloga il supporto per la valutazione e le limitazioni per i server di virtualizzazione VMware.

**Requisiti di VMware** | **Dettagli**
--- | ---
**server VMware vCenter** | Versione 5,5, 6,0, 6,5 o 6,7.
**VMware vSphere Host ESXI** | Versione 5,5, 6,0, 6,5 o 6,7.
**autorizzazioni server vCenter** | Account di sola lettura per server vCenter.

### <a name="vm-requirements-agent-based"></a>Requisiti delle macchine virtuali (basati su agenti)

La tabella riepiloga il supporto delle VM VMware per le macchine virtuali VMware di cui si vuole eseguire la migrazione tramite la migrazione basata su agenti.

**Supporto** | **Dettagli**
--- | ---
**Carico di lavoro del computer** | Azure Migrate supporta la migrazione di qualsiasi carico di lavoro (ad indicare Active Directory, SQL Server e così via) in esecuzione in un computer supportato.
**Sistemi operativi** | Per informazioni aggiornate, vedere il [supporto del sistema operativo](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) per Site Recovery. Azure Migrate offre un supporto identico per il sistema operativo della macchina virtuale.
**File system Linux/archiviazione Guest** | Per informazioni aggiornate, vedere il [supporto di Linux file System](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) per Site Recovery. Azure Migrate dispone di un supporto di Linux file system identico.
**Rete/archiviazione** | Per le informazioni più aggiornate, esaminare i prerequisiti di [rete](../site-recovery/vmware-physical-azure-support-matrix.md#network) e di [archiviazione](../site-recovery/vmware-physical-azure-support-matrix.md#storage) per Site Recovery. Azure Migrate fornisce requisiti di rete/archiviazione identici.
**Requisiti di Azure** | Per informazioni aggiornate, vedere i requisiti di rete, [archiviazione](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)e [calcolo](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) di [Azure](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)per Site Recovery. Azure Migrate presenta requisiti identici per la migrazione di VMware.
**Servizio Mobility** | L'agente del servizio Mobility deve essere installato in ogni macchina virtuale di cui si vuole eseguire la migrazione.
**Avvio UEFI** | Supportata. Verrà eseguita la migrazione delle VM basate su UEFI alle macchine virtuali di Azure di seconda generazione. 
**UEFI-avvio protetto**         | Non supportato per la migrazione.
**Disco di destinazione** | È possibile eseguire la migrazione delle macchine virtuali solo a Managed Disks (standard HDD, SSD standard, unità SSD Premium) in Azure.
**Dimensioni disco** | disco del sistema operativo da 2 TB (avvio BIOS); disco del sistema operativo da 4 TB (avvio UEFI); 8 TB per i dischi dati.
**Limiti del disco** |  Fino a 63 dischi per macchina virtuale.
**Dischi/volumi crittografati** | Le macchine virtuali con dischi/volumi crittografati non sono supportate per la migrazione.
**Cluster di dischi condivisi** | Non supportata.
**Dischi indipendenti** | Supportata.
**Dischi pass-through** | Supportata.
**NFS** | I volumi NFS montati come volumi nelle macchine virtuali non verranno replicati.
**destinazioni iSCSI** | Supportata.
**I/o a percorsi multipli** | Non supportata.
**Storage vMotion** | Supportato
**NIC raggruppate** | Non supportata.
**IPv6** | Non supportata.




### <a name="appliance-requirements-agent-based"></a>Requisiti dell'appliance (basati su agenti)

Quando si configura l'appliance di replica usando il modello OVA fornito nell'hub Azure Migrate, l'Appliance esegue Windows Server 2016 e soddisfa i requisiti di supporto. Se si configura manualmente l'appliance di replica in un server fisico, assicurarsi che sia conforme ai requisiti.

- Informazioni sui [requisiti del dispositivo di replica](migrate-replication-appliance.md#appliance-requirements) per VMware.
- MySQL deve essere installato nell'appliance. Informazioni sulle [Opzioni di installazione](migrate-replication-appliance.md#mysql-installation).
- Informazioni sugli URL a cui l'appliance di replica deve accedere in cloud [pubblici](migrate-replication-appliance.md#url-access) e [governativi](migrate-replication-appliance.md#azure-government-url-access) .
- Esaminare le [porte](migrate-replication-appliance.md#port-access) a cui deve accedere l'appliance di replica.

### <a name="port-requirements-agent-based"></a>Requisiti delle porte (basati su agenti)

**Dispositivo** | **Connection**
--- | ---
VM | Il servizio Mobility in esecuzione nelle VM comunica con l'appliance di replica locale (server di configurazione) sulla porta HTTPS 443 in ingresso, per la gestione della replica.<br/><br/> Le macchine virtuali inviano i dati della replica al server di elaborazione (in esecuzione sul computer del server di configurazione) sulla porta HTTPS 9443 in ingresso. La porta può essere modificata.
Appliance di replica | L'appliance di replica orchestra la replica con Azure tramite la porta HTTPS 443 in uscita.
Server di elaborazione | Il server di elaborazione riceve i dati di replica, li ottimizza e li crittografa e li invia ad archiviazione di Azure tramite la porta 443 in uscita.<br/> Per impostazione predefinita, il server di elaborazione viene eseguito nell'appliance di replica.

## <a name="azure-vm-requirements"></a>Requisiti per le VM di Azure

Tutte le macchine virtuali locali replicate in Azure (con migrazione senza agenti o basata su agenti) devono soddisfare i requisiti delle macchine virtuali di Azure riepilogati in questa tabella. 

**Componente** | **Requisiti** 
--- | --- | ---
Sistema operativo guest | Verifica i sistemi operativi della macchina virtuale VMware supportati per la migrazione.<br/> È possibile eseguire la migrazione di qualsiasi carico di lavoro in esecuzione in un sistema operativo supportato. 
Architettura del sistema operativo guest | 64 bit. 
Dimensioni disco del sistema operativo | Fino a 2.048 GB. 
Conteggio dischi del sistema operativo | 1 
Conteggio dischi dati | 64 o un numero inferiore. 
Dimensioni del disco dati | Fino a 8.095 GB
Schede di rete | Sono supportate più schede.
VHD condiviso | Non supportata. 
Disco FC | Non supportata. 
BitLocker | Non supportata.<br/><br/> Prima di eseguire la migrazione della macchina virtuale, è necessario disabilitare BitLocker.
Nome della VM. | Da 1 a 63 caratteri.<br/><br/> Limitato a lettere, numeri e trattini.<br/><br/> Il nome del computer deve iniziare e terminare con una lettera o un numero. 
Connetti dopo la migrazione-Windows | Per connettersi alle macchine virtuali di Azure che eseguono Windows dopo la migrazione:<br/><br/> -Prima della migrazione, abilitare RDP nella macchina virtuale locale.<br/><br/> Assicurarsi che vengano aggiunte regole TCP e UDP per il profilo **pubblico** e che il protocollo RDP sia consentito in **Windows Firewall**  >  **app consentite**per tutti i profili.<br/><br/> Per l'accesso VPN da sito a sito, abilitare RDP e consentire il protocollo RDP in **Windows Firewall**  ->  **app e funzionalità consentite** per le reti di **dominio e private** .<br/><br/> Verificare inoltre che il criterio SAN del sistema operativo sia impostato su onlineal **.** [Altre informazioni](prepare-for-migration.md)
Connetti dopo la migrazione-Linux | Per connettersi alle macchine virtuali di Azure dopo la migrazione tramite SSH:<br/><br/> Prima della migrazione, nel computer locale controllare che il servizio Secure Shell sia impostato su avvio e che le regole del firewall consentano una connessione SSH.<br/><br/> Dopo il failover, nella macchina virtuale di Azure, consentire le connessioni in ingresso alla porta SSH per le regole del gruppo di sicurezza di rete nella macchina virtuale sottoposta a failover e per la subnet di Azure a cui è connessa.<br/><br/> Aggiungere inoltre un indirizzo IP pubblico per la macchina virtuale.  


## <a name="next-steps"></a>Passaggi successivi

[Selezionare](server-migrate-overview.md) un'opzione di migrazione VMware.
