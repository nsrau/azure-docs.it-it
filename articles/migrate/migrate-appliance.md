---
title: Architettura di appliance Azure Migrate | Microsoft Docs
description: Viene fornita una panoramica dell'appliance Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: c2c9ca3082aa9c2067a63f8d6304e8a229dac14a
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811453"
---
# <a name="azure-migrate-appliance"></a>Appliance Azure Migrate

Questo articolo descrive l'appliance Azure Migrate. Si distribuisce il dispositivo quando si usano strumenti di migrazione e valutazione di Azure Migrate per individuare, valutare ed eseguire la migrazione di App, infrastruttura e i carichi di lavoro in Microsoft Azure. 

[Azure Migrate](migrate-services-overview.md) fornisce un hub centrale per tenere traccia di individuazione, valutazione e migrazione delle App in locale e i carichi di lavoro e cloud privati/pubblici macchine virtuali di Azure. L'hub fornisce gli strumenti di Azure Migrate per la valutazione e migrazione, nonché le offerte di fornitori di software indipendenti di terze parti.



## <a name="appliance-overview"></a>Panoramica delle Appliance

I tipi di dispositivo di Azure Migrate e utilizzo sono i seguenti.

**Distribuito come** | **Usata per** | **Dettagli**
--- | --- |  ---
Macchina virtuale VMware | Valutazione delle VM VMware con lo strumento di valutazione di Azure Migrate.<br/><br/> Migrazione senza agente VM VMware con lo strumento di migrazione del Server eseguire la migrazione di Azure | Scaricare il modello con estensione OVA e importare in vCenter Server per la creazione della macchina virtuale dell'appliance.
Macchina virtuale Hyper-V | Valutazione delle VM Hyper-V con lo strumento di valutazione di Azure Migrate. | Scaricare un file VHD compresso e importare in Hyper-V per creare l'appliance della macchina virtuale.

## <a name="appliance-access"></a>Accesso dispositivo

Dopo aver configurato l'appliance, è possibile accedere in remoto all'appliance di macchina virtuale tramite la porta TCP 3389. È possibile accedere in modalità remota l'app web di gestione per l'appliance, sulla porta 44368 con URL: ``` https://<appliance-ip-or-name>:44368 ```.

## <a name="appliance-license"></a>Licenza dispositivo
L'appliance viene fornito con una licenza di valutazione di Windows Server 2016, questa operazione è valida per 180 giorni. Se il periodo di valutazione sta scadenza, è consigliabile scaricare e distribuire una nuova appliance oppure di attivare la licenza del sistema operativo della macchina virtuale dell'appliance.

## <a name="appliance-agents"></a>Agenti di Appliance
L'appliance abbia questi agenti installati.

**Agent** | **Dettagli**
--- | ---
Agente di individuazione | Raccoglie i dati di configurazione da macchine virtuali locali.
Agente di valutazione | Profila l'ambiente locale per raccogliere dati sulle prestazioni della macchina virtuale.
Scheda di migrazione | Orchestra la replica delle macchine Virtuali e coordina le comunicazioni tra le macchine virtuali e Azure.
Gateway di migrazione | Invia replicati i dati della macchina virtuale in Azure.


## <a name="appliance-deployment-requirements"></a>Requisiti di distribuzione del dispositivo

- [Revisione](migrate-support-matrix-vmware.md#assessment-appliance-requirements) i requisiti di distribuzione per un'appliance di VMware e gli URL che l'appliance deve accedere.
- [Revisione](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) i requisiti di distribuzione per un'appliance di Hyper-V e gli URL che l'appliance deve accedere.


## <a name="collected-performance-data-vmware"></a>I dati sulle prestazioni raccolti-VMware

Ecco i dati sulle prestazioni di VM VMware che l'appliance raccoglie e invia ad Azure.

**Dati** | **Contatore** | **Impatto sulla valutazione**
--- | --- | ---
Uso della CPU | cpu.usage.average | Le dimensioni e i costi della VM
Utilizzo della memoria | mem.usage.average | Le dimensioni e i costi della VM
Velocità effettiva (MB al secondo) letti da disco | virtualDisk.read.average | Calcolo per le dimensioni del disco, costi di archiviazione, le dimensioni della macchina virtuale
Velocità effettiva di scrittura disco (MB al secondo) | virtualDisk.write.average | Calcolo per le dimensioni del disco, costi di archiviazione, le dimensioni della macchina virtuale
Operazioni al secondo letti da disco | virtualDisk.numberReadAveraged.average | Calcolo per le dimensioni del disco, costi di archiviazione, le dimensioni della macchina virtuale
Scritture disco/operazioni al secondo | virtualDisk.numberWriteAveraged.average  | Calcolo per le dimensioni del disco, costi di archiviazione, le dimensioni della macchina virtuale
Interfaccia di rete letti velocità effettiva (MB al secondo) | net.received.average | Calcolo di dimensioni della macchina virtuale
Velocità effettiva di scrittura di interfaccia di rete (MB al secondo) | net.transmitted.average  |Calcolo di dimensioni della macchina virtuale


## <a name="collected-metadata-vmware"></a>I metadati raccolti-VMware

Ecco l'elenco completo dei metadati di VM VMware che l'appliance raccoglie e invia ad Azure.

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
Numero di dischi | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk).count
Elenco dimensioni disco | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk)
Elenco schede di rete | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualEthernet).count
Uso della CPU | cpu.usage.average
Utilizzo della memoria |mem.usage.average
**Dettagli disco** | 
Valore chiave disco | disk.Key
Numero Dikunit | disk.UnitNumber
Valore chiave controller del disco | disk.ControllerKey.Value
Gigabyte con provisioning | virtualDisk.DeviceInfo.Summary
Nome del disco | Valore generato utilizzo disco. UnitNumber, disco. Chiave, disco. ControllerKey.VAlue
Operazioni di lettura per secondo | virtualDisk.numberReadAveraged.average
Operazioni di scrittura per secondo | virtualDisk.numberWriteAveraged.average
Velocità effettiva di lettura (MB al secondo) | virtualDisk.read.average
Scrivere la velocità effettiva (MB al secondo) | virtualDisk.write.average
**Per informazioni dettagliate di interfaccia di rete** | 
Nome scheda di rete | nic.Key
Indirizzo MAC | ((VirtualEthernetCard)nic).MacAddress
Indirizzi IPv4 | vm.Guest.Net
Indirizzi IPv6 | vm.Guest.Net
Velocità effettiva di lettura (MB al secondo) | net.received.average
Scrivere la velocità effettiva (MB al secondo) | net.transmitted.average
**Dettagli del percorso di inventario** | 
Name | container.GetType().Name
Tipo di oggetto figlio | container.ChildType
Informazioni di riferimento | container.MoRef
Dettagli elemento padre | Container.Parent
Dettagli della cartella per ogni macchina virtuale | ((Folder)container).ChildEntity.Type
Informazioni dettagliate di Data Center per ogni macchina virtuale | ((Datacenter)container).VmFolder
Informazioni dettagliate di Data Center per ogni cartella host | ((Datacenter)container).HostFolder
Dettagli dei cluster per ogni host | ((ClusterComputeResource)container).Host
Dettagli dell'host per macchina virtuale | ((HostSystem)container).VM



## <a name="collected-performance-data-hyper-v"></a>Sulle prestazioni raccolti dati Hyper-V

Ecco i dati sulle prestazioni di VM VMware che l'appliance raccoglie e invia ad Azure.

**Classe del contatore delle prestazioni** | **Contatore** | **Impatto sulla valutazione**
--- | --- | ---
Processore virtuale Hypervisor Hyper-V | % Tempo di esecuzione Guest | Le dimensioni e i costi della VM
Memoria dinamica di Hyper-V della macchina virtuale | Utilizzo elevato corrente (%)<br/> Memoria fisica visibile guest (MB) | Le dimensioni e i costi della VM
Dispositivo di archiviazione virtuale Hyper-V | Leggere i byte al secondo | Calcolo per le dimensioni del disco, costi di archiviazione, le dimensioni della macchina virtuale
Dispositivo di archiviazione virtuale Hyper-V | Scrivere byte al secondo | Calcolo per le dimensioni del disco, costi di archiviazione, le dimensioni della macchina virtuale
Scheda di rete virtuale di Hyper-V | Byte ricevuti al secondo | Calcolo di dimensioni della macchina virtuale
Scheda di rete virtuale di Hyper-V | Byte inviati al secondo | Calcolo di dimensioni della macchina virtuale

- Utilizzo della CPU è la somma di tutti gli utilizzi, per tutti i processori virtuali collegati a una macchina virtuale.
- Utilizzo della memoria è (pressione corrente * memoria fisica visibile Guest) / 100.
- I valori di utilizzo del disco e rete vengono raccolti dai contatori delle prestazioni Hyper-V elencati.

## <a name="collected-metadata-hyper-v"></a>Raccogliere i metadati Hyper-V

Ecco l'elenco completo dei metadati di macchina virtuale Hyper-V che l'appliance raccoglie e invia ad Azure.

**Dati** | **Classe WMI** | **Proprietà della classe WMI**
--- | --- | ---
**Dettagli computer** | 
Numero di serie del BIOS _ Msvm_BIOSElement | BIOSSerialNumber
Tipo di macchina virtuale (generazione 1 o 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Nome visualizzato della macchina virtuale | Msvm_VirtualSystemSettingData | ElementName
Versione della macchina virtuale | Msvm_ProcessorSettingData | VirtualQuantity
Memoria (byte) | Msvm_MemorySettingData | VirtualQuantity
Quantità massima di memoria che possono essere usati dalla macchina virtuale | Msvm_MemorySettingData | Limite
Memoria dinamica abilitata | Msvm_MemorySettingData | DynamicMemoryEnabled
Nome di sistema operativo/versione/FQDN | Msvm_KvpExchangeComponent | Nome GuestIntrinsciExchangeItems dati
Stato di alimentazione della macchina virtuale | Msvm_ComputerSystem | EnabledState
**Dettagli disco** | 
Identificatore del disco | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Tipo di disco rigido virtuale | Msvm_VirtualHardDiskSettingData | Type
Dimensioni del disco rigido virtuale | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Disco rigido virtuale padre | Msvm_VirtualHardDiskSettingData | ParentPath
**Per informazioni dettagliate di interfaccia di rete** | 
Indirizzi IP (schede di rete sintetiche) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP abilitato (schede di rete sintetiche) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
ID di interfaccia di rete (NIC sintetica) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Indirizzo MAC di interfaccia di rete (NIC sintetica) | Msvm_SyntheticEthernetPortSettingData | Indirizzo
ID di interfaccia di rete (NIC legacy) | Dati MsvmEmulatedEthernetPortSetting | InstanceID
ID MAC di interfaccia di rete (NIC legacy) | Dati MsvmEmulatedEthernetPortSetting | Indirizzo




## <a name="discovery-and-collection-process"></a>Processo di individuazione e la raccolta

L'appliance comunica con il server vCenter e host/cluster Hyper-V usando la procedura seguente.


1. **Avviare l'individuazione**:
    - Quando si avvia l'individuazione nell'appliance di Hyper-V, comunica con gli host Hyper-V su porte WinRM 5985 (HTTP) e 5986 (HTTPS).
    - Quando si avvia individuazione nell'appliance di VMware, comunica con il server vCenter sulla porta TCP 443 per impostazione predefinita. Se il server vCenter è in ascolto su una porta diversa, è possibile configurarlo nell'app web di appliance.
2. **Raccogliere i dati dei metadati e le prestazioni**:
    - L'appliance utilizza una sessione di modello CIM (Common Information) per raccogliere i dati della macchina virtuale Hyper-V dall'host Hyper-V su porte 5985 e 5986.
    - L'appliance comunica con la porta 443 per impostazione predefinita, per raccogliere dati di VM VMware dal Server vCenter.
3. **Inviare dati**: L'appliance invia i dati raccolti alla valutazione di Azure Migrate Server e la migrazione di Server eseguire la migrazione di Azure tramite la porta SSL 443.
    - Per i dati sulle prestazioni, l'appliance raccoglie i dati di utilizzo in tempo reale.
        - I dati sulle prestazioni vengono raccolti ogni 20 secondi per VMware e ogni 30 secondi per Hyper-V, per ogni metrica di prestazioni.
        - I dati raccolti viene eseguito il rollup per creare un singolo punto dati per dieci minuti.
        - Il valore di utilizzo di picco è selezionato da tutti i 20 o 30 secondo punto dati e inviati ad Azure per il calcolo della valutazione.
        - Basato sul valore percentile specificato nelle proprietà della valutazione (50 ° / 90 º / 95 ° / 99. °), i punti di dieci minuti vengono ordinati in ordine crescente e il valore percentile appropriato viene utilizzato per calcolare la valutazione
    - Per la migrazione di Server, il dispositivo avvia la raccolta dei dati della macchina virtuale ed è la replica in Azure.
4. **Valutazione e la migrazione**: È ora possibile creare le valutazioni dai metadati raccolti dall'appliance usando Azure eseguire la migrazione di Server Assessment. Inoltre, è anche possibile avviare la migrazione di macchine virtuali VMware tramite migrazione del Server eseguire la migrazione di Azure per orchestrare la replica delle macchine Virtuali senza agente.


![Architettura](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Aggiornamenti delle Appliance

L'appliance viene aggiornata perché gli agenti di Azure Migrate in esecuzione nell'appliance sono aggiornati.

- Ciò avviene automaticamente perché l'aggiornamento automatico è abilitato nell'appliance per impostazione predefinita.
- È possibile modificare questa impostazione predefinita per aggiornare manualmente gli agenti.
- Per disabilitare gli aggiornamenti automatici, impostare la chiave del Registro di sistema Appliance, aggiornamento automatico, in HKLM\SOFTWAREMicrosoft\Azure.

### <a name="set-agent-updates-to-manual"></a>Set di aggiornamenti degli agenti su manuale

Per gli aggiornamenti manuali, assicurarsi di aggiornare tutti gli agenti nell'appliance nello stesso momento, usando il **aggiornare** pulsante per ogni agente obsoleti nell'appliance. È possibile passare all'impostazione di aggiornamento agli aggiornamenti automatici in qualsiasi momento.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come](tutorial-assess-vmware.md#set-up-the-appliance-vm) per configurare l'appliance per VMware.
[Informazioni su come](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) per configurare l'appliance per Hyper-V.

