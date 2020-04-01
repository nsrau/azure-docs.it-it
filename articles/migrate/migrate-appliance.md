---
title: Appliance di Azure Migrate
description: Fornisce una panoramica dell'appliance di Azure Migrate usata nella valutazione e nella migrazione del server.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: bccf4738d46b65f2d149eafc8e69591141d7d073
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437582"
---
# <a name="azure-migrate-appliance"></a>Appliance di Azure Migrate

Questo articolo riepiloga i prerequisiti e i requisiti di supporto per l'appliance Azure Migrate.This article summarizes the prerequisites and support requirements for the Azure Migrate appliance. 

## <a name="deployment-scenarios"></a>Scenari di distribuzione

L'appliance di Azure Migrate viene usata negli scenari seguenti.

**Scenario** | **Strumento** | **Utilizzo** 
--- | --- | ---
**Valutazione delle macchine virtuali VMware** | Migrazione di Azure:valutazione del serverAzure Migrate:Server Assessment | Scopri le macchine virtuali VMware<br/><br/> Individuazione delle app e delle dipendenze delle macchine<br/><br/> Raccogliere i metadati del computer e i metadati delle prestazioni per le valutazioni.
**Migrazione senza agenti di VM VMware** | Migrazione di Azure:migrazione serverAzure Migrate:Server Migration | Scopri le macchine virtuali VMware <br/><br/> Replicare le macchine virtuali VMware con la migrazione senza agente.
**Valutazione della macchina virtuale Hyper-V** | Migrazione di Azure:valutazione del serverAzure Migrate:Server Assessment | Individuazione delle macchine virtuali Hyper-V<br/><br/> Raccogliere i metadati del computer e i metadati delle prestazioni per le valutazioni.
**Valutazione fisica delle macchine** |  Migrazione di Azure:valutazione del serverAzure Migrate:Server Assessment |  Individuare i server fisici (o le macchine virtuali considerate come server fisici).<br/><br/> Raccogliere i metadati del computer e i metadati delle prestazioni per le valutazioni.

## <a name="appliance---vmware"></a>Appliance - VMware 

The following table summarizes the Azure Migrate appliance requirements for VMware.

**Requisito** | **Vmware** 
--- | ---
**Componenti dell'apparecchio** | L'apparecchio è dotato dei seguenti componenti:<br/><br/> - **App di gestione:** si tratta di un'app Web per l'input dell'utente durante la distribuzione dell'appliance. Utilizzato durante la valutazione dei computer per la migrazione ad Azure.Used when assessing machines for migration to Azure.<br/> - **Agente di individuazione:** l'agente raccoglie i dati di configurazione del computer. Utilizzato durante la valutazione dei computer per la migrazione ad Azure.Used when assessing machines for migration to Azure.<br/>- **Agente di valutazione:** l'agente raccoglie i dati sulle prestazioni. Utilizzato durante la valutazione dei computer per la migrazione ad Azure.Used when assessing machines for migration to Azure.<br/>- **Servizio di aggiornamento automatico**: Aggiorna i componenti dell'appliance (eseguiti ogni 24 ore).<br/>- **Agente DRA:** orchestra la replica delle macchine virtuali e coordina la comunicazione tra i computer replicati e Azure.DRA agent : Orchestrates VM replication, and coordinates communication between replicated machines and Azure. Utilizzato solo durante la replica di macchine virtuali VMware in Azure usando la migrazione senza agente.<br/>- **Gateway:** invia i dati replicati in Azure.Gateway : Sends replicated data to Azure. Utilizzato solo durante la replica di macchine virtuali VMware in Azure usando la migrazione senza agente.
**Distribuzione supportata** | Eseguire la distribuzione come VM VMware usando il modello OVA.<br/><br/> Eseguire la distribuzione come macchina virtuale o computer fisico VMware usando lo script di installazione di PowerShell.Deploy as a VMware VM or physical machine using PowerShell installation script.
**Sostegno al progetto** |  Un accessorio può essere associato a un singolo progetto. <br/> Un numero qualsiasi di appliance può essere associato a un singolo progetto.<br/> 
**Limiti di individuazione** | Un'appliance può individuare fino a 10.000 macchine virtuali VMware in un server vCenter.<br/> Un'appliance può connettersi a un singolo server vCenter.
**Modello OVA** | Scarica dal portale https://aka.ms/migrate/appliance/vmwareo da .<br/><br/> La dimensione del download è 11,2 GB.<br/><br/> Il modello di appliance scaricata viene fornito con una licenza di valutazione di Windows Server 2016, valida per 180 giorni. Se il periodo di valutazione è prossimo alla scadenza, è consigliabile scaricare e distribuire una nuova appliance oppure attivare la licenza del sistema operativo della macchina virtuale dell'appliance.
**Script di PowerShell** | Script [scaricare](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> 
**Software/hardware** |  L'appliance deve essere eseguita nel computer con Windows Server 2016, 32 GB di RAM, 8 vCPU, circa 80 GB di spazio di archiviazione su disco e un commutatore virtuale esterno.<br/> L'appliance richiede l'accesso a Internet, direttamente o tramite un proxy.<br/><br/> Se si esegue l'appliance in una macchina virtuale VMware, sono necessarie risorse sufficienti nel server vCenter per allocare una macchina virtuale che soddisfi i requisiti.<br/><br/> Se si esegue l'appliance in un computer fisico, assicurarsi che esegua Windows Server 2016 e soddisfi i requisiti hardware. 
**Requisiti di VMware** | Se si distribuisce l'appliance come macchina virtuale VMware, è necessario distribuirla in un host ESXi con versione 5.5 o successiva.<br/><br/> vCenter Server con 5.5, 6.0, 6.5 o 6.7.
**VDDK (migrazione senza agenti)** | Se si distribuisce l'appliance come macchina virtuale VMware e si esegue una migrazione senza agente, VMware vSphere VDDK deve essere installato nella macchina virtuale dell'appliance.
**Valore hash-OVA** | [Verificare](tutorial-assess-vmware.md#verify-security) i valori hash del modello OVA.
**Script valore hash-PowerShell** | [Verificare](deploy-appliance-script.md#verify-file-security) i valori dell'hash dello script di PowerShell.Verify the PowerShell script hash values.




## <a name="appliance---hyper-v"></a>Appliance - Hyper-V

**Requisito** | **Hyper-V** 
--- | ---
**Componenti dell'apparecchio** | L'apparecchio è dotato dei seguenti componenti:<br/><br/>- **App di gestione:** si tratta di un'app Web per l'input dell'utente durante la distribuzione dell'appliance. Utilizzato durante la valutazione dei computer per la migrazione ad Azure.Used when assessing machines for migration to Azure.<br/> - **Agente di individuazione:** l'agente raccoglie i dati di configurazione del computer. Utilizzato durante la valutazione dei computer per la migrazione ad Azure.Used when assessing machines for migration to Azure.<br/>- **Agente di valutazione:** l'agente raccoglie i dati sulle prestazioni. Utilizzato durante la valutazione dei computer per la migrazione ad Azure.Used when assessing machines for migration to Azure.<br/>- **Servizio di aggiornamento automatico**: Aggiorna i componenti dell'appliance (eseguiti ogni 24 ore).
**Distribuzione supportata** | Distribuire come macchina virtuale Hyper-V usando un modello VHD.<br/><br/> Eseguire la distribuzione come macchina virtuale Hyper-V o computer fisico usando uno script di installazione di PowerShell.Deploy as a Hyper-V VM or physical machine using a PowerShell installation script.
**Sostegno al progetto** |  Un accessorio può essere associato a un singolo progetto. <br/> Un numero qualsiasi di appliance può essere associato a un singolo progetto.<br/> 
**Limiti di individuazione** | Un'appliance può individuare fino a 5000 macchine virtuali Hyper-V.<br/> Un'appliance può connettersi a un massimo di 300 host Hyper-V.
**Modello VHD** | Cartella compressa, incluso il disco rigido virtuale. Scarica dal portale https://aka.ms/migrate/appliance/hypervo da .<br/><br/> La dimensione del download è 10 GB.<br/><br/> Il modello di appliance scaricata viene fornito con una licenza di valutazione di Windows Server 2016, valida per 180 giorni. Se il periodo di valutazione è prossimo alla scadenza, è consigliabile scaricare e distribuire una nuova appliance oppure attivare la licenza del sistema operativo della macchina virtuale dell'appliance.
**Script di PowerShell** | Script [scaricare](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> 
**Software/hardware***   |  L'appliance deve essere eseguita nel computer con Windows Server 2016, 32 GB di RAM, 8 vCPU, circa 80 GB di spazio di archiviazione su disco e un commutatore virtuale esterno.<br/> L'appliance necessita di un indirizzo IP statico o dinamico e richiede l'accesso a Internet, direttamente o tramite un proxy.<br/><br/> Se si esegue l'appliance come macchina virtuale Hyper-V, sono necessarie risorse sufficienti nell'host Hyper-V per allocare 16 GB di RAM, 8 vCPU, circa 80 GB di spazio di archiviazione e uno switch esterno per la macchina virtuale dell'appliance.<br/><br/> Se si esegue l'appliance in un computer fisico, assicurarsi che esegua Windows Server 2016 e soddisfi i requisiti hardware. 
**Requisiti di Hyper-V** | Se si distribuisce l'appliance con il modello VHD, la macchina virtuale dell'appliance fornita da Azure Migrate è La versione 5.0 della macchina virtuale Hyper-V.If you deploy the appliance with the VHD template, the appliance VM provided by Azure Migrate is Hyper-V VM version 5.0.<br/><br/> L'host Hyper-V deve eseguire Windows Server 2012 R2 o versione successiva. 
**Valore hash-VHDHash value-VHD** | [Verificare](tutorial-assess-hyper-v.md#verify-security) i valori hash del modello VHD.
**Script valore hash-PowerShell** | [Verificare](deploy-appliance-script.md#verify-file-security) i valori dell'hash dello script di PowerShell.Verify the PowerShell script hash values.


## <a name="appliance---physical"></a>Elettrodomestico - Fisico

**Requisito** | **Fisico** 
--- | ---
**Componenti dell'apparecchio** | L'apparecchio è dotato dei seguenti componenti: <br/><br/> - **App di gestione:** si tratta di un'app Web per l'input dell'utente durante la distribuzione dell'appliance. Utilizzato durante la valutazione dei computer per la migrazione ad Azure.Used when assessing machines for migration to Azure.<br/> - **Agente di individuazione:** l'agente raccoglie i dati di configurazione del computer. Utilizzato durante la valutazione dei computer per la migrazione ad Azure.Used when assessing machines for migration to Azure.<br/>- **Agente di valutazione:** l'agente raccoglie i dati sulle prestazioni. Utilizzato durante la valutazione dei computer per la migrazione ad Azure.Used when assessing machines for migration to Azure.<br/>- **Servizio di aggiornamento automatico**: Aggiorna i componenti dell'appliance (eseguiti ogni 24 ore).
**Distribuzione supportata** | Eseguire la distribuzione come computer fisico dedicato o come macchina virtuale usando uno script di installazione di PowerShell.Deploy as a dedicated physical machine, or a VM, using a PowerShell installation script.
**Sostegno al progetto** |  Un accessorio può essere associato a un singolo progetto. <br/> Un numero qualsiasi di appliance può essere associato a un singolo progetto.<br/> 
**Limiti di individuazione** | Un apparecchio può scoprire fino a 250 server fisici.
**Script di PowerShell** | Scaricare lo script (AzureMigrateInstaller.ps1) in una cartella compressa dal portale. [Altre informazioni](tutorial-assess-physical.md#set-up-the-appliance) In alternativa, [scaricare direttamente](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> La dimensione del download è 59,7 MB.
**Software/hardware** |  L'appliance deve essere eseguita nel computer con Windows Server 2016, 32 GB di RAM, 8 vCPU, circa 80 GB di spazio di archiviazione su disco e un commutatore virtuale esterno.<br/> L'appliance necessita di un indirizzo IP statico o dinamico e richiede l'accesso a Internet, direttamente o tramite un proxy.<br/><br/> Se si esegue l'appliance in un computer fisico, assicurarsi che esegua Windows Server 2016 e soddisfi i requisiti hardware. 
**Valore hash** | [Verificare](deploy-appliance-script.md#verify-file-security) i valori dell'hash dello script di PowerShell.Verify the PowerShell script hash values.

## <a name="url-access"></a>accesso con URL

L'appliance di Azure Migrate richiede connettività a Internet.The Azure Migrate appliance needs connectivity to the internet.

- Quando si distribuisce l'appliance, Azure Migrate esegue un controllo di connettività agli URL riepilogati nella tabella seguente.
- Se si utilizza un proxy basato su URL per connettersi a Internet, è necessario consentire l'accesso a questi URL, assicurandosi che il proxy risolva tutti i record CNAME ricevuti durante la ricerca degli URL.

**URL** | **Dettagli**  
--- | --- |
*.portal.azure.com  | Passare al portale di Azure.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Accedere alla sottoscrizione di Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Creare app di Azure Active Directory (AD) per l'appliance per comunicare con Azure Migrate.Create Azure Active Directory (AD) apps for the appliance to communicate with Azure Migrate.
management.azure.com | Creare app di Azure AD per l'appliance per comunicare con il servizio Azure Migrate.Create Azure AD apps for the appliance to communicate with the Azure Migrate service.
dc.services.visualstudio.com | Caricare i log dell'app usati per il monitoraggio interno.
*.vault.azure.net | Gestire i segreti nell'insieme di credenziali delle chiavi di Azure.Manage secrets in the Azure Key Vault.
aka.ms/ ) | Consentire l'accesso ai collegamenti alias. Utilizzato per gli aggiornamenti dell'appliance di Azure Migrate.Used for Azure Migrate appliance updates.
download.microsoft.com/download | Consenti download dal download Microsoft.
*.servicebus.windows.net | Comunicazione tra l'appliance e il servizio Azure Migrate.Communication between the appliance and the Azure Migrate service.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Connettersi agli URL del servizio Azure Migrate.Connect to Azure Migrate service URLs.
*.hypervrecoverymanager.windowsazure.com | **Utilizzato per la migrazione senza agente VMware**<br/><br/> Connettersi agli URL del servizio Azure Migrate.Connect to Azure Migrate service URLs.
*.blob.core.windows.net |  **Utilizzato per la migrazione senza agente VMware**<br/><br/>Caricare i dati nell'archivio per la migrazione.




## <a name="collected-data---vmware"></a>Dati raccolti - VMware

L'appliance raccoglie metadati, dati sulle prestazioni e dati di analisi delle dipendenze (se viene utilizzata [l'analisi](concepts-dependency-visualization.md) delle dipendenze senza agente).

### <a name="metadata"></a>Metadati

I metadati individuati dall'appliance Azure Migrate consentono di determinare se i computer e le app sono pronti per la migrazione ad Azure, i computer e le app di dimensioni corrette, i costi dei piani e l'analisi delle dipendenze delle applicazioni. Microsoft non usa questi dati in alcun controllo di conformità delle licenze.

Ecco l'elenco completo dei metadati delle macchine virtuali VMware raccolti e inviati dall'appliance ad Azure.Here's the full list of VMware VM metadata that the appliance collects and sends to Azure.

**Dati** | **Contatore**
--- | --- 
**Dettagli macchina** | 
ID macchina virtuale | vm.Config.InstanceUuid 
Nome della VM. | vm.Config.Name
ID server vCenter | VMwareClient.Instance.Uuid
Descrizione macchina virtuale | vm.Summary.Config.Annotation
Nome prodotto con licenza | vm.Client.ServiceContent.About.LicenseProductName
Tipo di sistema operativo | Vm. SummaryConfig.GuestFullName
Tipo di avvio | vm.Config.Firmware
Numero di core | vm.Config.Hardware.NumCPU
Memoria (MB) | vm.Config.Hardware.MemoryMB
Numero di dischi | Vm. Config.Hardware.Device.ToList(). FindAll(x e> è VirtualDisk).count
Elenco dimensioni disco | Vm. Config.Hardware.Device.ToList(). FindAll(x> è VirtualDisk)
Elenco schede di rete | Vm. Config.Hardware.Device.ToList(). FindAll(x x> è VirtualEthernet).count
Uso della CPU | cpu.usage.average
Utilizzo della memoria |mem.usage.average
**Dettagli per disco** | 
Valore chiave disco | disk.Key
Numero di unità | disk.UnitNumber
Valore chiave controller del disco | disk.ControllerKey.Value
Gigabyte con provisioning | virtualDisk.DeviceInfo.Summary
Nome del disco | Valore generato utilizzando il disco. UnitNumber, disco. Chiave, disco. ControllerKey.VAlue
Operazioni di lettura per secondo | virtualDisk.numberReadAveraged.average
Operazioni di scrittura per secondo | virtualDisk.numberWriteAveraged.average
Velocità effettiva di lettura (MB al secondo)Read throughput (MB per second) | virtualDisk.read.average
Velocità effettiva di scrittura (MB al secondo)Write throughput (MB per second) | virtualDisk.write.average
**Dettagli per scheda di rete** | 
Nome scheda di rete | nic.Key
Indirizzo MAC | ((VirtualEthernetCard)nic).MacAddress
Indirizzi IPv4 | vm.Guest.Net
Indirizzi IPv6 | vm.Guest.Net
Velocità effettiva di lettura (MB al secondo)Read throughput (MB per second) | net.received.average
Velocità effettiva di scrittura (MB al secondo)Write throughput (MB per second) | net.transmitted.average
**Dettagli percorso inventario** | 
Nome | container.GetType().Name
Tipo di oggetto figlio | container.ChildType
Informazioni di riferimento | container.MoRef
Dettagli elemento padre | Container.Parent
Dettagli cartella per macchina virtuale | ((Folder)container).ChildEntity.Type
Dettagli del data center per macchina virtualeDatacenter details per VM | ((Datacenter)container).VmFolder
Dettagli del data center per cartella host | ((Datacenter)container).HostFolder
Dettagli cluster per host | ((ClusterComputeResource)contenitore). Host
Host details per VM | ((HostSystem)contenitore). Vm

### <a name="performance-data"></a>Dati sulle prestazioni


Ecco i dati sulle prestazioni della macchina virtuale VMware raccolti e inviati dall'appliance ad Azure.Here's the VMware VM performance data that the appliance collects and sends to Azure.

**Dati** | **Contatore** | **Impatto sulla valutazione**
--- | --- | ---
Uso della CPU | cpu.usage.average | Dimensioni/costo consigliati per la macchina virtuale
Utilizzo della memoria | mem.usage.average | Dimensioni/costo consigliati per la macchina virtuale
Velocità effettiva lettura disco (MB al secondo) | virtualDisk.read.average | Calcolo delle dimensioni del disco, del costo di archiviazione, delle dimensioni della macchina virtuale
Velocità effettiva scritture disco (MB al secondo)Disk writes throughput (MB per second) | virtualDisk.write.average | Calcolo delle dimensioni del disco, del costo di archiviazione, delle dimensioni della macchina virtuale
Operazioni di lettura su disco al secondo | virtualDisk.numberReadAveraged.average | Calcolo delle dimensioni del disco, del costo di archiviazione, delle dimensioni della macchina virtuale
Operazioni di scrittura su disco al secondoDisk writes operations per second | virtualDisk.numberWriteAveraged.average  | Calcolo delle dimensioni del disco, del costo di archiviazione, delle dimensioni della macchina virtuale
Velocità effettiva lettura scheda di rete (MB al secondo)NIC read throughput (MB per second) | net.received.average | Calcolo delle dimensioni della macchina virtuale
Velocità effettiva di scrittura DELLA scheda di rete (MB al secondo)NIC writes throughput (MB per second) | net.transmitted.average  |Calcolo delle dimensioni della macchina virtuale

### <a name="app-dependencies-metadata"></a>Metadati delle dipendenze delle app

L'analisi delle dipendenze senza agente raccoglie i dati di connessione ed elaborazione.

#### <a name="connection-data"></a>Dati di connessione

Ecco i dati di connessione raccolti dall'appliance da ogni macchina virtuale abilitata per l'analisi delle dipendenze senza agente. Questi dati vengono inviati ad Azure.This data is sent to Azure.

**Dati** | **Comando utilizzato** 
--- | --- 
Porta locale | netstat
Indirizzo IP locale | netstat
Porta remota | netstat
Indirizzo IP remoto | netstat
Stato della connessione TCP | netstat
ID di processo | netstat
No. di connessioni attive | netstat

#### <a name="process-data"></a>Elaborazione dei dati
Ecco i dati di processo raccolti dall'appliance da ogni macchina virtuale abilitata per l'analisi delle dipendenze senza agente. Questi dati vengono inviati ad Azure.This data is sent to Azure.

**Dati** | **classe WMI** | **Proprietà della classe WMI**
--- | --- | ---
Nome del processo | Win32_Process | Percorsoeseguibile
Elaborare argomenti | Win32_Process | CommandLine
Nome applicazione | Win32_Process | Parametro VersionInfo.ProductName della proprietà ExecutablePath

#### <a name="linux-vm-data"></a>Dati di macchine virtuali Linux

Ecco i dati di connessione ed elaborazione raccolti dall'appliance da ogni macchina virtuale Linux abilitata per l'analisi delle dipendenze senza agente. Questi dati vengono inviati ad Azure.This data is sent to Azure.

**Dati** | **Comando utilizzato** 
--- | ---
Porta locale | netstat 
Indirizzo IP locale | netstat 
Porta remota | netstat 
Indirizzo IP remoto | netstat 
Stato della connessione TCP | netstat 
No. di connessioni attive | netstat
ID di processo  | netstat 
Nome del processo | ps
Elaborare argomenti | ps
Nome applicazione | dpkg o rpm



## <a name="collected-data---hyper-v"></a>Dati raccolti - Hyper-V

L'appliance raccoglie metadati, dati sulle prestazioni e dati di analisi delle dipendenze (se viene utilizzata [l'analisi](concepts-dependency-visualization.md) delle dipendenze senza agente).

### <a name="metadata"></a>Metadati
I metadati individuati dall'appliance Azure Migrate consentono di determinare se i computer e le app sono pronti per la migrazione ad Azure, i computer e le app di dimensioni corrette, i costi dei piani e l'analisi delle dipendenze delle applicazioni. Microsoft non usa questi dati in alcun controllo di conformità delle licenze.

Ecco l'elenco completo dei metadati della macchina virtuale Hyper-V raccolti e inviati dall'appliance ad Azure.Here's the full list of Hyper-V VM metadata that the appliance collects and sends to Azure.

**Dati* | **CLASSE WMI** | **WMI CLASS (PROPRIETÀ)**
--- | --- | ---
**Dettagli macchina** | 
Numero di serie del BIOS _ Msvm_BIOSElement | BIOSSerialNumber (NumeroBIO)
Tipo di VM (Gen 1 o 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType (Sottotipo VirtualSystemSubType)
Nome visualizzato della macchina virtuale | Msvm_VirtualSystemSettingData | ElementName
Versione VM | Msvm_ProcessorSettingData | VirtualQuantity (Quantità Virtuale)
Memoria (byte) | Msvm_MemorySettingData | VirtualQuantity (Quantità Virtuale)
Memoria massima che può essere utilizzata dalla macchina virtualeMaximum memory that can be consumed by VM | Msvm_MemorySettingData | Limite
Memoria dinamica abilitata | Msvm_MemorySettingData | DynamicMemoryEnabled
Nome/versione/FQDN del sistema operativo | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems Nome Dati
Stato di alimentazione della macchina virtuale | Msvm_ComputerSystem | EnabledState
**Dettagli per disco** | 
Identificatore disco | Msvm_VirtualHardDiskSettingData | IdDisco virtuale
Tipo di disco rigido virtuale | Msvm_VirtualHardDiskSettingData | Type
Dimensioni del disco rigido virtuale | Msvm_VirtualHardDiskSettingData | MaxInternalSize (Dimensioni interne)
Padre del disco rigido virtuale | Msvm_VirtualHardDiskSettingData | ParentPath
**Dettagli per scheda di rete** | 
Indirizzi IP (NIC sintetiche) | Msvm_GuestNetworkAdapterConfiguration | Ipaddresses
DHCP abilitato (NIC sintetiche) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
ID scheda di interfaccia di rete (NIC sintetiche) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Indirizzo MAC NIC (NIC sintetiche) | Msvm_SyntheticEthernetPortSettingData | Indirizzo
ID scheda di interfaccia di rete (NIC legacy) | MsvmEmulatedEthernetPortSetting Dati | InstanceID
ID MAC NIC (NIC legacy) | MsvmEmulatedEthernetPortSetting Dati | Indirizzo

### <a name="performance-data"></a>Dati sulle prestazioni

Ecco i dati sulle prestazioni di Hyper VM raccolti e inviati dall'appliance ad Azure.Here's the Hyper VM performance data that the appliance collects and sends to Azure.

**Classe del contatore delle prestazioni** | **Contatore** | **Impatto sulla valutazione**
--- | --- | ---
Processore virtuale hypervisor Hyper-V | % tempo di esecuzione guest | Dimensioni/costo consigliati per la macchina virtuale
VM di memoria dinamica Hyper-V | Pressione corrente (%)<br/> Memoria fisica visibile guest (MB) | Dimensioni/costo consigliati per la macchina virtuale
Dispositivo di archiviazione virtuale Hyper-V | Byte letti al secondo | Calcolo delle dimensioni del disco, del costo di archiviazione, delle dimensioni della macchina virtuale
Dispositivo di archiviazione virtuale Hyper-V | Byte scritti al secondo | Calcolo delle dimensioni del disco, del costo di archiviazione, delle dimensioni della macchina virtuale
Scheda di rete virtuale Hyper-V | Byte ricevuti/secondo | Calcolo delle dimensioni della macchina virtuale
Scheda di rete virtuale Hyper-V | Byte inviati al secondo | Calcolo delle dimensioni della macchina virtuale

- L'utilizzo della CPU è la somma di tutti gli utilizzi, per tutti i processori virtuali collegati a una macchina virtuale.
- L'utilizzo della memoria è (Pressione corrente - Memoria fisica visibile guest) / 100.
- I valori di utilizzo del disco e della rete vengono raccolti dai contatori delle prestazioni di Hyper-V elencati.

## <a name="appliance-upgrades"></a>Aggiornamenti dell'appliance

L'appliance viene aggiornata man mano che gli agenti di Azure Migrate in esecuzione nell'appliance vengono aggiornati. Ciò avviene automaticamente perché l'aggiornamento automatico è abilitato sull'appliance per impostazione predefinita. È possibile modificare questa impostazione predefinita per aggiornare manualmente gli agenti.

- **Disattivare l'aggiornamento automatico**: disattivare l'aggiornamento automatico nel Registro di sistema impostando HKEY_LOCAL_MACHINE chiave "AutoUpdate" SOFTWARE Microsoft AzureAppliance su 0 (DWORD). Se si decide di utilizzare gli aggiornamenti manuali, è importante aggiornare tutti gli agenti nell'appliance contemporaneamente, utilizzando il pulsante **Aggiorna** per ogni agente obsoleto nell'appliance.
- **Aggiorna manualmente:** per gli aggiornamenti manuali, assicurarsi di aggiornare tutti gli agenti nell'appliance utilizzando il pulsante **Aggiorna** per ogni agente obsoleto nell'appliance. È possibile impostare nuovamente l'impostazione di aggiornamento su Aggiornamenti automatici in qualsiasi momento.

![Aggiorna automaticamente l'apparecchio](./media/migrate-appliance/autoupdate.png)

## <a name="next-steps"></a>Passaggi successivi

- [Scopri come](how-to-set-up-appliance-vmware.md) configurare l'appliance per VMware.
- [Informazioni su come](how-to-set-up-appliance-hyper-v.md) configurare l'appliance per Hyper-V.
- [Informazioni su come](how-to-set-up-appliance-physical.md) configurare l'appliance per i server fisici.

