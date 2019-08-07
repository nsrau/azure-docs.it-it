---
title: Matrice di supporto Azure Migrate per la valutazione e la migrazione di VMware
description: Riepiloga le impostazioni di supporto e le limitazioni per la valutazione e la migrazione di macchine virtuali VMware in Azure usando il servizio Azure Migrate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: raynew
ms.openlocfilehash: ed7e53570127f12dbe41932481a3286a78865dde
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828286"
---
# <a name="support-matrix-for-vmware-assessment-and-migration"></a>Matrice di supporto per la valutazione e la migrazione di VMware

È possibile usare [Azure migrate](migrate-overview.md) per valutare ed eseguire la migrazione di computer nel cloud Microsoft Azure. Questo articolo riepiloga le impostazioni di supporto e le limitazioni per la valutazione e la migrazione di macchine virtuali VMware locali.


## <a name="vmware-scenarios"></a>Scenari VMware

Nella tabella sono riepilogati gli scenari supportati per le macchine virtuali VMware.

**Distribuzione** | **Dettagli**
--- | ---
**Valutare le macchine virtuali VMware locali** | [Configurare](tutorial-prepare-vmware.md) la prima valutazione.<br/><br/> [Eseguire](scale-vmware-assessment.md) una valutazione su larga scala.
**Eseguire la migrazione di VM VMware** | È possibile eseguire la migrazione tramite la migrazione senza agenti oppure utilizzare una migrazione basata su agenti. [Altre informazioni](server-migrate-overview.md)


## <a name="azure-migrate-projects"></a>Progetti Azure Migrate

**Supporto** | **Dettagli**
--- | ---
**Autorizzazioni di Azure** | Per creare un progetto di Azure Migrate, è necessario disporre delle autorizzazioni Collaboratore o proprietario nella sottoscrizione.
**Limitazioni di VMware**  | Consente di valutare fino a 35.000 VM VMware in un singolo progetto. È possibile creare più progetti in una sottoscrizione di Azure.
**Limiti del progetto** | Un progetto può includere sia macchine virtuali VMware che macchine virtuali Hyper-V, fino ai limiti di valutazione.
**Area geografica** | È possibile creare un progetto Azure Migrate in diverse aree geografiche. Sebbene sia possibile creare progetti solo in queste aree geografiche, è possibile valutare o migrare i computer per altri percorsi di destinazione. L'area geografica del progetto viene usata solo per archiviare i metadati individuati.

**Area geografica** | **Posizione di archiviazione dei metadati**
--- | ---
Azure per enti pubblici | US Gov Virginia
Asia/Pacifico | Asia orientale o Asia sud-orientale
Australia | Australia orientale o Australia sudorientale
Canada | Canada centrale o Canada orientale
Europa | Europa settentrionale o Europa occidentale
India | India centrale o India meridionale
Giappone |  Giappone orientale o Giappone occidentale
Regno Unito | Regno Unito meridionale o Regno Unito occidentale
Stati Uniti | Stati Uniti centrali o Stati Uniti occidentali 2


 > [!NOTE]
 > Il supporto per Azure per enti pubblici è attualmente disponibile solo per la [versione precedente](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) di Azure migrate.


## <a name="assessment-vcenter-server-requirements"></a>Valutazione-requisiti di server vCenter

Questa tabella riepiloga il supporto per la valutazione e le limitazioni per i server di virtualizzazione VMware.

**Supporto** | **Dettagli**
--- | ---
**Server vCenter** | Le macchine virtuali VMware che si vuole valutare devono essere gestite da uno o più server vCenter che eseguono 5,5, 6,0, 6,5 o 6,7.

## <a name="assessment-vcenter-server-permissions"></a>Valutazione: autorizzazioni server vCenter

Per la valutazione, è necessario un account di sola lettura per la server vCenter.

## <a name="assessment-appliance-requirements"></a>Valutazione-requisiti del dispositivo

Il Azure Migrate Appliance per VMware viene distribuito usando un modello OVA importato in server vCenter.

**Supporto** | **Dettagli**
--- | ---
**server vCenter** | È necessario disporre di risorse sufficienti nel server vCenter per allocare una macchina virtuale con 32 GB di RAM, 8 vCPU e un Commuter virtuale esterno.<br/><br/> Il dispositivo richiede l'accesso a Internet, direttamente o tramite un proxy.
**ESXi** | La macchina virtuale dell'appliance deve essere distribuita in un host ESXi che esegue la versione 5,5 o successiva.
**Progetto Azure Migrate** | Un appliance può essere associato a un singolo progetto.
**server vCenter** | Un dispositivo può individuare fino a 10.000 VM VMware in una server vCenter.<br/> Un appliance può connettersi a una server vCenter.


## <a name="assessment-url-access-requirements"></a>Valutazione-requisiti di accesso agli URL

Il dispositivo Azure Migrate richiede la connettività a Internet.

- Quando si distribuisce il dispositivo, Azure Migrate esegue un controllo della connettività agli URL riepilogati nella tabella seguente.
- Se si usa un proxy basato su URL per connettersi a Internet, consentire l'accesso a questi URL, assicurandosi che il proxy risolva tutti i record CNAME ricevuti durante la ricerca degli URL.

**URL** | **Dettagli**  
--- | --- |
*.portal.azure.com  | Passare al Azure Migrate nel portale di Azure.
*.windows.net | Accedere alla propria sottoscrizione di Azure.
*.microsoftonline.com | Creare app Active Directory per l'appliance per comunicare con il servizio Azure Migrate.
management.azure.com | Creare app Active Directory per l'appliance per comunicare con il servizio Azure Migrate.
dc.services.visualstudio.com | Caricare i log delle app usati per il monitoraggio interno.
*.vault.azure.net | Gestisci i segreti nel Azure Key Vault.
*.servicebus.windows.net | Comunicazione tra l'appliance e il servizio Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Connettersi agli URL del servizio Azure Migrate.
*.blob.core.windows.net | Caricare i dati negli account di archiviazione.
http://aka.ms/latestapplianceservices<br/><br/> https://download.microsoft.com/download | Usato per gli aggiornamenti di Azure Migrate Appliance.

## <a name="assessment-port-requirements"></a>Valutazione-requisiti per le porte

**Dispositivo** | **Connection**
--- | ---
Appliance | Connessioni in ingresso sulla porta TCP 3389 per consentire le connessioni Desktop remoto al dispositivo.<br/><br/> Connessioni in ingresso sulla porta 44368 per accedere in remoto all'app di gestione Appliance usando l'URL:```https://<appliance-ip-or-name>:44368``` <br/><br/>Connessioni in uscita sulla porta 443 per inviare i metadati di individuazione e di prestazioni a Azure Migrate.
Server vCenter | Connessioni in ingresso sulla porta TCP 443 per consentire all'appliance di raccogliere i metadati di configurazione e prestazioni per le valutazioni. <br/><br/> Per impostazione predefinita, l'appliance si connette a vCenter sulla porta 443. Se il server vCenter è in ascolto su una porta diversa, è possibile modificare la porta quando si configura l'individuazione.


## <a name="agentless-migration-vmware-server-requirements"></a>Migrazione senza agente-requisiti del server VMware

Questa tabella riepiloga il supporto per la valutazione e le limitazioni per i server di virtualizzazione VMware.

**Supporto** | **Dettagli**
--- | ---
Server vCenter | Versione 5,5, 6,0, 6,5 o 6,7.
VMware vSphere | Versione 5,5, 6,0, 6,5 o 6,7,

## <a name="agentless-migration-vcenter-server-permissions"></a>Migrazione senza agente-autorizzazioni server vCenter

**Autorizzazioni** | **Dettagli**
--- | ---
Datastore.Browse | Consente l'esplorazione dei file di log della macchina virtuale per risolvere i problemi di creazione ed eliminazione di snapshot.
Datastore.LowLevelFileOperations | Consente di eseguire operazioni di lettura/scrittura/eliminazione/ridenominazione nel browser dell'archivio dati per risolvere i problemi relativi alla creazione e all'eliminazione di snapshot.
VirtualMachine.Configuration.DiskChangeTracking | Consenti abilitazione o disabilitazione del rilevamento delle modifiche dei dischi delle macchine virtuali, per eseguire il pull dei blocchi di dati modificati tra gli snapshot
VirtualMachine.Configuration.DiskLease | Consenti operazioni di lease del disco per una macchina virtuale, per leggere il disco utilizzando il VMware vSphere Virtual Disk Development Kit (VDDK).
VirtualMachine.Provisioning.AllowReadOnlyDiskAccess | Consente di aprire un disco in una macchina virtuale per leggere il disco usando VDDK.
VirtualMachine.Provisioning.AllowVirtualMachineDownload  | Consente le operazioni di lettura sui file associati a una macchina virtuale, per scaricare i log e risolvere i problemi se si verifica un errore.
VirtualMachine.SnapshotManagement.* | Consente la creazione e la gestione di snapshot VM per la replica.
Macchina virtuale. interazione. Spegnere | Consentire la spegnimento della VM durante la migrazione ad Azure.


## <a name="agentless-migration-vmware-vm-requirements"></a>Migrazione senza agente-requisiti per le macchine virtuali VMware

**Supporto** | **Dettagli**
--- | ---
**Sistemi operativi supportati** | È possibile eseguire la migrazione dei sistemi operativi [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) supportati da Azure con la migrazione senza agenti.
**Modifiche necessarie per Azure** | Alcune macchine virtuali potrebbero richiedere modifiche in modo che possano essere eseguite in Azure. Azure Migrate apporta automaticamente queste modifiche per i sistemi operativi seguenti:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> Per altri sistemi operativi, è necessario apportare modifiche manualmente prima della migrazione. Gli articoli pertinenti contengono istruzioni su come eseguire questa operazione.
**Avvio di Linux** | Se/boot si trova in una partizione dedicata, deve risiedere nel disco del sistema operativo e non essere distribuito tra più dischi.<br/> Se/boot fa parte della partizione radice (/), la partizione '/' deve trovarsi nel disco del sistema operativo e non si estende su altri dischi.
**Avvio UEFI** | Le macchine virtuali con avvio UEFI non sono supportate per la migrazione.
**Dimensioni disco** | disco del sistema operativo da 2 TB; 4 TB per i dischi dati.
**Limiti del disco** |  Fino a 60 dischi per macchina virtuale.
**Dischi/volumi crittografati** | Le macchine virtuali con dischi/volumi crittografati non sono supportate per la migrazione.
**Cluster di dischi condivisi** | Non supportati.
**Dischi indipendenti** | Non supportati.
**Dischi RDM/passthrough** | Se le VM contengono dischi RDM o passthrough, questi dischi non verranno replicati in Azure.
**NFS** | I volumi NFS montati come volumi nelle macchine virtuali non verranno replicati.
**destinazioni iSCSI** | Le macchine virtuali con destinazioni iSCSI non sono supportate per la migrazione senza agenti.
**I/o a percorsi multipli** | Non supportati.
**Storage vMotion** | Non supportati. La replica non funziona se una macchina virtuale usa Storage vMotion.
**NIC raggruppate** | Non supportati.
**IPv6** | Non supportati.
**Disco di destinazione** | È possibile eseguire la migrazione delle macchine virtuali solo a Managed Disks (standard HDD, unità SSD Premium) in Azure.
**Replica simultanea** | 100 VM per ogni server vCenter. Se si dispone di altro, eseguirne la migrazione in batch di 100.


## <a name="agentless-migration-appliance-requirements"></a>Migrazione senza agente-requisiti del dispositivo


**Supporto** | **Dettagli**
--- | ---
**ESXi** | La macchina virtuale dell'appliance deve essere distribuita in un host ESXi che esegue la versione 5,5 o successiva.
**Progetto Azure Migrate** | Un appliance può essere associato a un singolo progetto.
**server vCenter** | Un dispositivo può individuare fino a 10.000 VM VMware in una server vCenter.<br/> Un appliance può connettersi a una server vCenter.
**VDDK** | Se si esegue una migrazione senza agente con Azure Migrate migrazione del server, è necessario installare il VMware vSphere VDDK nella macchina virtuale del dispositivo.

## <a name="agentless-migration-url-access-requirements"></a>Migrazione senza agente-requisiti di accesso agli URL

Il dispositivo Azure Migrate necessita della connettività Internet a Internet.

- Quando si distribuisce il dispositivo, Azure Migrate esegue un controllo della connettività agli URL riepilogati nella tabella seguente.
- Se si usa un proxy basato su URL, consentire l'accesso a questi URL, assicurandosi che il proxy risolva tutti i record CNAME ricevuti durante la ricerca degli URL.

**URL** | **Dettagli**  
--- | ---
*.portal.azure.com | Passare al Azure Migrate nel portale di Azure.
*.windows.net | Accedere alla propria sottoscrizione di Azure.
*.microsoftonline.com | Creare app Active Directory per l'appliance per comunicare con il servizio Azure Migrate.
management.azure.com | Creare app Active Directory per l'appliance per comunicare con il servizio Azure Migrate.
dc.services.visualstudio.com | Caricare i log delle app usati per il monitoraggio interno.
*.vault.azure.net | Gestisci i segreti nel Azure Key Vault.
*.servicebus.windows.net | Comunicazione tra l'appliance e il servizio Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Connettersi agli URL del servizio Azure Migrate.
*.blob.core.windows.net | Caricare i dati negli account di archiviazione.
http://aka.ms/latestapplianceservices<br/><br/> https://download.microsoft.com/download | Usato per gli aggiornamenti di Azure Migrate Appliance.


## <a name="agentless-migration-port-requirements"></a>Migrazione senza agente-requisiti per le porte

**Dispositivo** | **Connection**
--- | ---
Appliance | Connessioni in uscita sulla porta 443 per caricare i dati replicati in Azure e per comunicare con Azure Migrate Services che orchestrano la replica e la migrazione.
Server vCenter | Connessioni in ingresso sulla porta 443 per consentire all'appliance di orchestrare la replica: creare snapshot, copiare dati, rilasciare snapshot
host vSphere/EXSI | In ingresso sulla porta TCP 902 per l'appliance per replicare i dati dagli snapshot.


## <a name="agent-based-migration-vmware-server-requirements"></a>Migrazione basata su agenti-requisiti del server VMware

Questa tabella riepiloga il supporto per la valutazione e le limitazioni per i server di virtualizzazione VMware.

**Supporto** | **Dettagli**
--- | ---
Server vCenter | Versione 5,5, 6,0, 6,5 o 6,7.
VMware vSphere | Versione 5,5, 6,0, 6,5 o 6,7.

### <a name="agent-based-migration-vcenter-server-permissions"></a>Migrazione basata su agenti-autorizzazioni di server vCenter

Account di sola lettura per server vCenter.

## <a name="agent-based-migration-replication-appliance-requirements"></a>Migrazione basata su agenti-requisiti dell'appliance di replica

I requisiti per l' [appliance di replica](migrate-replication-appliance.md) usato per la migrazione basata su agenti di VM VMware e server fisici con la migrazione di Azure migrate server sono riepilogati nella tabella.

> [!NOTE]
> Quando si configura l'appliance di replica usando il modello OVA fornito nell'hub Azure Migrate, l'Appliance esegue Windows Server 2016 e soddisfa i requisiti di supporto. Se si configura manualmente l'appliance di replica in un server fisico, assicurarsi che sia conforme ai requisiti.



**Componente** | **Requisito**
--- | ---
 | **Impostazioni VMware** (Appliance VM VMware)
PowerCLI | Se l'appliance di replica è in esecuzione in una VM VMware, è necessario installare la [versione 6,0 di PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) .
Tipo di scheda di interfaccia di rete | VMXNET3 (se il dispositivo è una macchina virtuale VMware)
 | **Impostazioni hardware**
Core CPU | 8
RAM | 16 GB
Numero di dischi | Tre Il disco del sistema operativo, il disco della cache del server di elaborazione e l'unità di conservazione.
Spazio libero su disco (cache) | 600 GB
Spazio libero su disco (disco di conservazione) | 600 GB
**Impostazioni software** |
Sistema operativo | Windows Server 2016 o Windows Server 2012 R2
Impostazioni locali del sistema operativo | Inglese (en-us)
TLS | TLS 1,2 deve essere abilitato.
.NET Framework | Nel computer deve essere installato .NET Framework 4,6 o versione successiva (con crittografia avanzata abilitata.
MySQL | MySQL deve essere installato nell'appliance.<br/> MySQL deve essere installato. È possibile eseguire l'installazione manualmente oppure Site Recovery possibile installarla durante la distribuzione dell'appliance.
Altre app | Non eseguire altre app nell'appliance di replica.
Ruoli di Windows Server | Non abilitare questi ruoli: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V
Criteri di gruppo | Non abilitare questi criteri di gruppo: <br> - Impedisci accesso al prompt dei comandi <br> - Impedisci accesso agli strumenti di modifica del Registro di sistema <br> - Logica di attendibilità per file allegati <br> - Attiva l'esecuzione di script <br> [Altre informazioni](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Nessun sito Web predefinito preesistente <br> - Nessun sito Web o applicazione preesistente in ascolto sulla porta 443 <br>- Abilitare l'[autenticazione anonima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Abilitare l'impostazione di [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)
**Impostazioni di rete** |
Tipo di indirizzo IP | statico
Porte | 443 (orchestrazione del canale di controllo)<br>9443 (trasporto dei dati)
Tipo di scheda di interfaccia di rete | VMXNET3

### <a name="replication-appliance-url-access"></a>Accesso con URL dell'appliance di replica

L'appliance di replica deve accedere a questi URL.

**URL** | **Dettagli**
--- | ---
\*.backup.windowsazure.com | Usato per il coordinamento e il trasferimento dei dati di replica
\*.store.core.windows.net | Usato per il coordinamento e il trasferimento dei dati replicati
\*.blob.core.windows.net | Usato per accedere all'account di archiviazione in cui sono archiviati i dati replicati
\*.hypervrecoverymanager.windowsazure.com | Usato per il coordinamento e le operazioni di gestione della replica
https:\//management.azure.com | Usato per il coordinamento e le operazioni di gestione della replica
*.services.visualstudio.com | Utilizzato per scopi di telemetria (Facoltativo)
time.nist.gov | Usati per controllare la sincronizzazione tra ora di sistema e ora globale.
time.windows.com | Usati per controllare la sincronizzazione tra ora di sistema e ora globale.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.Live.com <br/> https:\//Graph.Windows.NET <br/> https:\//login.windows.net <br/> https:\//www.Live.com <br/> https:\//www.microsoft.com  | L'installazione di OVF richiede l'accesso a questi URL. Vengono usati per la gestione di identità e controllo di accesso da Azure Active Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Per completare il download di MySQL


#### <a name="mysql-installation-options"></a>Opzioni di installazione di MySQL

MySQL può essere installato nell'appliance di replica usando uno di questi metodi.

**Metodo** | **Dettagli**
--- | ---
Scaricare e installare manualmente | Scaricare l'applicazione MySQL & posizionarla nella cartella C:\Temp\ASRSetup, quindi installarla manualmente.<br/> Quando si configura l'appliance, MySQL viene visualizzato come già installato.
Senza download online | Inserire l'applicazione MySQL Installer nella cartella C:\Temp\ASRSetup. Quando si installa il dispositivo e si fa clic per scaricare e installare MySQL, il programma di installazione userà il programma di installazione aggiunto.
Scaricare e installare in Azure Migrate | Quando si installa l'appliance e viene richiesto MySQL, selezionare **Scarica e installa**.



## <a name="agent-based-migration-vmware-vm-requirements"></a>Migrazione basata su agenti-requisiti per le macchine virtuali VMware

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
**Cluster di dischi condivisi** | Non supportati.
**Dischi indipendenti** | Supportato.
**Dischi pass-through** | Supportato.
**NFS** | I volumi NFS montati come volumi nelle macchine virtuali non verranno replicati.
destinazioni iSCSI | Le macchine virtuali con destinazioni iSCSI non sono supportate per la migrazione senza agenti.
**I/o a percorsi multipli** | Non supportati.
**Storage vMotion** | Supportato
**NIC raggruppate** | Non supportati.
**IPv6** | Non supportati.




## <a name="agent-based-migration-url-access-requirements"></a>Migrazione basata su agenti-requisiti di accesso agli URL

Il servizio Mobility in esecuzione su macchine virtuali VMware richiede la connettività Internet.

Quando si distribuisce il servizio Mobility, viene eseguita una verifica della connettività agli URL riepilogati nella tabella seguente.


**URL** | **Dettagli**  
--- | ---
*.portal.azure.com | Passare al Azure Migrate nel portale di Azure.
*.windows.net | Accedere alla propria sottoscrizione di Azure.
*.microsoftonline.com | Creare app Active Directory per l'appliance per comunicare con il servizio Azure Migrate.
management.azure.com | Creare app Active Directory per l'appliance per comunicare con il servizio Azure Migrate.
dc.services.visualstudio.com | Caricare i log delle app usati per il monitoraggio interno.
*.vault.azure.net | Gestisci i segreti nel Azure Key Vault.
*.servicebus.windows.net | Comunicazione tra l'appliance e il servizio Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Connettersi agli URL del servizio Azure Migrate.
*.blob.core.windows.net | Caricare i dati negli account di archiviazione.

## <a name="agent-based-migration-port-requirements"></a>Migrazione basata su agenti-requisiti per le porte

**Dispositivo** | **Connection**
--- | ---
Macchine virtuali | Il servizio Mobility in esecuzione nelle VM comunica con l'appliance di replica locale (server di configurazione) sulla porta HTTPS 443 in ingresso, per la gestione della replica.<br/><br/> Le macchine virtuali inviano i dati della replica al server di elaborazione (in esecuzione sul computer del server di configurazione) sulla porta HTTPS 9443 in ingresso. La porta può essere modificata.
Appliance di replica | L'appliance di replica orchestra la replica con Azure tramite la porta HTTPS 443 in uscita.
Server di elaborazione | Il server di elaborazione riceve i dati di replica, li ottimizza e li crittografa e li invia ad archiviazione di Azure tramite la porta 443 in uscita.<br/> Per impostazione predefinita, il server di elaborazione viene eseguito nell'appliance di replica.

## <a name="azure-vm-requirements"></a>Requisiti per le VM di Azure

Tutte le macchine virtuali locali replicate in Azure devono soddisfare i requisiti della macchina virtuale di Azure riepilogati in questa tabella. Quando Site Recovery esegue un controllo dei prerequisiti per la replica, il controllo ha esito negativo se alcuni dei requisiti non vengono soddisfatti.

**Componente** | **Requisiti** | **Dettagli**
--- | --- | ---
Sistema operativo guest | Verificare i sistemi operativi supportati per le [macchine virtuali VMware tramite la replica senza agenti](#agentless-migration-vmware-vm-requirements)e per le [macchine virtuali VMware tramite la replica basata su agenti](#agent-based-migration-vmware-vm-requirements).<br/> È possibile eseguire la migrazione di qualsiasi carico di lavoro in esecuzione in un sistema operativo supportato. | Il controllo ha esito negativo se non supportato.
Architettura del sistema operativo guest | 64 bit. | Il controllo ha esito negativo se non supportato.
Dimensioni disco del sistema operativo | Fino a 2.048 GB. | Il controllo ha esito negativo se non supportato.
Conteggio dischi del sistema operativo | 1 | Il controllo ha esito negativo se non supportato.
Conteggio dischi dati | 64 o un numero inferiore. | Il controllo ha esito negativo se non supportato.
Dimensioni del disco dati | Fino a 4.095 GB | Il controllo ha esito negativo se non supportato.
Schede di rete | Sono supportate più schede. |
VHD condiviso | Non supportati. | Il controllo ha esito negativo se non supportato.
Disco FC | Non supportati. | Il controllo ha esito negativo se non supportato.
BitLocker | Non supportati. | Prima di abilitare la replica per un computer, occorre disabilitare BitLocker.
Nome della VM. | Da 1 a 63 caratteri.<br/> Limitato a lettere, numeri e trattini.<br/><br/> Il nome del computer deve iniziare e terminare con una lettera o un numero. |  Aggiornare il valore nelle proprietà del computer in Site Recovery.
Connetti dopo la migrazione-Windows | Per connettersi alle macchine virtuali di Azure che eseguono Windows dopo la migrazione:<br/> -Prima della migrazione Abilita RDP nella macchina virtuale locale. Assicurarsi che siano aggiunte regole TCP e UDP per il profilo **Pubblico** e che RDP sia consentito in **Windows Firewall** > **App consentite** per tutti i profili.<br/> Per l'accesso VPN da sito a sito, abilitare RDP e consentire il protocollo RDP in **Windows Firewall** -> **app e funzionalità** consentite per le reti di **dominio e private** . Verificare inoltre che il criterio SAN del sistema operativo sia impostato su onlineal. [Altre informazioni](https://support.microsoft.com/kb/3031135) |
Connetti dopo la migrazione-Linux | Per connettersi alle macchine virtuali di Azure dopo la migrazione tramite SSH:<br/> Prima della migrazione, nel computer locale controllare che il servizio Secure Shell sia impostato su avvio e che le regole del firewall consentano una connessione SSH.<br/> Dopo il failover, nella macchina virtuale di Azure, consentire le connessioni in ingresso alla porta SSH per le regole del gruppo di sicurezza di rete nella macchina virtuale sottoposta a failover e per la subnet di Azure a cui è connessa. Aggiungere inoltre un indirizzo IP pubblico per la macchina virtuale. |  


## <a name="next-steps"></a>Passaggi successivi

[Prepararsi per la valutazione e la migrazione di VMware](tutorial-prepare-vmware.md) .
