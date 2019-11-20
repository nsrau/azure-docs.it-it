---
title: Architettura Azure Migrate Appliance
description: Viene fornita una panoramica dell'appliance Azure Migrate utilizzata per la valutazione e la migrazione dei server.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: bdc81820b1ac9867d45fd26e26d24c65e20641e4
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185824"
---
# <a name="azure-migrate-appliance"></a>Appliance Azure Migrate

Questo articolo descrive l'appliance Azure Migrate. Si distribuisce l'appliance quando si usano Azure Migrate strumenti di valutazione e migrazione per individuare, valutare ed eseguire la migrazione di app, infrastruttura e carichi di lavoro a Microsoft Azure. 

[Azure migrate](migrate-services-overview.md) offre un hub centrale per tenere traccia dell'individuazione, della valutazione e della migrazione delle app e dei carichi di lavoro locali e delle macchine virtuali del cloud privato/pubblico in Azure. L'hub fornisce gli strumenti di Azure Migrate per la valutazione e la migrazione, oltre a offerte di fornitori di software indipendenti (ISV) di terze parti.



## <a name="appliance-overview"></a>Panoramica dell'appliance

I tipi di appliance Azure Migrate e l'utilizzo sono i seguenti.

**Distribuito come** | **Usata per** | **Dettagli**
--- | --- |  ---
Macchina virtuale VMware | Valutazione delle VM VMware con lo strumento Azure Migrate assessment.<br/><br/> Migrazione senza agente di macchine virtuali VMware con lo strumento di migrazione di Azure Migrate server | Scaricare il modello OVA e importarlo in server vCenter per creare la macchina virtuale dell'appliance.
Macchina virtuale Hyper-V | Valutazione delle VM Hyper-V con lo strumento Azure Migrate assessment. | Scaricare il disco rigido virtuale compresso e importarlo in Hyper-V per creare la macchina virtuale dell'appliance.

## <a name="appliance-access"></a>Accesso Appliance

Dopo aver configurato il dispositivo, è possibile accedere in modalità remota alla macchina virtuale dell'appliance tramite la porta TCP 3389. È anche possibile accedere in remoto all'app di gestione Web per l'appliance sulla porta 44368 con URL: `https://<appliance-ip-or-name>:44368`.

## <a name="appliance-license"></a>Licenza Appliance
L'Appliance viene fornita con una licenza di valutazione di Windows Server 2016, valida per 180 giorni. Se il periodo di valutazione è prossimo alla scadenza, è consigliabile scaricare e distribuire una nuova appliance oppure attivare la licenza del sistema operativo della VM del dispositivo.

## <a name="appliance-agents"></a>Agenti Appliance
Nell'appliance sono installati questi agenti.

**Agent** | **Dettagli**
--- | ---
Agente di individuazione | Raccoglie i dati di configurazione delle macchine virtuali locali
Agente di valutazione | Consente di profilare l'ambiente locale per raccogliere i dati sulle prestazioni della macchina virtuale.
Adapter di migrazione | Orchestra la replica della macchina virtuale e coordina la comunicazione tra le macchine virtuali e Azure.
Gateway di migrazione | Invia i dati della macchina virtuale replicata in Azure.


## <a name="appliance-deployment-requirements"></a>Requisiti di distribuzione dell'appliance

- [Esaminare](migrate-support-matrix-vmware.md#assessment-appliance-requirements) i requisiti di distribuzione per un appliance VMware e gli URL a cui l'appliance deve accedere.
- [Esaminare](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) i requisiti di distribuzione per un appliance Hyper-V e gli URL a cui l'appliance deve accedere.


## <a name="collected-performance-data-vmware"></a>Dati sulle prestazioni raccolti-VMware

Ecco i dati sulle prestazioni delle macchine virtuali VMware che l'appliance raccoglie e Invia ad Azure.

**Dati** | **Contatore** | **Impatto valutazione**
--- | --- | ---
Uso della CPU | cpu.usage.average | Costo/dimensioni VM consigliate
Utilizzo della memoria | mem.usage.average | Costo/dimensioni VM consigliate
Velocità effettiva lettura disco (MB al secondo) | virtualDisk.read.average | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni della macchina virtuale
Velocità effettiva scrittura disco (MB al secondo) | virtualDisk.write.average | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni della macchina virtuale
Operazioni di lettura disco al secondo | virtualDisk.numberReadAveraged.average | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni della macchina virtuale
Operazioni di scrittura su disco al secondo | virtualDisk.numberWriteAveraged.average  | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni della macchina virtuale
Velocità effettiva di lettura NIC (MB al secondo) | net.received.average | Calcolo delle dimensioni della macchina virtuale
Velocità effettiva scrittura NIC (MB al secondo) | net.transmitted.average  |Calcolo delle dimensioni della macchina virtuale


## <a name="collected-metadata-vmware"></a>Metadati raccolti-VMware

> [!NOTE]
> I metadati individuati dal dispositivo Azure Migrate vengono usati per semplificare le operazioni di ridimensionamento delle applicazioni durante la migrazione in Azure, l'analisi dell'idoneità di Azure, l'analisi delle dipendenze delle applicazioni e la pianificazione dei costi. Microsoft non utilizza questi dati in relazione a qualsiasi controllo di conformità delle licenze.

Di seguito è riportato l'elenco completo dei metadati delle macchine virtuali VMware che l'appliance raccoglie e Invia ad Azure.

**Dati** | **Contatore**
--- | --- 
**Dettagli computer** | 
ID macchina virtuale | vm.Config.InstanceUuid 
Nome della VM | vm.Config.Name
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



## <a name="collected-performance-data-hyper-v"></a>Dati sulle prestazioni raccolti-Hyper-V

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

## <a name="collected-metadata-hyper-v"></a>Metadati raccolti-Hyper-V

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
Tipo di disco rigido virtuale | Msvm_VirtualHardDiskSettingData | digitare
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
3. **Invia dati**: il dispositivo invia i dati raccolti a Azure migrate server Assessment e Azure migrate migrazione del server sulla porta SSL 443.
    - Per i dati sulle prestazioni, l'appliance raccoglie i dati di utilizzo in tempo reale.
        - I dati sulle prestazioni vengono raccolti ogni 20 secondi per VMware e ogni 30 secondi per Hyper-V per ogni metrica delle prestazioni.
        - Viene eseguito il rollup dei dati raccolti per creare un singolo punto dati per dieci minuti.
        - Il valore di picco dell'utilizzo viene selezionato da tutti i punti dati di 20/30 secondi e inviato ad Azure per il calcolo della valutazione.
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

