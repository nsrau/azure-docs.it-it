---
title: Matrice di supporto Azure Migrate per la valutazione di VMware e la migrazione
description: Vengono riepilogate le impostazioni del supporto e le limitazioni per la valutazione e migrazione delle macchine virtuali VMware in Azure usando il servizio Azure Migrate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: raynew
ms.openlocfilehash: 567a6582e193208a7ff4aa37bafefe1dec4f4e8f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811583"
---
# <a name="support-matrix-for-vmware-assessment-and-migration"></a>Matrice di supporto per la valutazione di VMware e la migrazione

È possibile usare la [servizio Azure Migrate](migrate-overview.md) valutare ed eseguire la migrazione di macchine nel cloud di Microsoft Azure. Questo articolo riepiloga le impostazioni del supporto e le limitazioni per la valutazione e la migrazione di macchine virtuali VMware locali.


## <a name="vmware-scenarios"></a>Scenari di VMware

La tabella riepiloga gli scenari supportati per le macchine virtuali VMware.

**Distribuzione** | **Dettagli** 
--- | --- 
**Valutare le macchine virtuali VMware locali** | [Configurare](tutorial-prepare-vmware.md) la prima valutazione.<br/><br/> [Eseguire](scale-vmware-assessment.md) una valutazione su larga scala.
**Eseguire la migrazione di macchine virtuali VMware** | È possibile eseguire la migrazione usando la migrazione senza agente, con alcune limitazioni o usare una migrazione basata su agente. [Altre informazioni](server-migrate-overview.md)


    


## <a name="azure-migrate-projects"></a>Progetti di Azure Migrate

**Supporto** | **Dettagli**
--- | ---
Autorizzazioni di Azure | Sono necessarie autorizzazioni di proprietario o collaboratore nella sottoscrizione per creare un progetto Azure Migrate.
Limitazioni di VMware  | Consente di valutare fino a 35.000 macchine virtuali VMware in un singolo progetto.

Un progetto può includere sia macchine virtuali VMware e le macchine virtuali Hyper-V, fino ai limiti di valutazione.

## <a name="assessment-vmware-server-requirements"></a>Requisiti del server Assessment-VMware

Questa tabella riepiloga il supporto della valutazione e le limitazioni per i server di virtualizzazione VMware.

**Supporto** | **Dettagli**
--- | ---
**Server vCenter** | Si desidera valutare le macchine virtuali VMware deve essere gestite da uno o più server vCenter che esegue 5.5, 6.0, 6.5 o 6.7.

## <a name="assessment-vcenter-server-permissions"></a>Autorizzazioni del Server vCenter di valutazione

Per la valutazione, è necessario un account di sola lettura per il Server vCenter.

## <a name="assessment-appliance-requirements"></a>Requisiti di valutazione di appliance

**Supporto** | **Dettagli**
--- | ---
**ESXi** | L'appliance della macchina virtuale deve essere distribuito in un host ESXi esegue 5.5 o versione successiva.
**Progetto Azure Migrate** | Un dispositivo può essere associato a un singolo progetto.
**vCenter Server** | Un'appliance può individuare fino a 10.000 macchine virtuali VMware in un Server vCenter.<br/> Un'appliance può connettersi a un solo Server vCenter.


## <a name="assessment-url-access-requirements"></a>Requisiti di accesso URL della valutazione

L'appliance Azure Migrate richiede la connettività internet a internet.

- Quando si distribuisce l'appliance, Azure Migrate esegue una verifica della connettività agli URL riepilogati nella tabella riportata di seguito.
- Se si usa un firewall.proxy basato su URL, consentire l'accesso a questi URL, assicurandosi che il proxy venga risolto alcun record CNAME ricevuti durante la ricerca degli URL.

**URL** | **Dettagli**  
--- | --- |
*.portal.azure.com  | Passare a Azure Migrate nel portale di Azure.
*.windows.net | Accedere alla propria sottoscrizione di Azure.
*.microsoftonline.com | Creare app di Active Directory per l'appliance comunicare con il servizio Azure Migrate.
management.azure.com | Creare app di Active Directory per l'appliance comunicare con il servizio Azure Migrate.
dc.services.visualstudio.com | Caricare i log dell'app usati per il monitoraggio interno.
*.vault.azure.net | Gestire i segreti nell'insieme di credenziali chiave di Azure.
*.servicebus.windows.net | Comunicazione tra il dispositivo e il servizio Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Connettersi all'URL del servizio Azure Migrate.
*.blob.core.windows.net | Caricare i dati per gli account di archiviazione.


## <a name="assessment-port-requirements"></a>Requisiti delle porte di valutazione

**Dispositivo** | **Connection**
--- | --- 
Appliance | Connessioni in ingresso sulla porta TCP 3389 per consentire le connessioni desktop remoto all'appliance.<br/> Connessioni sulla porta 44368 per accedere in remoto le app di gestione di appliance usando l'URL in ingresso: https://<appliance-ip-or-name>:44368 <br/>Connessioni in uscita sulla porta 443 per inviare i metadati di individuazione e le prestazioni di Azure Migrate.
Server vCenter | Connessioni sulla porta TCP 443 per consentire l'appliance raccogliere i metadati di configurazione e sulle prestazioni per le valutazioni in ingresso. <br/> L'appliance si connette a vCenter sulla porta 443 per impostazione predefinita. Se il server vCenter è in ascolto su una porta diversa, è possibile modificare la porta quando si configura il rilevamento.


## <a name="agentless-migration-vmware-server-requirements"></a>Requisiti del server VMware-migrazione senza agenti

Questa tabella riepiloga il supporto della valutazione e le limitazioni per i server di virtualizzazione VMware.

**Supporto** | **Dettagli**
--- | ---
**Server vCenter** | Le macchine virtuali VMware, che si esegue la migrazione usando una migrazione senza agente deve essere gestite da uno o più server vCenter che esegue 5.5, 6.0, 6.5 o 6.7.

## <a name="agentless-migration-vcenter-server-permissions"></a>Autorizzazioni del Server vCenter-migrazione senza agenti

**Autorizzazioni** | **Dettagli**
--- | --- 
Datastore.Browse | Consentire l'esplorazione del file di log della macchina virtuale per risolvere i problemi di eliminazione e la creazione di snapshot.
Datastore.LowLevelFileOperations | Consente operazioni di lettura/scrittura/delete/rename nel browser archivio dati, per risolvere i problemi di eliminazione e la creazione di snapshot.
VirtualMachine.Configuration.DiskChangeTracking | Consente di abilitare o disabilitare il rilevamento delle modifiche dei dischi delle macchine Virtuali, per inserire i blocchi modificati dei dati tra gli snapshot
VirtualMachine.Configuration.DiskLease | Consenti operazioni di lease del disco per una macchina virtuale, leggere il disco con VMware vSphere del Kit di sviluppo del disco virtuale (VDDK).
VirtualMachine.Provisioning.AllowReadOnlyDiskAccess | Consentire l'apertura di un disco in una macchina virtuale, leggere il disco usando il VDDK.
VirtualMachine.Provisioning.AllowVirtualMachineDownload  | Consente operazioni di lettura sul file associati a una macchina virtuale, scaricare i log e risolvere i problemi in caso di errore. 
VirtualMachine.SnapshotManagement.* | Consenti la creazione e la gestione di snapshot delle macchine Virtuali per la replica. 
Virtuale Machine.Interaction.Power Off | Consentire alla macchina virtuale verrà spenta durante la migrazione ad Azure.


## <a name="agentless-migration-vmware-vm-requirements"></a>Requisiti per le VM VMware-migrazione senza agenti

**Supporto** | **Dettagli**
--- | ---
**Sistemi operativi supportati** | [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) i sistemi operativi supportati da Azure possono essere migrati utilizzando la migrazione senza agente.
**Modifiche necessarie per Azure** | Alcune macchine virtuali possono richiedere modifiche, in modo che possano eseguire in Azure. Azure Migrate rende tali modifiche automaticamente per i sistemi operativi seguenti:<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> -Debian 7,8<br/><br/> Per altri sistemi operativi, è necessario apportare le modifiche manualmente prima della migrazione. Gli articoli pertinenti contengono le istruzioni su come eseguire questa operazione.
**Avvio di Linux** | Se /boot si trova in una partizione dedicata, dovrebbero risiedere su disco del sistema operativo e non essere distribuito tra più dischi.<br/> Se /boot fa parte della partizione radice (/), quindi la partizione '/' deve essere il disco del sistema operativo e non su altri dischi.
**Avvio UEFI** | Le macchine virtuali con UEFI boot non sono supportate per la migrazione.
**I dischi/volumi crittografati** | Le macchine virtuali con dischi/volumi crittografati non sono supportate per la migrazione.
**Dischi RDM/pass-through** | Se le macchine virtuali hanno dischi RDM o pass-through, questi dischi non verranno replicati in Azure.
**NFS** | Non è possibile replicare volumi NFS montati come volumi nelle macchine virtuali.
**Disco di destinazione** | Le macchine virtuali possono essere eseguite solo a managed disks (HHD standard, premium SSD) in Azure.


## <a name="agentless-migration-appliance-requirements"></a>Requisiti di appliance migrazione senza agenti


**Supporto** | **Dettagli**
--- | ---
**ESXi** | L'appliance della macchina virtuale deve essere distribuito in un host ESXi esegue 5.5 o versione successiva.
**Progetto Azure Migrate** | Un dispositivo può essere associato a un singolo progetto.
**vCenter Server** | Un'appliance può individuare fino a 10.000 macchine virtuali VMware in un Server vCenter.<br/> Un'appliance può connettersi a un solo Server vCenter.
**VDDK** | Se si esegue una migrazione senza agente con la migrazione di Server eseguire la migrazione di Azure, è necessario installare VMware vSphere del Kit di sviluppo del disco virtuale (VDDK) nella macchina virtuale dell'appliance.

## <a name="agentless-migration-url-access-requirements"></a>Requisiti di accesso URL di migrazione senza agenti

L'appliance Azure Migrate richiede la connettività internet a internet.

- Quando si distribuisce l'appliance, Azure Migrate esegue una verifica della connettività agli URL riepilogati nella tabella riportata di seguito.
- Se si usa un firewall.proxy basato su URL, consentire l'accesso a questi URL, assicurandosi che il proxy venga risolto alcun record CNAME ricevuti durante la ricerca degli URL.

**URL** | **Dettagli**  
--- | --- 
*.portal.azure.com | Passare a Azure Migrate nel portale di Azure.
*.windows.net | Accedere alla propria sottoscrizione di Azure.
*.microsoftonline.com | Creare app di Active Directory per l'appliance comunicare con il servizio Azure Migrate.
management.azure.com | Creare app di Active Directory per l'appliance comunicare con il servizio Azure Migrate.
dc.services.visualstudio.com | Caricare i log dell'app usati per il monitoraggio interno.
*.vault.azure.net | Gestire i segreti nell'insieme di credenziali chiave di Azure.
*.servicebus.windows.net | Comunicazione tra il dispositivo e il servizio Azure Migrate.
*.discoverysrv.windowsazure.com<br/> *.migration.windowsazure.com<br/> *.hypervrecoverymanager.windowsazure.com | Connettersi all'URL del servizio Azure Migrate.
*.blob.core.windows.net | Caricare i dati per gli account di archiviazione.


## <a name="agentless-migration-port-requirements"></a>Requisiti di migrazione-port senza agenti

**Dispositivo** | **Connection**
--- | --- 
Appliance | Porta TCP in uscita 3389 per caricare i dati replicati in Azure e per comunicare con Azure Migrate per la replica e la migrazione.
Server vCenter | Le connessioni in ingresso sulla porta TCP 443 per consentire l'appliance orchestrare la replica - creazione di snapshot, copiare i dati, gli snapshot di rilascio
host vSphere/EXSI | Connessioni in entrata sulla porta TCP 902 per l'appliance replicare i dati da snapshot. 


## <a name="agent-based-migration-vmware-server-requirements"></a>Requisiti del server VMware-migrazione basata su agente

Questa tabella riepiloga il supporto della valutazione e le limitazioni per i server di virtualizzazione VMware.

**Supporto** | **Dettagli**
--- | ---
**server vCenter/ESXI** | Le macchine virtuali VMware, che si esegue la migrazione deve essere gestite da uno o più server vCenter 5.5, 6.0, 6.5 o 6.7 è in esecuzione oppure in esecuzione in un host ESXI vSphere versione 5.5, 6.0, 6.5 o 6.7.

### <a name="agent-based-migration-vcenter-server-permissions"></a>Autorizzazioni del Server vCenter-migrazione basata su agente

**Autorizzazioni** | **Dettagli**
--- | --- 
Datastore.AllocateSpace | Consentire l'allocazione dello spazio in un archivio dati per una macchina virtuale, disco virtuale, clonazione o snapshot. 
Datastore.Browse | Consentire l'esplorazione del file di log della macchina virtuale per risolvere i problemi di eliminazione e la creazione di snapshot.
Datastore.LowLevelFileOperations | Consentano la lettura, scrittura, eliminare e rinominare le operazioni nel browser archivio dati per risolvere i problemi di creazione/eliminazione dello snapshot.
Datastore.UpdateVirtualMachineFiles | Consentire l'aggiornamento di percorsi ai file di macchina virtuale in un archivio dati dopo che l'archivio dati è stato resignatured.
Network.AssignNetwork | Consenti assegnazione di una rete a una risorsa di macchina virtuale.
AssignVirtualMachineToResourcePool | Consente l'assegnazione di una macchina virtuale a un pool di risorse.
Resource.MigratePoweredOffVirtualMachine | Consentono la migrazione di una VM spenta a un host o il pool di risorse diverso.
Resource.MigratePoweredOnVirtualMachine | Consentono la migrazione con vMotion, di una macchina virtuale acceso a un host o il pool di risorse diverso.
Tasks.CreateTask | Consentono a un'estensione creare un'attività definita dall'utente.
Tasks.UpdateTask | Consentono a un'estensione aggiornare un'attività definita dall'utente.
VirtualMachine.Configuration. | Consentono di configurare i dispositivi e le opzioni della macchina virtuale.
Virtual Machine.Interaction.AnswerQuestion | Consenti la risoluzione dei problemi con le transizioni di stato della macchina virtuale o errori di runtime.
Virtual Machine.Interaction.DeviceConnection | Possibilità di modificare lo stato dei dispositivi virtuali scollegabili della macchina virtuale.
Machine.Interaction.ConfigureCDMedia virtuale | Consentire la configurazione di un dispositivo DVD o CD-ROM virtuale.
Virtual Machine.Interaction.ConfigureFloppyMedia | Consentire la configurazione di un dispositivo disco floppy virtuale.
Machine.Interaction.PowerOff virtuale | Consente la macchina virtuale verrà spenta durante la migrazione ad Azure.
Machine.Interaction.PowerOn virtuale | Consentire l'accensione di una macchina virtuale spenta e dalla ripresa di una macchina virtuale sospesa.
Virtual Machine.Interaction.VMwareToolsInstall | Consentono di montare e smontare il programma di installazione del CD con gli strumenti VMware come un CD-ROM per il sistema operativo guest.
VirtualMachine.Inventory.CreateNew | Consenti la creazione di una macchina virtuale e l'allocazione delle risorse necessarie.
VirtualMachine.Inventory.Register | È possibile aggiungere una macchina virtuale esistente a un Server vCenter o un inventario di host.
VirtualMachine.Inventory.Unregister | Consenti l'annullamento della registrazione di una macchina virtuale da un Server vCenter o un inventario di host.
VirtualMachine.Provisioning.AllowVirtualMachineFilesUpload | Consenti operazioni di scrittura sul file associati a una macchina virtuale, inclusi file vmx, dischi, i log e nvram.
VirtualMachine.Provisioning.AllowVirtualMachineDownload | Consenti operazioni di lettura sul file associati a una macchina virtuale, per scaricare i log per la risoluzione dei problemi.
VirtualMachine.SnapshotManagement.RemoveSnapshot | Consente la rimozione di uno snapshot dalla cronologia degli snapshot.

## <a name="agent-based-migration-replication-appliance-requirements"></a>Requisiti di appliance replica migrazione basata su agente

I requisiti per il [appliance replica](migrate-replication-appliance.md) usato per la migrazione basata su agente di macchine virtuali VMware e fisici server con la migrazione di Server eseguire la migrazione di Azure sono riepilogati nella tabella.

> [!NOTE]
> Quando si configura l'appliance di replica utilizzando il modello con estensione OVA fornito nell'hub di Azure Migrate, l'appliance viene eseguito Windows Server 2016 e sia conforme ai requisiti di supporto. Se si configura l'appliance di replica manuale in un server fisico, assicurarsi che sia conforme ai requisiti.



**Componente** | **Requisito** 
--- | ---
 | **Le impostazioni di VMware** (appliance di VM VMware)
**PowerCLI** | [PowerCLI versione 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) deve essere installato se l'appliance di replica è in esecuzione in una VM VMware.
**Tipo di interfaccia di rete** | VMXNET3 (se l'appliance è una VM VMware)
 | **Impostazioni hardware** 
Core CPU | 8 
RAM | 16 GB
Numero di dischi | Tre: Il disco del sistema operativo, disco della cache di server di elaborazione e unità di conservazione.
Spazio libero su disco (cache) | 600 GB
Spazio libero su disco (disco di conservazione) | 600 GB
**Impostazioni del software** | 
Sistema operativo | Windows Server 2016 o Windows Server 2012 R2
Impostazioni locali del sistema operativo | Inglese (en-us)
TLS | TLS 1.2 dovrebbe essere abilitato.
.NET Framework | .NET framework 4.6 o versione successiva deve essere installato nel computer (con abilitata la crittografia avanzata.
MySQL | MySQL deve essere installato nell'appliance.<br/> MySQL deve essere installato. È possibile installare manualmente o Site Recovery possa installare durante la distribuzione del dispositivo. 
Altre app | È non deve eseguire altre applicazioni nell'appliance di replica.
Ruoli di Windows Server | Non abilitare questi ruoli: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Criteri di gruppo | Non abilitare questi criteri di gruppo: <br> - Impedisci accesso al prompt dei comandi <br> - Impedisci accesso agli strumenti di modifica del Registro di sistema <br> - Logica di attendibilità per file allegati <br> - Attiva l'esecuzione di script <br> [Altre informazioni](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Nessun sito Web predefinito preesistente <br> - Nessun sito Web o applicazione preesistente in ascolto sulla porta 443 <br>- Abilitare l'[autenticazione anonima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Abilitare l'impostazione di [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 
**Impostazioni di rete** | 
Tipo di indirizzo IP | statico 
Porte | 443 (orchestrazione del canale di controllo)<br>9443 (trasporto dei dati) 
Tipo di scheda di interfaccia di rete | VMXNET3 

### <a name="replication-appliance-url-access"></a>Accesso con URL appliance replica

L'appliance di replica deve avere accesso a questi URL.

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
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | Il programma di installazione OVF deve accedere a questi URL. Vengono usati per la gestione di identità e controllo di accesso da Azure Active Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Per completare il download di MySQL


#### <a name="mysql-installation-options"></a>Opzioni di installazione di MySQL

MySQL può essere installato nell'appliance di replica usando uno di questi metodi.

**Installa** | **Dettagli**
--- | ---
Scaricare e installare manualmente | Scaricare l'applicazione MySQL e inserirlo nella cartella C:\Temp\ASRSetup e quindi installare manualmente.<br/> Quando si configura l'appliance MySQL mostrerà come già installato. 
Non scaricare online | Inserire l'applicazione di programma di installazione di MySQL nella cartella C:\Temp\ASRSetup. Quando si installa l'appliance e fare clic qui per scaricare e installare MySQL, il programma di installazione Usa il programma di installazione che è stato aggiunto. 
Eseguire la migrazione di download di Azure | Quando si installa l'appliance e viene richiesto per MySQL, selezionare **scaricare e installare**.



## <a name="agent-based-migration-vmware-vm-requirements"></a>Requisiti per le VM VMware-migrazione basata su agente

**Supporto** | **Dettagli**
--- | ---
**Carico di lavoro computer** | Azure Migrate supporta la migrazione di qualsiasi carico di lavoro (ad esempio Active Directory, SQL server e così via) in esecuzione in una macchina supportata.
**Sistemi operativi** | Per informazioni aggiornate, vedere la [sistemi operativi supportati](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) per Site Recovery. Azure Migrate fornisce supporto del sistema operativo della macchina virtuale identico.
**Linux file system/archiviazione guest** | Per informazioni aggiornate, vedere la [supporto per Linux file system](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) per Site Recovery. Azure Migrate ha supporto del file system di Linux identico.
**Rete/archiviazione** | Per informazioni aggiornate, vedere la [network](../site-recovery/vmware-physical-azure-support-matrix.md#network) e [archiviazione](../site-recovery/vmware-physical-azure-support-matrix.md#storage) prerequisiti per Site Recovery. Azure Migrate include requisiti di rete/archiviazione identiche.
**Requisiti di Azure** | Per informazioni aggiornate, vedere la [rete di Azure](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [archiviazione](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage), e [calcolo](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) requisiti per il ripristino del sito. Azure Migrate ha requisiti identici per la migrazione di VMware.
**Servizio Mobility** | L'agente del servizio Mobility deve essere installato in ogni macchina virtuale si vuole eseguire la migrazione.
**Disco di destinazione** | Le macchine virtuali possono essere eseguite solo a managed disks (HHD standard, premium SSD) in Azure.

   

## <a name="agent-based-migration-url-access-requirements"></a>Requisiti di accesso URL di migrazione basata su agente

Il servizio Mobility in esecuzione nelle macchine virtuali VMware richiede la connettività internet a internet.

- Quando si distribuisce il servizio Mobility, esegue una verifica della connettività agli URL riepilogati nella tabella riportata di seguito.
- Se si usa un firewall.proxy basato su URL, consentire l'accesso a questi URL, assicurandosi che il proxy venga risolto alcun record CNAME ricevuti durante la ricerca degli URL.

**URL** | **Dettagli**  
--- | --- 
*.portal.azure.com | Passare a Azure Migrate nel portale di Azure.
*.windows.net | Accedere alla propria sottoscrizione di Azure.
*.microsoftonline.com | Creare app di Active Directory per l'appliance comunicare con il servizio Azure Migrate. 
management.azure.com | Creare app di Active Directory per l'appliance comunicare con il servizio Azure Migrate.
dc.services.visualstudio.com | Caricare i log dell'app usati per il monitoraggio interno.
*.vault.azure.net | Gestire i segreti nell'insieme di credenziali chiave di Azure.
*.servicebus.windows.net | Comunicazione tra il dispositivo e il servizio Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Connettersi all'URL del servizio Azure Migrate.
*.blob.core.windows.net | Caricare i dati per gli account di archiviazione.

## <a name="agent-based-migration-port-requirements"></a>Requisiti di migrazione-port basata su agente

**Dispositivo** | **Connection**
--- | --- 
VM | Il servizio Mobility in esecuzione nelle macchine virtuali comunica con il server di configurazione in locale sulla porta HTTPS 443 in ingresso, per la gestione delle repliche.<br/><br/> Le macchine virtuali inviano i dati della replica al server di elaborazione (in esecuzione sul computer del server di configurazione) sulla porta HTTPS 9443 in ingresso. La porta può essere modificata.
Appliance di replica | L'appliance replica Orchestra la replica con Azure tramite la porta HTTPS 443 in uscita.
Server di elaborazione | Il server di elaborazione riceve i dati della replica, li ottimizza e li crittografa, quindi li invia ad Archiviazione di Azure attraverso la porta 443 in uscita.<br/> Per impostazione predefinita il server di elaborazione viene eseguita nell'appliance di replica.

## <a name="azure-vm-requirements"></a>Requisiti per le VM di Azure

Tutto in locale le macchine virtuali replicate in Azure devono soddisfare i requisiti di macchina virtuale di Azure riepilogati in questa tabella. Quando viene eseguito un controllo dei prerequisiti per la replica di Site Recovery, la verifica avrà esito negativo se alcuni dei requisiti non vengono soddisfatti.

**Componente** | **Requisiti** | **Dettagli**
--- | --- | ---
Sistema operativo guest | Verificare i sistemi operativi supportati per [macchine virtuali VMware tramite la replica senza agente](#agentless-migration-vmware-vm-requirements)e per [macchine virtuali VMware tramite replica basata su agente](#agent-based-migration-vmware-vm-requirements).<br/> È possibile eseguire la migrazione di qualsiasi carico di lavoro in esecuzione in un sistema operativo supportato. | Il controllo ha esito negativo se non supportato.
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
Eseguire la connessione dopo la migrazione a Windows | Per connettersi a macchine virtuali di Azure che eseguono Windows dopo la migrazione:<br/> -Prima della migrazione, abilitare RDP nella macchina virtuale in locale. Assicurarsi che siano aggiunte regole TCP e UDP per il profilo **Pubblico** e che RDP sia consentito in **Windows Firewall** > **App consentite** per tutti i profili.<br/> Per l'accesso VPN site-to-site, abilitare RDP e consentire il protocollo RDP nella **Windows Firewall** -> **App e funzionalità consentite** per **dominio e Private** reti. Inoltre, verificare che criterio SAN del sistema operativo è impostato su **OnlineAll**. [Altre informazioni](https://support.microsoft.com/kb/3031135) | 
Eseguire la connessione dopo la migrazione-Linux | Per connettersi a macchine virtuali di Azure dopo la migrazione usando SSH:<br/> Prima della migrazione, nel computer locale, verificare che il servizio Secure Shell sia impostato per l'avvio e che le regole del firewall consentano una connessione SSH.<br/> Dopo il failover, nella VM di Azure, consentire le connessioni in ingresso alla porta SSH per le regole di gruppo di sicurezza di rete nella macchina virtuale con failover e per la subnet di Azure a cui è connessa. Inoltre, aggiungere un indirizzo IP pubblico per la macchina virtuale. |  


## <a name="next-steps"></a>Passaggi successivi

[Preparare VMware](tutorial-prepare-vmware.md) valutazione e la migrazione.








