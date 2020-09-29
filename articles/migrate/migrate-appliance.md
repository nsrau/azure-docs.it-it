---
title: Appliance Azure Migrate
description: Fornisce un riepilogo del supporto per Azure Migrate Appliance.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: ac3c90f1c09d290d5112a0e0d7abc5218788caf7
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450044"
---
# <a name="azure-migrate-appliance"></a>Appliance Azure Migrate

Questo articolo riepiloga i prerequisiti e i requisiti di supporto per l'appliance di Azure Migrate. 

## <a name="deployment-scenarios"></a>Scenari di distribuzione

L'appliance di Azure Migrate viene usata negli scenari seguenti.

**Scenario** | **Strumento** | **Usata per** 
--- | --- | ---
**Valutazione della macchina virtuale VMware** | Azure Migrate: Valutazione server | Individuare le macchine virtuali VMware<br/><br/> Individuare le app e le dipendenze del computer<br/><br/> Raccogliere metadati sul computer e sulle prestazioni per le valutazioni.
**Migrazione senza agente della macchina virtuale VMware** | Azure Migrate: Migrazione del server | Individuare le macchine virtuali VMware <br/><br/> Eseguire la replica delle macchine virtuali VMware con la migrazione senza agente.
**Valutazione della macchina virtuale Hyper-V** | Azure Migrate: Valutazione server | Individuare le macchine virtuali Hyper-V<br/><br/> Raccogliere metadati sul computer e sulle prestazioni per le valutazioni.
**Valutazione del computer fisico** |  Azure Migrate: Valutazione server |  Individuare i server fisici o le macchine virtuali considerate server fisici.<br/><br/> Raccogliere metadati sul computer e sulle prestazioni per le valutazioni.

## <a name="deployment-methods"></a>Metodi di distribuzione

L'appliance può essere distribuita usando due metodi:

- L'appliance può essere distribuita usando un modello per le macchine virtuali VMware e Hyper-V, ovvero un modello OVA per VMware o un disco rigido virtuale per Hyper-V.
- Se non si desidera usare un modello, è possibile distribuire l'appliance per VMware o Hyper-V usando uno script di PowerShell.
- In Azure per enti pubblici è necessario distribuire l'appliance tramite uno script.
- Per i server fisici, l'appliance viene distribuita sempre usando uno script.
- I collegamenti di download sono disponibili nelle tabelle seguenti.


## <a name="appliance---vmware"></a>Appliance - VMware 

La tabella seguente riepiloga i requisiti dell'appliance di Azure Migrate per VMware.

**Requisito** | **VMware** 
--- | ---
**Autorizzazioni** | Per accedere all'app Web dell'appliance in locale o in remoto, è necessario essere amministratore di dominio o amministratore locale nel computer dell'appliance.
**Componenti dell'appliance** | L'appliance include i componenti seguenti:<br/><br/> - **App di gestione**: si tratta di un'app Web per l'input dell'utente durante la distribuzione dell'appliance. È usata durante la valutazione dei computer per la migrazione ad Azure.<br/> - **Agente di individuazione**: l'agente raccoglie i dati di configurazione del computer. È usata durante la valutazione dei computer per la migrazione ad Azure.<br/>- **Agente di raccolta**: l'agente raccoglie i dati sulle prestazioni. È usata durante la valutazione dei computer per la migrazione ad Azure.<br/>- **Servizio di aggiornamento automatico**: aggiorna i componenti dell'appliance, viene eseguito ogni 24 ore.<br/>- **Agente DRA**: organizza la replica della macchina virtuale e coordina la comunicazione tra computer replicati e Azure. È usato solo quando si esegue la replica di macchine virtuali VMware in Azure con la migrazione senza agente.<br/>- **Gateway**: invia i dati replicati ad Azure. È usato solo quando si esegue la replica di macchine virtuali VMware in Azure con la migrazione senza agente.
**Distribuzione supportata** | Distribuire come macchina virtuale di VMware usando il modello OVA.<br/><br/> Distribuire come macchina virtuale VMware o computer fisico con uno script di installazione di PowerShell.
**Supporto del progetto** |  Un'appliance può essere associata a un solo progetto. <br/> È possibile associare a un progetto un numero qualsiasi di appliance.<br/> 
**Limiti dell'individuazione** | Un'appliance può individuare fino a 10.000 macchine virtuali VMware in un server vCenter.<br/> Un'appliance può connettersi a un solo server vCenter.
**Modello OVA** | Scaricare dal portale o da [qui](https://go.microsoft.com/fwlink/?linkid=2140333)<br/><br/> Le dimensioni del download sono di 11,9 GB.<br/><br/> Il modello di appliance scaricato include una licenza di valutazione di Windows Server 2016 valida per 180 giorni. Se il periodo di valutazione sta per terminare, è consigliabile scaricare e distribuire una nuova appliance oppure attivare la licenza del sistema operativo della macchina virtuale dell'appliance.
**Script di PowerShell** | Vedere questo [articolo](./deploy-appliance-script.md#set-up-the-appliance-for-vmware).<br/><br/> 
**Software/hardware** |  L'appliance deve essere eseguita in un computer con Windows Server 2016, con una RAM di 32 GB, 8 vCPU, circa 80 GB di spazio di archiviazione su disco e un commutatore virtuale esterno.<br/> L'appliance richiede l'accesso a Internet, direttamente o tramite un proxy.<br/><br/> Se si esegue l'appliance in una macchina virtuale VMware, è necessario disporre di risorse sufficienti nel server vCenter per allocare una macchina virtuale che soddisfa i requisiti.<br/><br/> Se si esegue l'appliance in un computer fisico, assicurarsi che esegua Windows Server 2016 e che soddisfi i requisiti hardware.
**Requisiti di VMware** | Se si distribuisce l'appliance come macchina virtuale VMware, questa deve essere distribuita in un host ESXi che esegue la versione 5.5 o successiva.<br/><br/> Server vCenter che esegue la versione 5.5, 6.0, 6.5 o 6.7.
**VDDK (migrazione senza agente)** | Se si distribuisce l'appliance come macchina virtuale VMware e si esegue una migrazione senza agente, è necessario installare VMware vSphere VDDK nella macchina virtuale dell'appliance.
**Valore hash - OVA** | [Verificare](tutorial-discover-vmware.md#verify-security) i valori hash del modello OVA.
**Valore hash - Script di PowerShell** | [Verificare](deploy-appliance-script.md#verify-file-security) i valori hash dello script di PowerShell.




## <a name="appliance---hyper-v"></a>Appliance - Hyper-V

**Requisito** | **Hyper-V** 
--- | ---
**Autorizzazioni** | Per accedere all'app Web dell'appliance in locale o in remoto, è necessario essere amministratore di dominio o amministratore locale nel computer dell'appliance.
**Componenti dell'appliance** | L'appliance include i componenti seguenti:<br/><br/>- **App di gestione**: si tratta di un'app Web per l'input dell'utente durante la distribuzione dell'appliance. È usata durante la valutazione dei computer per la migrazione ad Azure.<br/> - **Agente di individuazione**: l'agente raccoglie i dati di configurazione del computer. È usata durante la valutazione dei computer per la migrazione ad Azure.<br/>- **Agente di raccolta**: l'agente raccoglie i dati sulle prestazioni. È usata durante la valutazione dei computer per la migrazione ad Azure.<br/>- **Servizio di aggiornamento automatico**: aggiorna i componenti dell'appliance, viene eseguito ogni 24 ore.
**Distribuzione supportata** | Distribuire come macchina virtuale Hyper-V usando un modello di disco rigido virtuale.<br/><br/> Distribuire come macchina virtuale Hyper-V o computer fisico con uno script di installazione di PowerShell.
**Supporto del progetto** |  Un'appliance può essere associata a un solo progetto. <br/> È possibile associare a un progetto un numero qualsiasi di appliance.<br/> 
**Limiti dell'individuazione** | Un'appliance consente di individuare fino a 5000 macchine virtuali Hyper-V.<br/> Un'appliance può connettersi al massimo con 300 host Hyper-V.
**Modello di disco rigido virtuale** | Cartella compressa con include il disco rigido virtuale. Scaricare dal portale o da [qui](https://go.microsoft.com/fwlink/?linkid=2140422).<br/><br/> Le dimensioni del download sono di 8,91 GB.<br/><br/> Il modello di appliance scaricato include una licenza di valutazione di Windows Server 2016 valida per 180 giorni. Se il periodo di valutazione sta per terminare, è consigliabile scaricare e distribuire una nuova appliance oppure attivare la licenza del sistema operativo della macchina virtuale dell'appliance.
**Script di PowerShell** | Vedere questo [articolo](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).<br/><br/> 
**Software/hardware***   |  Il dispositivo deve essere eseguito in un computer con Windows Server 2016, 16 GB di RAM, 8 vCPU, circa 80 GB di spazio di archiviazione su disco e un Commuter virtuale esterno.<br/> L'appliance deve avere un indirizzo IP statico o dinamico e richiede l'accesso a Internet, direttamente o tramite un proxy.<br/><br/> Se si esegue l'appliance come macchina virtuale Hyper-V, è necessario disporre di risorse sufficienti nell'host Hyper-V per allocare i requisiti hardware.<br/><br/> Se si esegue l'appliance in un computer fisico, assicurarsi che esegua Windows Server 2016 e che soddisfi i requisiti hardware. 
**Requisiti di Hyper-V** | Se si distribuisce l'appliance con il modello di disco rigido virtuale, la macchina virtuale dell'appliance messa a disposizione da Azure Migrate è la macchina virtuale Hyper-V versione 5.0.<br/><br/> L'host Hyper-V deve eseguire Windows Server 2012 R2 o una versione successiva. 
**Valore hash - Disco rigido virtuale** | [Verifica](tutorial-discover-hyper-v.md#verify-security) Valori hash del modello VHD.
**Valore hash - Script di PowerShell** | [Verificare](deploy-appliance-script.md#verify-file-security) i valori hash dello script di PowerShell.


## <a name="appliance---physical"></a>Appliance - Computer fisico

**Requisito** | **Computer fisico** 
--- | ---
**Autorizzazioni** | Per accedere all'app Web dell'appliance in locale o in remoto, è necessario essere amministratore di dominio o amministratore locale nel computer dell'appliance.
**Componenti dell'appliance** | L'appliance include i componenti seguenti: <br/><br/> - **App di gestione**: si tratta di un'app Web per l'input dell'utente durante la distribuzione dell'appliance. È usata durante la valutazione dei computer per la migrazione ad Azure.<br/> - **Agente di individuazione**: l'agente raccoglie i dati di configurazione del computer. È usata durante la valutazione dei computer per la migrazione ad Azure.<br/>- **Agente di raccolta**: l'agente raccoglie i dati sulle prestazioni. È usata durante la valutazione dei computer per la migrazione ad Azure.<br/>- **Servizio di aggiornamento automatico**: aggiorna i componenti dell'appliance, viene eseguito ogni 24 ore.
**Distribuzione supportata** | Distribuire come computer fisico dedicato o macchina virtuale con uno script di installazione di PowerShell. Lo script può essere scaricato dal portale.
**Supporto del progetto** |  Un'appliance può essere associata a un solo progetto. <br/> È possibile associare a un progetto un numero qualsiasi di appliance.<br/> 
**Limiti dell'individuazione** | Un'appliance può individuare fino a 1000 di server fisici.
**Script di PowerShell** | Scaricare lo script (AzureMigrateInstaller.ps1) in una cartella compressa dal portale o da [qui](https://go.microsoft.com/fwlink/?linkid=2140334). [Altre informazioni](tutorial-discover-physical.md)<br/><br/> Le dimensioni del download sono pari a 85,8 MB.
**Software/hardware** |  Il dispositivo deve essere eseguito in un computer con Windows Server 2016, 16 GB di RAM, 8 vCPU, circa 80 GB di spazio di archiviazione su disco.<br/> L'appliance deve avere un indirizzo IP statico o dinamico e richiede l'accesso a Internet, direttamente o tramite un proxy.<br/><br/> Se si esegue l'appliance in un computer fisico, assicurarsi che esegua Windows Server 2016 e che soddisfi i requisiti hardware.<br/>_Attualmente la distribuzione del dispositivo è supportata solo in Windows Server 2016._
**Valore hash** | [Verificare](tutorial-discover-physical.md#verify-security) i valori hash dello script di PowerShell.

## <a name="url-access"></a>accesso con URL

L'appliance di Azure Migrate richiede la connettività a Internet.

- Quando si distribuisce l'appliance, Azure Migrate esegue un controllo della connettività agli URL richiesti.
- È necessario consentire l'accesso a tutti gli URL nell'elenco. Se si sta eseguendo solo la valutazione, è possibile ignorare gli URL contrassegnati come obbligatori solo per la migrazione senza agenti di VMware.
-  Se si usa un proxy basato su URL per eseguire la connessione a Internet, verificare che il proxy risolva tutti i record CNAME ricevuti durante la ricerca degli URL.

### <a name="public-cloud-urls"></a>URL del cloud pubblico

**URL** | **Dettagli**  
--- | --- |
*.portal.azure.com  | Passare al portale di Azure.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *. office.com | Accedere alla sottoscrizione di Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Creare app Azure Active Directory (AD) affinché l'appliance comunichi con Azure Migrate.
management.azure.com | Creare app Azure AD affinché l'appliance comunichi con il servizio Azure Migrate.
*.services.visualstudio.com | Caricare i log delle app usati per il monitoraggio interno.
*.vault.azure.net | Gestire i segreti in Azure Key Vault. Nota: verificare che i computer da replicare abbiano accesso a questo URL.
aka.ms/* | Consentire l'accesso ai collegamenti aka. Usato per gli aggiornamenti dell'appliance di Azure Migrate.
download.microsoft.com/download | Consentire i download dal download Microsoft.
*.servicebus.windows.net | Comunicazione tra l'appliance e il servizio Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Connettersi agli URL del servizio Azure Migrate.
*.hypervrecoverymanager.windowsazure.com | **Usato per la migrazione senza agente VMware**<br/><br/> Connettersi agli URL del servizio Azure Migrate.
*.blob.core.windows.net |  **Usato per la migrazione senza agente VMware**<br/><br/>Caricare i dati nella risorsa di archiviazione per la migrazione.

### <a name="government-cloud-urls"></a>URL del cloud per enti pubblici

**URL** | **Dettagli**  
--- | --- |
*.portal.azure.us  | Passare al portale di Azure.
graph.windows.net | Accedere alla sottoscrizione di Azure.
login.microsoftonline.us  | Creare app Azure Active Directory (AD) affinché l'appliance comunichi con Azure Migrate.
management.usgovcloudapi.net | Creare app Azure AD affinché l'appliance comunichi con il servizio Azure Migrate.
*.services.visualstudio.com | Caricare i log delle app usati per il monitoraggio interno.
*.vault.usgovcloudapi.net | Gestire i segreti in Azure Key Vault.
aka.ms/* | Consentire l'accesso ai collegamenti aka. Usato per gli aggiornamenti dell'appliance di Azure Migrate.
download.microsoft.com/download | Consentire i download dal download Microsoft.
*.servicebus.usgovcloudapi.net  | Comunicazione tra l'appliance e il servizio Azure Migrate.
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | Connettersi agli URL del servizio Azure Migrate.
*.hypervrecoverymanager.windowsazure.us | **Usato per la migrazione senza agente VMware**<br/><br/> Connettersi agli URL del servizio Azure Migrate.
*.blob.core.usgovcloudapi.net  |  **Usato per la migrazione senza agente VMware**<br/><br/>Caricare i dati nella risorsa di archiviazione per la migrazione.
*.applicationinsights.us | Caricare i log delle app usati per il monitoraggio interno.





## <a name="collected-data---vmware"></a>Dati raccolti - VMware

L'appliance raccoglie i metadati, i dati sulle prestazioni e i dati di analisi delle dipendenze, se viene usata l'[analisi delle dipendenze](concepts-dependency-visualization.md) senza agente.

### <a name="metadata"></a>Metadati

I metadati individuati dall'appliance di Azure Migrate consentono di determinare se i computer e le app sono pronti per la migrazione ad Azure, di individuare le macchine virtuali e le app di dimensioni appropriate, i costi dei piani e di analizzare le dipendenze dell'applicazione. Microsoft non usa questi dati nei controlli di conformità delle licenze.

Di seguito è riportato l'elenco completo dei metadati delle macchine virtuali VMware raccolti dall'appliance e inviati ad Azure.

**DATI** | **CONTATORE**
--- | --- 
**Dettagli del computer** | 
ID macchina virtuale | vm.Config.InstanceUuid 
Nome della VM. | vm.Config.Name
ID server vCenter | VMwareClient.Instance.Uuid
Descrizione macchina virtuale | vm.Summary.Config.Annotation
Nome prodotto con licenza | vm.Client.ServiceContent.About.LicenseProductName
Tipo di sistema operativo | vm.SummaryConfig.GuestFullName
Tipo di avvio | vm.Config.Firmware
Numero di core | vm.Config.Hardware.NumCPU
Memoria (MB) | vm.Config.Hardware.MemoryMB
Numero di dischi | vm.Config.Hardware.Device.ToList().FindAll(x => è VirtualDisk).count
Elenco dimensioni disco | vm.Config.Hardware.Device.ToList().FindAll(x => è VirtualDisk)
Elenco schede di rete | vm.Config.Hardware.Device.ToList().FindAll(x => è VirtualEthernet).count
Uso della CPU | cpu.usage.average
Utilizzo della memoria |mem.usage.average
**Dettagli del disco** | 
Valore chiave disco | disk.Key
Numero di unità del disco | disk.UnitNumber
Valore chiave controller del disco | disk.ControllerKey.Value
Gigabyte con provisioning | virtualDisk.DeviceInfo.Summary
Nome del disco | Valore generato usando disk.UnitNumber, disk.Key, disk.ControllerKey.VAlue
Operazioni di lettura per secondo | virtualDisk.numberReadAveraged.average
Operazioni di scrittura per secondo | virtualDisk.numberWriteAveraged.average
Velocità effettiva di lettura (MB al secondo) | virtualDisk.read.average
Velocità effettiva di scrittura (MB al secondo) | virtualDisk.write.average
**Dettagli della scheda di interfaccia di rete** | 
Nome scheda di rete | nic.Key
Indirizzo MAC | ((VirtualEthernetCard)nic).MacAddress
Indirizzi IPv4 | vm.Guest.Net
Indirizzi IPv6 | vm.Guest.Net
Velocità effettiva di lettura (MB al secondo) | net.received.average
Velocità effettiva di scrittura (MB al secondo) | net.transmitted.average
**Dettagli percorso di inventario** | 
Nome | container.GetType().Name
Tipo di oggetto figlio | container.ChildType
Informazioni di riferimento | container.MoRef
Dettagli elemento padre | Container.Parent
Dettagli della cartella per ogni macchina virtuale | ((Folder)container).ChildEntity.Type
Dettagli del data center per ogni macchina virtuale | ((Datacenter)container).VmFolder
Dettagli del data center per ogni cartella host | ((Datacenter)container).HostFolder
Dettagli del cluster per host | ((ClusterComputeResource)container).Host
Dettagli dell'host per ogni macchina virtuale | ((HostSystem)container).VM

### <a name="performance-data"></a>Dati sulle prestazioni


Di seguito sono riportati i dati sulle prestazioni delle macchine virtuali VMware raccolti dall'appliance e inviati ad Azure.

**Dati** | **Contatore** | **Impatto sulla valutazione**
--- | --- | ---
Uso della CPU | cpu.usage.average | Dimensioni/costo della macchina virtuale consigliati
Utilizzo della memoria | mem.usage.average | Dimensioni/costo della macchina virtuale consigliati
Velocità effettiva di lettura del disco (MB al secondo) | virtualDisk.read.average | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni della macchina virtuale
Velocità effettiva di scrittura del disco (MB al secondo) | virtualDisk.write.average | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni della macchina virtuale
Operazioni di lettura da disco al secondo | virtualDisk.numberReadAveraged.average | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni della macchina virtuale
Operazioni di scrittura su disco al secondo | virtualDisk.numberWriteAveraged.average  | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni della macchina virtuale
Velocità effettiva di lettura della scheda di interfaccia di rete (MB al secondo) | net.received.average | Calcolo delle dimensioni della macchina virtuale
Velocità effettiva di scrittura della scheda di interfaccia di rete (MB al secondo) | net.transmitted.average  |Calcolo delle dimensioni della macchina virtuale


### <a name="installed-apps-metadata"></a>Metadati delle app installate

L'individuazione applicazioni raccoglie le applicazioni installate e i dati del sistema operativo.

#### <a name="windows-vm-apps-data"></a>Dati delle app VM Windows

Ecco i dati dell'applicazione installati raccolti dal dispositivo da ogni macchina virtuale abilitata per l'individuazione delle applicazioni. Questi dati vengono inviati ad Azure.

**Dati** | **Percorso del registro** | **Chiave**
--- | --- | ---
Nome applicazione  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
Versione  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion 
Provider  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Publisher

#### <a name="windows-vm-features-data"></a>Dati delle funzionalità della VM Windows

Ecco i dati sulle funzionalità che l'appliance raccoglie da ogni macchina virtuale abilitata per l'individuazione delle applicazioni. Questi dati vengono inviati ad Azure.

**Dati**  | **Cmdlet di PowerShell** | **Proprietà**
--- | --- | ---
Nome  | Get-WindowsFeature  | Nome
Feature Type | Get-WindowsFeature  | FeatureType
Parent  | Get-WindowsFeature  | Parent

#### <a name="windows-vm-sql-server-metadata"></a>Metadati di SQL Server VM Windows

Ecco i metadati di SQL Server raccolti dall'appliance dalle macchine virtuali che eseguono Microsoft SQL Server abilitata per l'individuazione delle applicazioni. Questi dati vengono inviati ad Azure.

**Dati**  | **Percorso del registro**  | **Chiave**
--- | --- | ---
Nome  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL server\istanza Names\SQL  | installedInstance
Edizione  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | Edizione 
Service Pack  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | SP
Versione  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | Versione 

#### <a name="windows-vm-operating-system-data"></a>Dati del sistema operativo della VM Windows

Ecco i dati del sistema operativo che l'appliance raccoglie ogni macchina virtuale abilitata per l'individuazione delle applicazioni. Questi dati vengono inviati ad Azure.

Data  | classe WMI  | Proprietà della classe WMI
--- | --- | ---
Nome  | Win32_operatingsystem  | Sottotitolo
Versione  | Win32_operatingsystem  | Versione
Architecture  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-vm-apps-data"></a>Dati delle app VM Linux

Ecco i dati dell'applicazione installati raccolti dal dispositivo da ogni macchina virtuale abilitata per l'individuazione delle applicazioni. In base al sistema operativo della macchina virtuale, vengono eseguiti uno o più comandi. Questi dati vengono inviati ad Azure.

Data  | Comando
--- | --- 
Nome | rpm, dpkg-query, snap
Versione | rpm, dpkg-query, snap
Provider | rpm, dpkg-query, snap

#### <a name="linux-vm-operating-system-data"></a>Dati del sistema operativo della VM Linux

Ecco i dati del sistema operativo che l'appliance raccoglie ogni macchina virtuale abilitata per l'individuazione delle applicazioni. Questi dati vengono inviati ad Azure.

**Dati**  | **Comando** 
--- | --- | ---
Nome <br/> version | Raccolto da uno o più dei seguenti file:<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version 
Architecture | uname


### <a name="app-dependencies-metadata"></a>Metadati delle dipendenze dell'app

L'analisi delle dipendenze senza agenti raccoglie i dati di connessione e del processo.

#### <a name="windows-vm-app-dependencies-data"></a>Dati sulle dipendenze dell'app VM Windows

Ecco i dati di connessione raccolti dall'appliance in ogni macchina virtuale abilitata all'analisi delle dipendenze senza agenti. Questi dati vengono inviati ad Azure.

**Dati** | **Comando usato** 
--- | --- 
Porta locale | netstat
Indirizzo IP locale | netstat
Porta remota | netstat
Indirizzo IP remoto | netstat
Stato di connessione TCP | netstat
ID di processo | netstat
Numero di connessioni attive | netstat


Ecco i dati del processo raccolti dall'appliance in ogni macchina virtuale abilitata all'analisi delle dipendenze senza agenti. Questi dati vengono inviati ad Azure.

**Dati** | **Classe WMI** | **Proprietà della classe WMI**
--- | --- | ---
Nome del processo | Win32_Process | ExecutablePath
Argomenti del processo | Win32_Process | CommandLine
Nome applicazione | Win32_Process | Parametro VersionInfo.ProductName della proprietà ExecutablePath

#### <a name="linux-vm-app-dependencies-data"></a>Dati sulle dipendenze dell'app VM Linux

Ecco i dati di connessione e del processo raccolti dall'appliance in ogni macchina virtuale Linux abilitata all'analisi delle dipendenze senza agenti. Questi dati vengono inviati ad Azure.

**Dati** | **Comando usato** 
--- | ---
Porta locale | netstat 
Indirizzo IP locale | netstat 
Porta remota | netstat 
Indirizzo IP remoto | netstat 
Stato di connessione TCP | netstat 
Numero di connessioni attive | netstat
ID di processo  | netstat 
Nome del processo | ps
Argomenti del processo | ps
Nome applicazione | dpkg o rpm



## <a name="collected-data---hyper-v"></a>Dati raccolti - Hyper-V

L'appliance raccoglie i metadati, i dati sulle prestazioni e i dati di analisi delle dipendenze, se viene usata l'[analisi delle dipendenze](concepts-dependency-visualization.md) senza agente.

### <a name="metadata"></a>Metadati
I metadati individuati dall'appliance di Azure Migrate consentono di determinare se i computer e le app sono pronti per la migrazione ad Azure, di individuare le macchine virtuali e le app di dimensioni appropriate, i costi dei piani e di analizzare le dipendenze dell'applicazione. Microsoft non usa questi dati nei controlli di conformità delle licenze.

Di seguito è riportato l'elenco completo dei metadati delle macchine virtuali Hyper-V raccolti dall'appliance e inviati ad Azure.

**DATI** | **CLASSE WMI** | **PROPRIETÀ DELLA CLASSE WMI**
--- | --- | ---
**Dettagli del computer** | 
Numero di serie di BIOS _ Msvm_BIOSElement | BIOSSerialNumber
Tipo di macchina virtuale (Gen 1 o 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Nome visualizzato della macchina virtuale | Msvm_VirtualSystemSettingData | ElementName
Versione della macchina virtuale | Msvm_ProcessorSettingData | VirtualQuantity
Memoria (byte) | Msvm_MemorySettingData | VirtualQuantity
Memoria massima che può essere usata dalla macchina virtuale | Msvm_MemorySettingData | Limite
Memoria dinamica abilitata | Msvm_MemorySettingData | DynamicMemoryEnabled
Nome/versione/FQDN del sistema operativo | Msvm_KvpExchangeComponent | Dati nome GuestIntrinsicExchangeItems
Stato di alimentazione della macchina virtuale | Msvm_ComputerSystem | EnabledState
**Dettagli del disco** | 
Identificatore del disco | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Tipo di disco rigido virtuale | Msvm_VirtualHardDiskSettingData | Type
Dimensioni del disco rigido virtuale | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Disco rigido virtuale padre | Msvm_VirtualHardDiskSettingData | ParentPath
**Dettagli della scheda di interfaccia di rete** | 
Indirizzi IP (schede di interfaccia di rete sintetiche) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP abilitato (schede di interfaccia di rete sintetiche) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
ID scheda di interfaccia di rete (schede di interfaccia di rete sintetiche) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Indirizzo MAC scheda di interfaccia di rete (schede di interfaccia di rete sintetiche) | Msvm_SyntheticEthernetPortSettingData | Indirizzo
ID scheda di interfaccia di rete (schede di interfaccia di rete legacy) | Dati MsvmEmulatedEthernetPortSetting | InstanceID
ID MAC scheda di interfaccia di rete (schede di interfaccia di rete legacy) | Dati MsvmEmulatedEthernetPortSetting | Indirizzo

### <a name="performance-data"></a>Dati sulle prestazioni

Di seguito sono riportati i dati sulle prestazioni delle macchine virtuali Hyper raccolti dall'appliance e inviati ad Azure.

**Classe del contatore delle prestazioni** | **Contatore** | **Impatto sulla valutazione**
--- | --- | ---
Processore virtuale hypervisor Hyper-V | % tempo di esecuzione guest | Dimensioni/costo della macchina virtuale consigliati
Macchina virtuale della memoria dinamica Hyper-V | Utilizzo elevato corrente (%)<br/> Memoria fisica visibile guest (MB) | Dimensioni/costo della macchina virtuale consigliati
Dispositivo di archiviazione virtuale Hyper-V | Byte letti al secondo | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni della macchina virtuale
Dispositivo di archiviazione virtuale Hyper-V | Byte scritti al secondo | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni della macchina virtuale
Scheda di rete virtuale Hyper-V | Byte ricevuti al secondo | Calcolo delle dimensioni della macchina virtuale
Scheda di rete virtuale Hyper-V | Byte inviati al secondo | Calcolo delle dimensioni della macchina virtuale

- L'utilizzo della CPU è la somma di tutti gli utilizzi, per tutti i processori virtuali collegati a una macchina virtuale.
- L'utilizzo della memoria è dato da (utilizzo elevato corrente * memoria fisica visibile guest)/100.
- I valori di utilizzo del disco e della rete vengono raccolti dai contatori delle prestazioni di Hyper-V elencati.


## <a name="collected-data---physical"></a>Dati raccolti - Computer fisico

L'appliance raccoglie i metadati, i dati sulle prestazioni e i dati di analisi delle dipendenze, se viene usata l'[analisi delle dipendenze](concepts-dependency-visualization.md) senza agente.

### <a name="windows-metadata"></a>Metadati di Windows

I metadati individuati dall'appliance di Azure Migrate consentono di determinare se i computer e le app sono pronti per la migrazione ad Azure, di individuare le macchine virtuali e le app di dimensioni appropriate, i costi dei piani e di analizzare le dipendenze dell'applicazione. Microsoft non usa questi dati nei controlli di conformità delle licenze.

Di seguito è riportato l'elenco completo dei metadati del server Windows raccolti dall'appliance e inviati ad Azure.

**DATI** | **CLASSE WMI** | **PROPRIETÀ DELLA CLASSE WMI**
--- | --- | ---
Nome di dominio completo | Win32_ComputerSystem | Domain, Name, PartOfDomain
Numero di core del processore | Win32_Processor | NumberOfCores
Memoria allocata | Win32_ComputerSystem | TotalPhysicalMemory
Numero di serie del BIOS | Win32_ComputerSystemProduct | IdentifyingNumber
GUID BIOS | Win32_ComputerSystemProduct | UUID
Tipo di avvio | Win32_DiskPartition | Verificare la partizione con Type = **GPT:System** per EFI/BIOS
Nome del sistema operativo | Win32_OperatingSystem | Sottotitolo
Versione del sistema operativo |Win32_OperatingSystem | Versione
Architettura del sistema operativo | Win32_OperatingSystem | OSArchitecture
Numero di dischi | Win32_DiskDrive | Model, Size, DeviceID, MediaType, Name
Dimensioni disco | Win32_DiskDrive | Dimensione
Elenco delle schede di interfaccia di rete | Win32_NetworkAdapterConfiguration | Description, Index
Indirizzo IP della scheda di interfaccia di rete | Win32_NetworkAdapterConfiguration | IPAddress
Indirizzo MAC della scheda di interfaccia di rete | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-metadata"></a>Metadati di Linux

Di seguito è riportato l'elenco completo dei metadati del server Linux raccolti dall'appliance e inviati ad Azure.

**DATI** | **LINUX** 
--- | --- 
Nome di dominio completo | cat /proc/sys/kernel/hostname, hostname -f
Numero di core del processore |  /proc/cpuinfo \| awk '/^processor/{print $3}' \| wc -l
Memoria allocata | cat /proc/meminfo \| grep MemTotal \| awk '{printf "%.0f", $2/1024}'
Numero di serie del BIOS | lshw \| grep "serial:" \| head -n1 \| awk '{print $2}' <br/> /usr/sbin/dmidecode -t 1 \| grep 'Serial' \| awk '{ $1="" ; $2=""; print}'
GUID BIOS | cat /sys/class/dmi/id/product_uuid
Tipo di avvio | [ -d /sys/firmware/efi ] && echo EFI \|\| echo BIOS
Nome/versione del sistema operativo | Per la versione e il nome del sistema operativo si accede a questi file:<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
Architettura del sistema operativo | Uname -m
Numero di dischi | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
Disco di avvio | df /boot \| sed -n 2p \| awk '{print $1}'
Dimensioni disco | fdisk -l \| egrep 'Disk.*bytes' \| egrep $disk: \| awk '{print $5}'
Elenco delle schede di interfaccia di rete | ip -o -4 addr show \| awk '{print $2}'
Indirizzo IP della scheda di interfaccia di rete | ip addr show $nic \| grep inet \| awk '{print $2}' \| cut -f1 -d "/" 
Indirizzo MAC della scheda di interfaccia di rete | ip addr show $nic \| grep ether  \| awk '{print $2}'

### <a name="windows-performance-data"></a>Dati sulle prestazioni di Windows

Di seguito sono riportati i dati sulle prestazioni del server Windows raccolti dall'appliance e inviati ad Azure.

**Dati** | **Classe WMI** | **Proprietà della classe WMI**
--- | --- | ---
Utilizzo di CPU | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
Utilizzo della memoria | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
Numero di schede di interfaccia di rete | Win32_PerfFormattedData_Tcpip_NetworkInterface | Ottenere il numero di dispositivi di rete.
Dati ricevuti per ogni scheda di interfaccia di rete | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
Dati trasmessi per ogni scheda di interfaccia di rete | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
Numero di dischi | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | Numero di dischi
Dettagli del disco | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec.

### <a name="linux-performance-data"></a>Dati sulle prestazioni di Linux

Di seguito sono riportati i dati sulle prestazioni del server Linux raccolti dall'appliance e inviati ad Azure.

**Dati** | **Linux** 
--- | --- 
Utilizzo di CPU | cat /proc/stat/| grep 'cpu' /proc/stat
Utilizzo della memoria | free \| grep Mem \| awk '{print $3/$2 * 100.0}'
Numero di schede di interfaccia di rete | lshw -class network \| grep eth[0-60] \| wc -l
Dati ricevuti per ogni scheda di interfaccia di rete | cat /sys/class/net/eth$nic/statistics/rx_bytes
Dati trasmessi per ogni scheda di interfaccia di rete | cat /sys/class/net/eth$nic/statistics/tx_bytes
Numero di dischi | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
Dettagli del disco | cat /proc/diskstats


## <a name="appliance-upgrades"></a>Aggiornamenti dell'appliance

L'appliance viene aggiornata quando vengono aggiornati gli agenti di Azure Migrate in esecuzione nell'appliance. Questa operazione viene eseguita in modo automatico, perché l'aggiornamento automatico è abilitato per impostazione predefinita nell'appliance. È possibile modificare questa impostazione predefinita per aggiornare manualmente i servizi dell'appliance.

### <a name="turn-off-auto-update"></a>Disattivare l'aggiornamento automatico

1. Nel computer che esegue l'appliance aprire l'Editor del Registro di sistema.
2. Passare a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Per disabilitare l'aggiornamento automatico, creare una chiave del Registro di sistema **AutoUpdate** impostando il valore DWORD su 0.

    ![Impostare la chiave del Registro di sistema](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Attivare l'aggiornamento automatico

È possibile attivare l'aggiornamento automatico usando uno di questi metodi:

- Eliminando la chiave del Registro di sistema AutoUpdate da HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance.
- Fare clic su **Visualizza servizi Appliance** dall'ultimo controllo aggiornamenti nel pannello **Configura prerequisiti** per attivare l'aggiornamento automatico.

Per eliminare la chiave del Registro di sistema:

1. Nel computer che esegue l'appliance aprire l'Editor del Registro di sistema.
2. Passare a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Eliminare la chiave del Registro di sistema **AutoUpdate**, creata in precedenza per disattivare l'aggiornamento automatico.

Per attivare l'aggiornamento automatico nel Configuration Manager dell'appliance, al termine dell'individuazione:

1. In Gestione configurazione Appliance passare a Configura il pannello dei **prerequisiti**
2. Nel controllo aggiornamenti più recenti fare clic su **Visualizza servizi Appliance** e fare clic sul collegamento per attivare l'aggiornamento automatico.

    ![Attivare l'aggiornamento automatico](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>Verificare la versione dei servizi dell'appliance

È possibile verificare la versione dei servizi dell'appliance usando uno di questi metodi:

- In Gestione configurazione Appliance passare a **Configura il** pannello dei prerequisiti.
- Nel **Pannello di controllo** > **Programmi e funzionalità** del computer dell'appliance.

Per archiviare Gestione configurazione Appliance:

1. In Gestione configurazione Appliance passare a Configura il pannello dei **prerequisiti**
2. Nel controllo aggiornamenti più recenti fare clic su **Visualizza servizi Appliance**.

    ![Controllare la versione](./media/migrate-appliance/versions.png)

Per verificare nel Pannello di controllo:

1. Nell'appliance fare clic su **Start** > **Pannello di controllo** > **Programmi e funzionalità**
2. Verificare le versioni dei servizi dell'appliance nell'elenco.

    ![Verificare la versione nel Pannello di controllo](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Aggiornare manualmente una versione precedente

Se si esegue una versione precedente per uno dei componenti, è necessario disinstallare il servizio ed eseguire manualmente l'aggiornamento alla versione più recente.

1. Per verificare le versioni del servizio dell'appliance più recenti, [scaricare](https://aka.ms/latestapplianceservices) il file LatestComponents.json.
2.    Dopo il download, aprire il file LatestComponents.json nel Blocco note.
3. Individuare la versione del servizio più recente nel file e il relativo collegamento di download. Ad esempio:

    "Nome": "ASRMigrationWebApp", "DownloadLink": "https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi", "Versione": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4.    Scaricare la versione più recente di un servizio obsoleto usando il collegamento di download nel file.
5. Dopo il download, eseguire il comando seguente in una finestra di comando dell'amministratore per verificare l'integrità del file MSI.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ``` Ad esempio:  C:\>CertUtil -HashFile C:\Utenti\public\download\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. Verificare che l'output del comando corrisponda alla voce del valore hash per il servizio nel file, ad esempio, al valore hash MD5 precedente.
6. Eseguire ora il file MSI per installare il servizio. Si tratta di un'installazione invisibile; al termine la finestra di installazione si chiude.
7. Al termine dell'installazione, verificare la versione del servizio nel **Pannello di controllo** > **Programmi e funzionalità**. La versione del servizio dovrebbe ora essere aggiornata alla versione più recente visualizzata nel file JSON.



## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come](how-to-set-up-appliance-vmware.md) configurare l'appliance per VMware.
- [Informazioni su come](how-to-set-up-appliance-hyper-v.md) configurare l'appliance per Hyper-V.
- [Informazioni su come](how-to-set-up-appliance-physical.md) configurare l'appliance per i server fisici.

