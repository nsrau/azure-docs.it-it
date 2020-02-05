---
title: Appliance Azure Migrate
description: Viene fornita una panoramica dell'appliance Azure Migrate utilizzata per la valutazione e la migrazione dei server.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 6311f24a9c977b5f8b34384f0754f041a0c57ce7
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990743"
---
# <a name="azure-migrate-appliance"></a>Appliance Azure Migrate

Questo articolo descrive l'appliance Azure Migrate. L'Appliance viene distribuita quando si usa [Azure migrate strumento Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) per individuare e valutare app, infrastruttura e carichi di lavoro per la migrazione ai Microsoft Azure. Il dispositivo viene usato anche quando si esegue la migrazione di macchine virtuali VMware in Azure usando [Azure migrate: valutazione del server](migrate-services-overview.md#azure-migrate-server-migration-tool) con [migrazione senza agenti](server-migrate-overview.md).

## <a name="appliance-overview"></a>Panoramica dell'appliance

Il dispositivo Azure Migrate viene usato negli scenari seguenti.

**Scenario** | **Strumento** | **Usata per** 
--- | --- | ---
Macchina virtuale VMware | Azure Migrate: valutazione del server<br/><br/> Azure Migrate: migrazione del server | Individuare le macchine virtuali VMware<br/><br/> Individuare le app e le dipendenze del computer<br/><br/> Raccolta dei metadati del computer e delle prestazioni per le valutazioni.<br/><br/> Eseguire la replica di macchine virtuali VMware con migrazione senza agenti.
Macchina virtuale Hyper-V | Azure Migrate: valutazione del server | Individuare le VM Hyper-V<br/><br/> Raccolta dei metadati del computer e delle prestazioni per le valutazioni.
Computer fisico |  Azure Migrate: valutazione del server |  Individuare i server fisici<br/><br/> Raccolta dei metadati del computer e delle prestazioni per le valutazioni.

## <a name="appliance---vmware"></a>Appliance-VMware 

**Requisito** | **VMware** 
--- | ---
**Formato di download** | . OVA 
**Collegamento di download** | https://aka.ms/migrate/appliance/vmware 
**Dimensioni del download** | 11,2 GB
**License** | Il modello di appliance scaricato è disponibile con una licenza di valutazione di Windows Server 2016, valida per 180 giorni. Se il periodo di valutazione è prossimo alla scadenza, è consigliabile scaricare e distribuire una nuova appliance oppure attivare la licenza del sistema operativo della VM del dispositivo.
**Distribuzione** | Si distribuisce l'appliance come macchina virtuale VMware. Per allocare una macchina virtuale con 32 GB di RAM, 8 vCPU, circa 80 GB di spazio di archiviazione su disco e un compartitore virtuale esterno, è necessario disporre di risorse sufficienti nel server vCenter.<br/> Il dispositivo richiede l'accesso a Internet, direttamente o tramite un proxy.<br/> Il dispositivo può connettersi a una singola server vCenter.
**Hardware** | Risorse su vCenter per allocare una macchina virtuale con 32 GB di RAM 8 vCPU, circa 80 GB di spazio di archiviazione su disco e un Commuter virtuale esterno. 
**Valore hash** | MD5: c06ac2a2c0f870d3b274a0b7a73b78b1<br/><br/> SHA256:4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c
**server vCenter/host** | La macchina virtuale dell'appliance deve essere distribuita in un host ESXi che esegue la versione 5,5 o successiva.<br/><br/> server vCenter che esegue 5,5, 6,0, 6,5 o 6,7.
**Progetto Azure Migrate** | Un appliance può essere associato a un singolo progetto. <br/> Un numero qualsiasi di Appliance può essere associato a un singolo progetto.<br/> 
**Individuazione** | Un dispositivo può individuare fino a 10.000 VM VMware in una server vCenter.<br/> Un appliance può connettersi a una singola server vCenter.
**Componenti del dispositivo** | App di gestione: app Web in appliance per l'input dell'utente durante la distribuzione.<br/> Agente di individuazione: raccoglie i dati di configurazione del computer.<br/> Agente di valutazione: raccolta dei dati sulle prestazioni.<br/> DRA: Orchestra la replica della macchina virtuale e coordina la comunicazione tra computer/Azure.<br/> Gateway: Invia i dati replicati in Azure.<br/> Aggiornamento automatico del servizio: Aggiorna componenti (eseguito ogni 24 ore).
**VDDK (migrazione senza agenti)** | Se si esegue una migrazione senza agente con Azure Migrate migrazione del server, è necessario installare il VMware vSphere VDDK nella macchina virtuale del dispositivo.


## <a name="appliance---hyper-v"></a>Appliance-Hyper-V

**Requisito** | **Hyper-V** 
--- | ---
**Formato di download** | Cartella compressa (con VHD)
**Collegamento di download** | https://aka.ms/migrate/appliance/hyperv 
**Dimensioni del download** | 10 GB
**License** | Il modello di appliance scaricato è disponibile con una licenza di valutazione di Windows Server 2016, valida per 180 giorni. Se il periodo di valutazione è prossimo alla scadenza, è consigliabile scaricare e distribuire una nuova appliance oppure attivare la licenza del sistema operativo della VM del dispositivo.
**Distribuzione dell'appliance**   |  Si distribuisce l'appliance come macchina virtuale Hyper-V.<br/> La macchina virtuale dell'appliance fornita da Azure Migrate è la macchina virtuale Hyper-V 5,0.<br/> L'host Hyper-V deve eseguire Windows Server 2012 R2 o versione successiva.<br/> L'host necessita di spazio sufficiente per allocare 16 GB di RAM, 8 vCPU, circa 80 GB di spazio di archiviazione e un commute esterno per la macchina virtuale dell'appliance.<br/> Per l'appliance sono necessari un indirizzo IP statico o dinamico e l'accesso a Internet.
**Hardware** | Risorse nell'host Hyper-V per allocare 16 GB di RAM, 8 vCPU, circa 80 GB di spazio di archiviazione e un commute esterno per la macchina virtuale dell'appliance.
**Valore hash** | MD5:29a7531f32bcf69f32d964fa5ae950bc<br/><br/> SHA256:37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31
**Host Hyper-V** | Esecuzione di Windows Server 2012 R2 o versione successiva.
**Progetto Azure Migrate** | Un appliance può essere associato a un singolo progetto. <br/> Un numero qualsiasi di Appliance può essere associato a un singolo progetto.<br/> 
**Individuazione** | Un dispositivo può individuare fino a 5000 VM VMware in una server vCenter.<br/> Un appliance può connettersi a un massimo di 300 host Hyper-V.
**Componenti del dispositivo** | App di gestione: app Web in appliance per l'input dell'utente durante la distribuzione.<br/> Agente di individuazione: raccoglie i dati di configurazione del computer.<br/> Agente di valutazione: raccolta dei dati sulle prestazioni.<br/>  Aggiornamento automatico del servizio: Aggiorna componenti (eseguito ogni 24 ore).


## <a name="appliance---physical"></a>Appliance-fisico

**Requisito** | **Fisico** 
--- | ---
**Formato di download** | Cartella compressa (con script del programma di installazione di PowerShell)
**Collegamento di download** | [Collegamento di download](https://go.microsoft.com/fwlink/?linkid=2105112)
**Dimensioni del download** | 59,7 MB
**Hardware** | Computer fisico dedicato o macchina virtuale. Il computer che esegue l'appliance necessita di 16 GB di RAM, 8 vCPU, circa 80 GB di spazio di archiviazione e un commute esterno.<br/> Per l'appliance sono necessari un indirizzo IP statico o dinamico e l'accesso a Internet.
**Valore hash** | MD5:1e92ede3e87c03bd148e56a708cdd33f<br/><br/> SHA256: a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c
**Software** | Il computer appliance deve eseguire Windows Server 2016. 
**Distribuzione dell'appliance**   |  Lo script del programma di installazione dell'Appliance viene scaricato dal portale (in una cartella compressa). <br/> È possibile decomprimere la cartella ed eseguire lo script di PowerShell (AzureMigrateInstaller. ps1).
**Individuazione** | Un'appliance può individuare fino a 250 di server fisici.
**Componenti del dispositivo** | App di gestione: app Web in appliance per l'input dell'utente durante la distribuzione.<br/> Agente di individuazione: raccoglie i dati di configurazione del computer.<br/> Agente di valutazione: raccolta dei dati sulle prestazioni.<br/>  Aggiornamento automatico del servizio: Aggiorna componenti (eseguito ogni 24 ore).


## <a name="url-access"></a>accesso con URL

Il dispositivo Azure Migrate richiede la connettività a Internet.

- Quando si distribuisce il dispositivo, Azure Migrate esegue un controllo della connettività agli URL riepilogati nella tabella seguente.
- Se si usa un proxy basato su URL per connettersi a Internet, consentire l'accesso a questi URL, assicurandosi che il proxy risolva tutti i record CNAME ricevuti durante la ricerca degli URL.

**URL** | **Dettagli**  
--- | --- |
*.portal.azure.com  | Passare al portale di Azure.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com | Accedere alla sottoscrizione di Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Creare app Active Directory per l'appliance per comunicare con Azure Migrate.
management.azure.com | Creare app Active Directory per l'appliance per comunicare con il servizio Azure Migrate.
dc.services.visualstudio.com | Caricare i log delle app usati per il monitoraggio interno.
*.vault.azure.net | Gestisci i segreti nel Azure Key Vault.
aka.ms/* | Consente l'accesso a collegamenti aka. Usato per gli aggiornamenti di Azure Migrate Appliance.
download.microsoft.com/download | Consenti i download dal Download Microsoft.
*.servicebus.windows.net | **Usato per la migrazione senza agenti VMware**<br/><br/> Comunicazione tra l'appliance e il servizio Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | **Usato per la migrazione senza agenti VMware**<br/><br/> Connettersi agli URL del servizio Azure Migrate.
*.blob.core.windows.net |  **Usato per la migrazione senza agenti VMware**<br/><br/>Caricare i dati nella risorsa di archiviazione.




## <a name="collected-data---vmware"></a>Dati raccolti-VMware

### <a name="collected-performance-data-vmware"></a>Dati sulle prestazioni raccolti-VMware

Ecco i dati sulle prestazioni delle macchine virtuali VMware che l'appliance raccoglie e Invia ad Azure.

**Dati** | **Contatore** | **Impatto valutazione**
--- | --- | ---
Uso della CPU | cpu.usage.average | Costo/dimensioni VM consigliate
Utilizzo della memoria | mem.usage.average | Costo/dimensioni VM consigliate
Velocità effettiva lettura disco (MB al secondo) | virtualDisk.read.average | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni della macchina virtuale
Velocità effettiva scritture disco (MB al secondo) | virtualDisk.write.average | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni della macchina virtuale
Operazioni di lettura disco al secondo | virtualDisk.numberReadAveraged.average | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni della macchina virtuale
Operazioni di scrittura su disco al secondo | virtualDisk.numberWriteAveraged.average  | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni della macchina virtuale
Velocità effettiva di lettura NIC (MB al secondo) | net.received.average | Calcolo delle dimensioni della macchina virtuale
Velocità effettiva Scritture NIC (MB al secondo) | net.transmitted.average  |Calcolo delle dimensioni della macchina virtuale


### <a name="collected-metadata-vmware"></a>Metadati raccolti-VMware

> [!NOTE]
> I metadati individuati dal dispositivo Azure Migrate vengono usati per semplificare le operazioni di ridimensionamento delle applicazioni durante la migrazione in Azure, l'analisi dell'idoneità di Azure, l'analisi delle dipendenze delle applicazioni e la pianificazione dei costi. Microsoft non utilizza questi dati in relazione a qualsiasi controllo di conformità delle licenze.

Di seguito è riportato l'elenco completo dei metadati delle macchine virtuali VMware che l'appliance raccoglie e Invia ad Azure.

**Dati** | **Contatore**
--- | --- 
**Dettagli computer** | 
ID macchina virtuale | vm.Config.InstanceUuid 
Nome della VM. | vm.Config.Name
ID server vCenter | VMwareClient.Instance.Uuid
Descrizione macchina virtuale | vm.Summary.Config.Annotation
Nome prodotto con licenza | vm.Client.ServiceContent.About.LicenseProductName
Tipo di sistema operativo | vm.SummaryConfig.GuestFullName
Tipo di avvio | vm.Config.Firmware
Numero di core | vm.Config.Hardware.NumCPU
Memoria (MB) | vm.Config.Hardware.MemoryMB
Numero di dischi | VM. Config. hardware. Device. ToList (). FindAll (x = > è VirtualDisk). Count
Elenco dimensioni disco | VM. Config. hardware. Device. ToList (). FindAll (x = > è VirtualDisk)
Elenco schede di rete | VM. Config. hardware. Device. ToList (). FindAll (x = > è VirtualEthernet). Count
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
Nome | container.GetType().Name
Tipo di oggetto figlio | container.ChildType
Informazioni di riferimento | container.MoRef
Dettagli elemento padre | Container.Parent
Dettagli cartella per macchina virtuale | ((Folder)container).ChildEntity.Type
Dettagli Data Center per macchina virtuale | ((Datacenter)container).VmFolder
Dettagli Data Center per cartella host | ((Datacenter)container).HostFolder
Dettagli cluster per host | ((ClusterComputeResource)container).Host
Dettagli host per macchina virtuale | ((HostSystem)container).VM

## <a name="collected-data---hyper-v"></a>Dati raccolti-Hyper-V

### <a name="collected-performance-data-hyper-v"></a>Dati sulle prestazioni raccolti-Hyper-V

> [!NOTE]
> I metadati individuati dal dispositivo Azure Migrate vengono usati per semplificare le operazioni di ridimensionamento delle applicazioni durante la migrazione in Azure, l'analisi dell'idoneità di Azure, l'analisi delle dipendenze delle applicazioni e la pianificazione dei costi. Microsoft non utilizza questi dati in relazione a qualsiasi controllo di conformità delle licenze.

Ecco i dati sulle prestazioni di Hyper VM che l'appliance raccoglie e Invia ad Azure.

**Classe del contatore delle prestazioni** | **Contatore** | **Impatto valutazione**
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

### <a name="collected-metadata-hyper-v"></a>Metadati raccolti-Hyper-V

Ecco l'elenco completo dei metadati della macchina virtuale Hyper-V che l'appliance raccoglie e Invia ad Azure.

**Dati** | **Classe WMI** | **Proprietà della classe WMI**
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




## <a name="discovery-and-collection-process"></a>Processo di individuazione e raccolta

L'appliance comunica con i server vCenter e gli host/cluster Hyper-V usando il processo seguente.

1. **Avvia individuazione**:
    - Quando si avvia l'individuazione nell'appliance Hyper-V, comunica con gli host Hyper-V sulle porte WinRM 5985 (HTTP) e 5986 (HTTPS).
    - Quando si avvia l'individuazione nell'appliance VMware, per impostazione predefinita comunica con il server vCenter sulla porta TCP 443. Se il server vCenter è in ascolto su una porta diversa, è possibile configurarla nell'app Web Appliance.
2. **Raccogliere i metadati e i dati sulle prestazioni**:
    - L'appliance usa una sessione di Common Information Model (CIM) per raccogliere i dati delle macchine virtuali Hyper-V dall'host Hyper-V sulle porte 5985 e 5986.
    - Per impostazione predefinita, l'appliance comunica con la porta 443 per raccogliere i dati delle macchine virtuali VMware dal server vCenter.
3. **Invia dati**: il dispositivo invia i dati raccolti a Azure migrate server Assessment e Azure migrate migrazione del server sulla porta SSL 443. Il dispositivo è in grado di connettersi ad Azure tramite Internet oppure è possibile usare ExpressRoute con peering pubblico/Microsoft.
    - Per i dati sulle prestazioni, l'appliance raccoglie i dati di utilizzo in tempo reale.
        - I dati sulle prestazioni vengono raccolti ogni 20 secondi per VMware e ogni 30 secondi per Hyper-V per ogni metrica delle prestazioni.
        - Viene eseguito il rollup dei dati raccolti per creare un singolo punto dati per 10 minuti.
        - Il valore di picco dell'utilizzo viene selezionato da tutti i punti dati da 20 a 30 secondi e inviato ad Azure per il calcolo della valutazione.
        - In base al valore percentile specificato nelle proprietà di valutazione (cinquantesimo/90/95/99 °), i punti di dieci minuti sono ordinati in ordine crescente e il valore percentile appropriato viene usato per calcolare la valutazione
    - Per la migrazione del server, l'appliance avvia la raccolta dei dati della macchina virtuale e la replica in Azure.
4. Valutazione **e migrazione**: è ora possibile creare valutazioni dei metadati raccolti dal dispositivo usando Azure migrate Assessment server. Inoltre, è possibile avviare la migrazione di macchine virtuali VMware con Azure Migrate migrazione del server per orchestrare la replica di VM senza agenti.


![Architettura](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Aggiornamenti Appliance

Il dispositivo viene aggiornato quando vengono aggiornati gli agenti Azure Migrate in esecuzione nell'appliance.

- Questa operazione viene eseguita automaticamente perché per impostazione predefinita l'aggiornamento automatico è abilitato nell'appliance.
- È possibile modificare questa impostazione predefinita per aggiornare manualmente gli agenti.
- Per disabilitare l'aggiornamento automatico, passare all'editor del registro di sistema > HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\AzureAppliance e impostare la chiave del registro di sistema "AutoUpdate" su 0 (DWORD).
 
### <a name="set-agent-updates-to-manual"></a>Impostare gli aggiornamenti dell'agente su manuale

Per gli aggiornamenti manuali, assicurarsi di aggiornare contemporaneamente tutti gli agenti nell'appliance, usando il pulsante **Aggiorna** per ogni agente obsoleto nell'appliance. È possibile riportare l'aggiornamento automatico degli aggiornamenti in qualsiasi momento.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come](tutorial-assess-vmware.md#set-up-the-appliance-vm) configurare l'appliance per VMware.
[Informazioni su come](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) configurare l'appliance per Hyper-V.

