---
title: Appliance Azure Migrate
description: Viene fornita una panoramica dell'appliance Azure Migrate utilizzata per la valutazione e la migrazione dei server.
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: 71a17211a530b4cb55764f3b3ab84ff5a4d5f3e6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82106371"
---
# <a name="azure-migrate-appliance"></a>Appliance Azure Migrate

Questo articolo riepiloga i prerequisiti e i requisiti di supporto per la Azure Migrate Appliance. 

## <a name="deployment-scenarios"></a>Scenari di distribuzione

Il dispositivo Azure Migrate viene usato negli scenari seguenti.

**Scenario** | **Strumento** | **Utilizzato per** 
--- | --- | ---
**Valutazione VM VMware** | Azure Migrate: valutazione del server | Individuare le macchine virtuali VMware<br/><br/> Individuare le app e le dipendenze del computer<br/><br/> Raccolta dei metadati del computer e delle prestazioni per le valutazioni.
**Migrazione senza agenti di macchine virtuali VMware** | Azure Migrate: migrazione del server | Individuare le macchine virtuali VMware <br/><br/> Eseguire la replica di macchine virtuali VMware con migrazione senza agenti.
**Valutazione della macchina virtuale Hyper-V** | Azure Migrate: valutazione del server | Individuare le VM Hyper-V<br/><br/> Raccolta dei metadati del computer e delle prestazioni per le valutazioni.
**Valutazione computer fisico** |  Azure Migrate: valutazione del server |  Individuare i server fisici o le macchine virtuali considerate come server fisici.<br/><br/> Raccolta dei metadati del computer e delle prestazioni per le valutazioni.

## <a name="deployment-methods"></a>Metodi di distribuzione

Il dispositivo può essere distribuito usando due metodi:

- Il dispositivo può essere distribuito usando un modello per le macchine virtuali VMware e Hyper-V (modello OVA per VMware o VHD per Hyper-V).
- Se non si vuole usare un modello, è possibile distribuire l'appliance per VMware o Hyper-V usando uno script di PowerShell.
- In Azure per enti pubblici è necessario distribuire l'appliance usando uno script.
- Per i server fisici, è sempre necessario distribuire l'appliance usando uno script.
- I collegamenti di download sono disponibili nelle tabelle seguenti.


## <a name="appliance---vmware"></a>Appliance-VMware 

La tabella seguente riepiloga i requisiti di Azure Migrate Appliance per VMware.

**Requisito** | **VMware** 
--- | ---
**Componenti del dispositivo** | Il dispositivo include i componenti seguenti:<br/><br/> - **App di gestione**: si tratta di un'app Web per l'input dell'utente durante la distribuzione dell'appliance. Usato durante la valutazione dei computer per la migrazione ad Azure.<br/> - **Agente di individuazione**: l'agente raccoglie i dati di configurazione del computer. Usato durante la valutazione dei computer per la migrazione ad Azure.<br/>- **Agente di valutazione**: l'agente raccoglie i dati sulle prestazioni. Usato durante la valutazione dei computer per la migrazione ad Azure.<br/>- **Aggiornamento automatico del servizio**: Aggiorna i componenti del dispositivo (viene eseguito ogni 24 ore).<br/>- **Agente DRA**: Orchestra la replica della macchina virtuale e coordina la comunicazione tra computer replicati e Azure. Usato solo quando si esegue la replica di macchine virtuali VMware in Azure usando la migrazione senza agente.<br/>- **Gateway**: Invia i dati replicati in Azure. Usato solo quando si esegue la replica di macchine virtuali VMware in Azure usando la migrazione senza agente.
**Distribuzione supportata** | Distribuire come macchina virtuale VMware usando il modello OVA.<br/><br/> Distribuire come macchina virtuale VMware o macchina fisica usando lo script di installazione di PowerShell.
**Supporto del progetto** |  Un appliance può essere associato a un singolo progetto. <br/> Un numero qualsiasi di Appliance può essere associato a un singolo progetto.<br/> 
**Limiti di individuazione** | Un dispositivo può individuare fino a 10.000 VM VMware in una server vCenter.<br/> Un appliance può connettersi a una singola server vCenter.
**Modello OVA** | Scaricare dal portale o da https://aka.ms/migrate/appliance/vmware.<br/><br/> Le dimensioni del download sono di 11,2 GB.<br/><br/> Il modello di appliance scaricato è disponibile con una licenza di valutazione di Windows Server 2016, valida per 180 giorni. Se il periodo di valutazione è prossimo alla scadenza, è consigliabile scaricare e distribuire una nuova appliance oppure attivare la licenza del sistema operativo della VM del dispositivo.
**Script di PowerShell** | [Download](https://go.microsoft.com/fwlink/?linkid=2105112)dello script.<br/><br/> 
**Software/hardware** |  Il dispositivo deve essere eseguito in un computer con Windows Server 2016, 32 GB di RAM, 8 vCPU, circa 80 GB di spazio di archiviazione su disco e un Commuter virtuale esterno.<br/> Il dispositivo richiede l'accesso a Internet, direttamente o tramite un proxy.<br/><br/> Se si esegue l'appliance in una VM VMware, è necessario disporre di risorse sufficienti nel server vCenter per allocare una macchina virtuale che soddisfi i requisiti.<br/><br/> Se l'Appliance viene eseguita in un computer fisico, assicurarsi che esegua Windows Server 2016 e soddisfi i requisiti hardware. 
**Requisiti di VMware** | Se si distribuisce l'appliance come macchina virtuale VMware, questa deve essere distribuita in un host ESXi che esegue la versione 5,5 o successiva.<br/><br/> server vCenter che esegue 5,5, 6,0, 6,5 o 6,7.
**VDDK (migrazione senza agenti)** | Se si distribuisce l'appliance come macchina virtuale VMware e si esegue una migrazione senza agenti, è necessario installare il VMware vSphere VDDK nella macchina virtuale del dispositivo.
**Valore hash-OVA** | [Verificare](tutorial-assess-vmware.md#verify-security) i valori hash del modello OVA.
**Valore hash-script di PowerShell** | [Verificare](deploy-appliance-script.md#verify-file-security) i valori hash dello script di PowerShell.




## <a name="appliance---hyper-v"></a>Appliance-Hyper-V

**Requisito** | **Hyper-V** 
--- | ---
**Componenti del dispositivo** | Il dispositivo include i componenti seguenti:<br/><br/>- **App di gestione**: si tratta di un'app Web per l'input dell'utente durante la distribuzione dell'appliance. Usato durante la valutazione dei computer per la migrazione ad Azure.<br/> - **Agente di individuazione**: l'agente raccoglie i dati di configurazione del computer. Usato durante la valutazione dei computer per la migrazione ad Azure.<br/>- **Agente di valutazione**: l'agente raccoglie i dati sulle prestazioni. Usato durante la valutazione dei computer per la migrazione ad Azure.<br/>- **Aggiornamento automatico del servizio**: Aggiorna i componenti del dispositivo (viene eseguito ogni 24 ore).
**Distribuzione supportata** | Distribuire come macchina virtuale Hyper-V usando un modello VHD.<br/><br/> Eseguire la distribuzione come macchina virtuale o macchina fisica Hyper-V usando uno script di installazione di PowerShell.
**Supporto del progetto** |  Un appliance può essere associato a un singolo progetto. <br/> Un numero qualsiasi di Appliance può essere associato a un singolo progetto.<br/> 
**Limiti di individuazione** | Un'appliance può individuare fino a 5000 macchine virtuali Hyper-V.<br/> Un appliance può connettersi a un massimo di 300 host Hyper-V.
**Modello VHD** | Cartella compressa, incluso VHD. Scaricare dal portale o da https://aka.ms/migrate/appliance/hyperv.<br/><br/> Le dimensioni del download sono di 10 GB.<br/><br/> Il modello di appliance scaricato è disponibile con una licenza di valutazione di Windows Server 2016, valida per 180 giorni. Se il periodo di valutazione è prossimo alla scadenza, è consigliabile scaricare e distribuire una nuova appliance oppure attivare la licenza del sistema operativo della VM del dispositivo.
**Script di PowerShell** | [Download](https://go.microsoft.com/fwlink/?linkid=2105112)dello script.<br/><br/> 
**Software/hardware***   |  Il dispositivo deve essere eseguito in un computer con Windows Server 2016, 32 GB di RAM, 8 vCPU, circa 80 GB di spazio di archiviazione su disco e un Commuter virtuale esterno.<br/> L'appliance necessita di un indirizzo IP statico o dinamico e richiede l'accesso a Internet, direttamente o tramite un proxy.<br/><br/> Se si esegue l'appliance come macchina virtuale Hyper-V, è necessario disporre di risorse sufficienti nell'host Hyper-V per allocare 16 GB di RAM, 8 vCPU, circa 80 GB di spazio di archiviazione e un commute esterno per la macchina virtuale dell'appliance.<br/><br/> Se l'Appliance viene eseguita in un computer fisico, assicurarsi che esegua Windows Server 2016 e soddisfi i requisiti hardware. 
**Requisiti di Hyper-V** | Se si distribuisce il dispositivo con il modello VHD, la macchina virtuale dell'appliance fornita da Azure Migrate è la macchina virtuale Hyper-V versione 5,0.<br/><br/> L'host Hyper-V deve eseguire Windows Server 2012 R2 o versione successiva. 
**Valore hash-VHD** | [Verificare](tutorial-assess-hyper-v.md#verify-security) i valori hash del modello VHD.
**Valore hash-script di PowerShell** | [Verificare](deploy-appliance-script.md#verify-file-security) i valori hash dello script di PowerShell.


## <a name="appliance---physical"></a>Appliance-fisico

**Requisito** | **Fisico** 
--- | ---
**Componenti del dispositivo** | Il dispositivo include i componenti seguenti: <br/><br/> - **App di gestione**: si tratta di un'app Web per l'input dell'utente durante la distribuzione dell'appliance. Usato durante la valutazione dei computer per la migrazione ad Azure.<br/> - **Agente di individuazione**: l'agente raccoglie i dati di configurazione del computer. Usato durante la valutazione dei computer per la migrazione ad Azure.<br/>- **Agente di valutazione**: l'agente raccoglie i dati sulle prestazioni. Usato durante la valutazione dei computer per la migrazione ad Azure.<br/>- **Aggiornamento automatico del servizio**: Aggiorna i componenti del dispositivo (viene eseguito ogni 24 ore).
**Distribuzione supportata** | Distribuire come computer fisico dedicato o una VM usando uno script di installazione di PowerShell. Lo script è disponibile per il download dal portale.
**Supporto del progetto** |  Un appliance può essere associato a un singolo progetto. <br/> Un numero qualsiasi di Appliance può essere associato a un singolo progetto.<br/> 
**Limiti di individuazione** | Un'appliance può individuare fino a 250 di server fisici.
**Script di PowerShell** | Scaricare lo script (AzureMigrateInstaller. ps1) in una cartella compressa dal portale. [Altre informazioni](tutorial-assess-physical.md#set-up-the-appliance) In alternativa, [scaricare direttamente](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> Le dimensioni del download sono pari a 59,7 MB.
**Software/hardware** |  Il dispositivo deve essere eseguito in un computer con Windows Server 2016, 32 GB di RAM, 8 vCPU, circa 80 GB di spazio di archiviazione su disco e un Commuter virtuale esterno.<br/> L'appliance necessita di un indirizzo IP statico o dinamico e richiede l'accesso a Internet, direttamente o tramite un proxy.<br/><br/> Se l'Appliance viene eseguita in un computer fisico, assicurarsi che esegua Windows Server 2016 e soddisfi i requisiti hardware. 
**Valore hash** | [Verificare](deploy-appliance-script.md#verify-file-security) i valori hash dello script di PowerShell.

## <a name="url-access"></a>accesso con URL

Il dispositivo Azure Migrate richiede la connettività a Internet.

- Quando si distribuisce il dispositivo, Azure Migrate esegue un controllo della connettività agli URL richiesti.
- Se si usa un proxy basato su URL per connettersi a Internet, è necessario consentire l'accesso a questi URL, assicurandosi che il proxy risolva tutti i record CNAME ricevuti durante la ricerca degli URL.

### <a name="public-cloud-urls"></a>URL del cloud pubblico

**URL** | **Dettagli**  
--- | --- |
*.portal.azure.com  | Passare al portale di Azure.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Accedere alla sottoscrizione di Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Creare app Azure Active Directory (AD) per la comunicazione del dispositivo con Azure Migrate.
management.azure.com | Creare app Azure AD per l'appliance per comunicare con il servizio Azure Migrate.
dc.services.visualstudio.com | Caricare i log delle app usati per il monitoraggio interno.
*.vault.azure.net | Gestisci i segreti nel Azure Key Vault.
aka.ms/* | Consente l'accesso a collegamenti aka. Usato per gli aggiornamenti di Azure Migrate Appliance.
download.microsoft.com/download | Consenti i download dal Download Microsoft.
*.servicebus.windows.net | Comunicazione tra l'appliance e il servizio Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Connettersi agli URL del servizio Azure Migrate.
*.hypervrecoverymanager.windowsazure.com | **Usato per la migrazione senza agenti VMware**<br/><br/> Connettersi agli URL del servizio Azure Migrate.
*.blob.core.windows.net |  **Usato per la migrazione senza agenti VMware**<br/><br/>Caricare i dati nella risorsa di archiviazione per la migrazione.

### <a name="government-cloud-urls"></a>URL cloud per enti pubblici

**URL** | **Dettagli**  
--- | --- |
*.portal.azure.us  | Passare al portale di Azure.
graph.windows.net | Accedere alla sottoscrizione di Azure.
login.microsoftonline.us  | Creare app Azure Active Directory (AD) per la comunicazione del dispositivo con Azure Migrate.
management.usgovcloudapi.net | Creare app Azure AD per l'appliance per comunicare con il servizio Azure Migrate.
dc.services.visualstudio.com | Caricare i log delle app usati per il monitoraggio interno.
*. vault.usgovcloudapi.net | Gestisci i segreti nel Azure Key Vault.
aka.ms/* | Consente l'accesso a collegamenti aka. Usato per gli aggiornamenti di Azure Migrate Appliance.
download.microsoft.com/download | Consenti i download dal Download Microsoft.
*. servicebus.usgovcloudapi.net  | Comunicazione tra l'appliance e il servizio Azure Migrate.
*. discoverysrv.windowsazure.us <br/> *. migration.windowsazure.us | Connettersi agli URL del servizio Azure Migrate.
*.hypervrecoverymanager.windowsazure.us | **Usato per la migrazione senza agenti VMware**<br/><br/> Connettersi agli URL del servizio Azure Migrate.
*.blob.core.usgovcloudapi.net  |  **Usato per la migrazione senza agenti VMware**<br/><br/>Caricare i dati nella risorsa di archiviazione per la migrazione.
*. applicationinsights.us | Caricare i log delle app usati per il monitoraggio interno.





## <a name="collected-data---vmware"></a>Dati raccolti-VMware

L'appliance raccoglie i metadati, i dati sulle prestazioni e i dati di analisi delle dipendenze (se si usa l' [analisi delle dipendenze](concepts-dependency-visualization.md) senza agenti).

### <a name="metadata"></a>Metadati

I metadati individuati dal dispositivo Azure Migrate consentono di determinare se i computer e le app sono pronti per la migrazione ad Azure, le macchine virtuali e le app di dimensioni appropriate, i costi dei piani e l'analisi delle dipendenze dell'applicazione. Microsoft non usa questi dati in alcun controllo di conformità delle licenze.

Di seguito è riportato l'elenco completo dei metadati delle macchine virtuali VMware che l'appliance raccoglie e Invia ad Azure.

**DATI** | **COUNTER**
--- | --- 
**Dettagli computer** | 
ID macchina virtuale | vm.Config.InstanceUuid 
Nome della VM. | vm.Config.Name
ID server vCenter | VMwareClient. Instance. UUID
Descrizione macchina virtuale | vm.Summary.Config.Annotation
Nome prodotto con licenza | vm.Client.ServiceContent.About.LicenseProductName
Tipo di sistema operativo | VM. SummaryConfig.GuestFullName
Tipo di avvio | vm.Config.Firmware
Numero di core | vm.Config.Hardware.NumCPU
Memoria (MB) | vm.Config.Hardware.MemoryMB
Numero di dischi | VM. Config. hardware. Device. ToList (). FindAll (x => è VirtualDisk). Count
Elenco dimensioni disco | VM. Config. hardware. Device. ToList (). FindAll (x => è VirtualDisk)
Elenco schede di rete | VM. Config. hardware. Device. ToList (). FindAll (x => è VirtualEthernet). Count
Uso della CPU | cpu.usage.average
Utilizzo della memoria |mem.usage.average
**Dettagli per disco** | 
Valore chiave disco | disk.Key
Numero Dikunit | disk.UnitNumber
Valore chiave controller del disco | disk.ControllerKey.Value
Gigabyte con provisioning | virtualDisk.DeviceInfo.Summary
Nome del disco | Valore generato tramite disco. UnitNumber, disco. Chiave, disco. ControllerKey. VAlue
Operazioni di lettura per secondo | virtualDisk.numberReadAveraged.average
Operazioni di scrittura per secondo | virtualDisk.numberWriteAveraged.average
Velocità effettiva lettura (MB al secondo) | virtualDisk.read.average
Velocità effettiva scrittura (MB al secondo) | virtualDisk.write.average
**Dettagli per NIC** | 
Nome scheda di rete | nic.Key
Indirizzo MAC | ((VirtualEthernetCard)nic).MacAddress
Indirizzi IPv4 | vm.Guest.Net
Indirizzi IPv6 | vm.Guest.Net
Velocità effettiva lettura (MB al secondo) | net.received.average
Velocità effettiva scrittura (MB al secondo) | net.transmitted.average
**Dettagli percorso inventario** | 
Name | container.GetType().Name
Tipo di oggetto figlio | container.ChildType
Informazioni di riferimento | container.MoRef
Dettagli elemento padre | Container.Parent
Dettagli cartella per macchina virtuale | ((Folder)container).ChildEntity.Type
Dettagli Data Center per macchina virtuale | ((Datacenter)container).VmFolder
Dettagli Data Center per cartella host | ((Datacenter)container).HostFolder
Dettagli cluster per host | ((ClusterComputeResource) contenitore). Host
Dettagli host per macchina virtuale | ((HostSystem) contenitore). VM

### <a name="performance-data"></a>Dati sulle prestazioni


Ecco i dati sulle prestazioni delle macchine virtuali VMware che l'appliance raccoglie e Invia ad Azure.

**Dati** | **Contatore** | **Impatto sulla valutazione**
--- | --- | ---
Uso della CPU | cpu.usage.average | Costo/dimensioni VM consigliate
Utilizzo della memoria | mem.usage.average | Costo/dimensioni VM consigliate
Velocità effettiva lettura disco (MB al secondo) | virtualDisk.read.average | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni della macchina virtuale
Velocità effettiva scritture disco (MB al secondo) | virtualDisk.write.average | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni della macchina virtuale
Operazioni di lettura disco al secondo | virtualDisk.numberReadAveraged.average | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni della macchina virtuale
Operazioni di scrittura su disco al secondo | virtualDisk.numberWriteAveraged.average  | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni della macchina virtuale
Velocità effettiva di lettura NIC (MB al secondo) | net.received.average | Calcolo delle dimensioni della macchina virtuale
Velocità effettiva Scritture NIC (MB al secondo) | net.transmitted.average  |Calcolo delle dimensioni della macchina virtuale

### <a name="app-dependencies-metadata"></a>Metadati delle dipendenze dell'app

L'analisi delle dipendenze senza agenti raccoglie i dati di connessione ed elaborazione.

#### <a name="connection-data"></a>Dati di connessione

Ecco i dati di connessione raccolti dal dispositivo da ogni macchina virtuale abilitata per l'analisi delle dipendenze senza agenti. Questi dati vengono inviati ad Azure.

**Dati** | **Comando utilizzato** 
--- | --- 
Porta locale | netstat
Indirizzo IP locale | netstat
Porta remota | netstat
Indirizzo IP remoto | netstat
Stato connessione TCP | netstat
ID di processo | netstat
No. di connessioni attive | netstat

#### <a name="process-data"></a>Elaborazione dei dati
Ecco i dati di processo raccolti dal dispositivo da ogni macchina virtuale abilitata per l'analisi delle dipendenze senza agenti. Questi dati vengono inviati ad Azure.

**Dati** | **classe WMI** | **Proprietà della classe WMI**
--- | --- | ---
Nome del processo | Win32_Process | ExecutablePath
Elaborare argomenti | Win32_Process | CommandLine
Nome applicazione | Win32_Process | Parametro VersionInfo. ProductName della proprietà ExecutablePath

#### <a name="linux-vm-data"></a>Dati VM Linux

Ecco i dati di connessione ed elaborazione raccolti dal dispositivo da ogni macchina virtuale Linux abilitata per l'analisi delle dipendenze senza agenti. Questi dati vengono inviati ad Azure.

**Dati** | **Comando utilizzato** 
--- | ---
Porta locale | netstat 
Indirizzo IP locale | netstat 
Porta remota | netstat 
Indirizzo IP remoto | netstat 
Stato connessione TCP | netstat 
No. di connessioni attive | netstat
ID di processo  | netstat 
Nome del processo | ps
Elaborare argomenti | ps
Nome applicazione | dpkg o RPM



## <a name="collected-data---hyper-v"></a>Dati raccolti-Hyper-V

L'appliance raccoglie i metadati, i dati sulle prestazioni e i dati di analisi delle dipendenze (se si usa l' [analisi delle dipendenze](concepts-dependency-visualization.md) senza agenti).

### <a name="metadata"></a>Metadati
I metadati individuati dal dispositivo Azure Migrate consentono di determinare se i computer e le app sono pronti per la migrazione ad Azure, le macchine virtuali e le app di dimensioni appropriate, i costi dei piani e l'analisi delle dipendenze dell'applicazione. Microsoft non usa questi dati in alcun controllo di conformità delle licenze.

Ecco l'elenco completo dei metadati della macchina virtuale Hyper-V che l'appliance raccoglie e Invia ad Azure.

**DATI* | **CLASSE WMI** | **PROPRIETÀ DELLA CLASSE WMI**
--- | --- | ---
**Dettagli computer** | 
Numero di serie del BIOS _ Msvm_BIOSElement | BIOSSerialNumber
Tipo di macchina virtuale (gen 1 o 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Nome visualizzato della macchina virtuale | Msvm_VirtualSystemSettingData | ElementName
Versione VM | Msvm_ProcessorSettingData | VirtualQuantity
Memoria (byte) | Msvm_MemorySettingData | VirtualQuantity
Memoria massima che può essere utilizzata dalla macchina virtuale | Msvm_MemorySettingData | Limite
Memoria dinamica abilitata | Msvm_MemorySettingData | DynamicMemoryEnabled
Nome/versione/FQDN del sistema operativo | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems i dati del nome
Stato di alimentazione della macchina virtuale | Msvm_ComputerSystem | EnabledState
**Dettagli per disco** | 
Identificatore disco | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Tipo di disco rigido virtuale | Msvm_VirtualHardDiskSettingData | Tipo
Dimensioni del disco rigido virtuale | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Padre del disco rigido virtuale | Msvm_VirtualHardDiskSettingData | ParentPath
**Dettagli per NIC** | 
Indirizzi IP (schede di rete sintetiche) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP abilitato (schede di rete sintetiche) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
ID NIC (schede di interfaccia di rete sintetiche) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Indirizzo MAC NIC (schede di interfaccia di rete sintetiche) | Msvm_SyntheticEthernetPortSettingData | Indirizzo
ID NIC (schede di interfaccia di rete legacy) | Dati MsvmEmulatedEthernetPortSetting | InstanceID
ID MAC NIC (schede di interfaccia di rete legacy) | Dati MsvmEmulatedEthernetPortSetting | Indirizzo

### <a name="performance-data"></a>Dati sulle prestazioni

Ecco i dati sulle prestazioni di Hyper VM che l'appliance raccoglie e Invia ad Azure.

**Classe del contatore delle prestazioni** | **Contatore** | **Impatto sulla valutazione**
--- | --- | ---
Processore virtuale hypervisor Hyper-V | % Tempo di esecuzione Guest | Costo/dimensioni VM consigliate
VM memoria dinamica Hyper-V | Pressione corrente (%)<br/> Memoria fisica visibile Guest (MB) | Costo/dimensioni VM consigliate
Dispositivo di archiviazione virtuale Hyper-V | Byte letti al secondo | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni della macchina virtuale
Dispositivo di archiviazione virtuale Hyper-V | Byte scritti al secondo | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni della macchina virtuale
Scheda di rete virtuale Hyper-V | Byte ricevuti al secondo | Calcolo delle dimensioni della macchina virtuale
Scheda di rete virtuale Hyper-V | Byte inviati al secondo | Calcolo delle dimensioni della macchina virtuale

- L'utilizzo della CPU è la somma di tutti i dati di utilizzo, per tutti i processori virtuali collegati a una macchina virtuale.
- L'utilizzo della memoria è (pressione corrente * memoria fisica visibile Guest)/100.
- I valori di utilizzo del disco e della rete vengono raccolti dai contatori delle prestazioni di Hyper-V elencati.

## <a name="appliance-upgrades"></a>Aggiornamenti Appliance

Il dispositivo viene aggiornato quando vengono aggiornati gli agenti Azure Migrate in esecuzione nell'appliance. Questa operazione viene eseguita automaticamente, perché l'aggiornamento automatico è abilitato per impostazione predefinita nell'appliance. È possibile modificare questa impostazione predefinita per aggiornare manualmente i servizi Appliance.

### <a name="turn-off-auto-update"></a>Disattiva aggiornamento automatico

1. Nel computer che esegue l'appliance, aprire l'editor del registro di sistema.
2. Passare a **HKEY_LOCAL_MACHINE \software\microsoft\azureappliance**.
3. Per disabilitare l'aggiornamento automatico, creare una chiave del registro di sistema **AutoUpdate** con valore DWORD pari a 0.

    ![Imposta chiave del registro di sistema](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Attiva aggiornamento automatico

È possibile attivare l'aggiornamento automatico usando uno dei metodi seguenti:

- Eliminando la chiave del registro di sistema AutoUpdate da HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\AzureAppliance.
- Al termine dell'individuazione, nel Configuration Manager Appliance.

Per eliminare la chiave del registro di sistema:

1. Nel computer che esegue l'appliance, aprire l'editor del registro di sistema.
2. Passare a **HKEY_LOCAL_MACHINE \software\microsoft\azureappliance**.
3. Eliminare la chiave del registro di sistema **AutoUpdate**creata in precedenza per disattivare l'aggiornamento automatico.

Per attivare il Configuration Manager Appliance, dopo il completamento dell'individuazione:

1. Nel computer dell'appliance aprire il Configuration Manager Appliance.
2. In **Appliance Services** > l'**aggiornamento automatico dei componenti Azure migrate è disattivato**, fare clic per abilitare l'aggiornamento automatico.

    ![Attiva aggiornamenti automatici](./media/migrate-appliance/turn-on.png)

### <a name="check-the-appliance-services-version"></a>Controllare la versione dei servizi Appliance

È possibile controllare la versione dei servizi Appliance utilizzando uno di questi metodi:

- In Appliance Configuration Manager, dopo il completamento dell'individuazione.
- Nel computer dell'appliance, nel **Pannello** > di controllo**programmi e funzionalità**.

Per archiviare il Configuration Manager dell'appliance:

1. Al termine dell'individuazione, aprire Appliance Configuration Manager (nell'app Web Appliance).
2. In **Servizi Appliance**verificare le versioni dei servizi Appliance.

    ![Controllare la versione](./media/migrate-appliance/version.png)

Per archiviare il pannello di controllo:

1. Nel dispositivo fare clic su **Start** > **Pannello** > **di controllo programmi e funzionalità**
2. Controllare le versioni dei servizi Appliance nell'elenco.

    ![Controllare la versione nel pannello di controllo](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Aggiornare manualmente una versione precedente

Se si esegue una versione precedente per uno dei componenti, è necessario disinstallare il servizio ed eseguire manualmente l'aggiornamento alla versione più recente.

1. Per verificare le versioni più recenti del servizio Appliance, [scaricare](https://aka.ms/latestapplianceservices) il file LatestComponents. JSON.
2.  Dopo il download, aprire il file LatestComponents. JSON nel blocco note.
3. Trovare la versione del servizio più recente nel file e il collegamento per il download. Ad esempio:

    "Name": "ASRMigrationWebApp", "DownloadLink": "https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi", "Version": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4.  Scaricare la versione più recente di un servizio obsoleto usando il collegamento per il download nel file.
5. Dopo il download, eseguire il comando seguente in una finestra di comando amministratore per verificare l'integrità del file MSI scaricato.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ```Ad esempio: C:\>certutil-HASHFILE C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.msi MD5

5. Verificare che l'output del comando corrisponda alla voce del valore hash per il servizio nel file (ad esempio, il valore hash MD5 precedente).
6. A questo punto, eseguire il file MSI per installare il servizio. Si tratta di un'installazione invisibile all'utente e la finestra di installazione si chiude dopo che è stata completata.
7. Al termine dell'installazione, controllare la versione del servizio nel **Pannello** > di controllo**programmi e funzionalità**. La versione del servizio dovrebbe ora essere aggiornata all'ultima versione visualizzata nel file JSON.



## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come](how-to-set-up-appliance-vmware.md) configurare l'appliance per VMware.
- [Informazioni su come](how-to-set-up-appliance-hyper-v.md) configurare l'appliance per Hyper-V.
- [Informazioni su come](how-to-set-up-appliance-physical.md) configurare l'appliance per i server fisici.

