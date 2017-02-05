---
title: Prerequisiti per la distribuzione di Azure Site Recovery | Documentazione Microsoft
description: Questo articolo descrive i prerequisiti per la configurazione della replica con Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/04/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 92abadf68e4acbd9daae9e15dcfdce53563f6eb2
ms.openlocfilehash: a407f5ebe096db74820ff93354a636bc63894b1d

---

# <a name="site-recovery-deployment-prerequisites"></a>Prerequisiti di distribuzione di Site Recovery

Le organizzazioni necessitano di una strategia di continuità aziendale e ripristino di emergenza per determinare come app, carichi di lavoro e dati possano rimanere in esecuzione e disponibili durante i periodi di inattività, pianificati o meno, e come ripristinare le normali condizioni di lavoro il prima possibile. Il servizio Azure Site Recovery contribuisce alla strategia BCDR orchestrando la replica dei server fisici locali e delle macchine virtuali nel cloud (Azure) o in un data center secondario. In caso di interruzioni nella località primaria, verrà eseguito il failover alla località secondaria per mantenere disponibili app e carichi di lavoro. Quando la località primaria sarà di nuovo operativa, si tornerà a tale località. Per altre informazioni, vedere [Che cos'è Site Recovery?](site-recovery-overview.md)

Questo articolo riepiloga i prerequisiti di distribuzione per gli scenari di replica di Site Recovery.  

È possibile inserire commenti nella parte inferiore di questo articolo oppure porre domande tecniche nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="azure-deployment-models"></a>Modelli di distribuzione di Azure

Azure offre due [modelli di distribuzione](../azure-resource-manager/resource-manager-deployment-model.md) diversi per la creazione e l'uso delle risorse: il modello Azure Resource Manager e il modello di distribuzione classica. Sono anche disponibili il [portale di Azure classico](https://manage.windowsazure.com/), che supporta il modello di distribuzione classica, e il [portale di Azure](https://ms.portal.azure.com/), che supporta entrambi i modelli di distribuzione.

È consigliabile distribuire i nuovi scenari di Site Recovery nel [portale di Azure](https://portal.azure.com). Questo portale offre nuove funzionalità e un'esperienza di distribuzione migliorata. Nel portale classico è possibile gestire gli insiemi di credenziali, ma non crearne di nuovi.


## <a name="azure-account-requirements"></a>Requisiti dell'account di Azure

**Requisito** | **Dettagli**
--- | --- 
**Account di Azure** | Account [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi di Site Recovery.


## <a name="azure-storage-requirements"></a>Requisiti dell'archiviazione di Azure

I dati replicati vengono memorizzati in Archiviazione di Azure e le macchine virtuali di Azure vengono create quando si verifica il failover.

**Requisito** | **Dettagli**
--- | --- 
[Account di archiviazione di Azure](../storage/storage-introduction.md) | È possibile usare l'[archiviazione con ridondanza geografica](../storage/storage-redundancy.md#geo-redundant-storage) o l'archiviazione con ridondanza locale.<br/><br/> È consigliabile usare l'archiviazione con ridondanza geografica per una maggiore resilienza dei dati in caso di interruzione del servizio a livello di area o se non è possibile recuperare l'area primaria. [Altre informazioni](https://docs.microsoft.com/en-us/azure/storage/storage-redundancy)
**Portale di Azure** | Nel portale di Azure è possibile usare l'archiviazione di Resource Manager o un account di archiviazione classico.
**Archiviazione Premium** | [Archiviazione Premium](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage) è supportata se si replicano VM VMware o server fisici in Azure usando il portale di Azure.<br/><br/> Archiviazione Premium si usa in genere per le macchine virtuali che richiedono un livello di prestazioni di I/O costantemente elevato e bassa latenza per ospitare carichi di lavoro con numerose operazioni di I/O.<br/><br/> Se si usa Archiviazione Premium, sarà necessario anche un account di archiviazione standard per l'archiviazione dei log di replica in cui vengono acquisite le modifiche in corso ai dati locali.
**Limiti di archiviazione** | Nel portale classico è supportata solo l'archiviazione con ridondanza geografica.<br/><br/> Gli account di archiviazione creati nel portale di Azure non possono essere spostati tra gruppi di risorse.<br/><br/> La replica in account di archiviazione Premium non è attualmente supportata in India centrale e India meridionale.

## <a name="azure-network-requirements"></a>Requisiti di rete di Azure

È necessario configurare una rete di Azure a cui le VM di Azure si connetteranno quando vengono create dopo il failover.

**Requisito** | **Dettagli**
--- | ---
**Area della rete** | La rete deve trovarsi nella stessa area dell'insieme di credenziali.
**Portale di Azure** | Nel portale di Azure è possibile usare una rete Resource Manager o classica.
**Mapping di rete** | In caso di replica da VMM ad Azure, si configurerà il [mapping di rete](site-recovery-network-mapping.md) tra le reti di VM VMM e le reti di Azure in modo da garantire che le VM di Azure si connettano alle reti appropriate dopo il failover.


## <a name="vmware-replication-requirements-to-azure"></a>Requisiti della replica VMware (in Azure)

**Componente** | **Requisito**
--- | ---
**Server di configurazione** | Un computer fisico o una macchina virtuale locale che esegue Windows Server 2012 R2. Tutti i componenti locali di Site Recovery vengono installati in questo dispositivo.<br/><br/>Per la replica di macchine virtuali VMware è consigliabile distribuire il server come una macchina virtuale VMware a disponibilità elevata. <br/><br/>Se il server è una macchina virtuale VMware, il tipo di scheda di rete deve essere VMXNET3. Se si usa un diverso tipo di scheda di rete, installare un [aggiornamento VMware](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1) nel server vSphere 5.5.<br/><br/>Il server deve disporre di un indirizzo IP statico.<br/><br/>Il server non deve essere un controller di dominio.<br/><br/>Il nome host del server deve avere una lunghezza massima di 15 caratteri.<br/><br/>Il sistema operativo deve essere solo in lingua inglese.<br/><br/> Installare VMware vSphere PowerCLI 6.0 server.<br/><br/>Il server di configurazione richiede l'accesso a Internet. È necessario l'accesso in uscita con le caratteristiche seguenti:<br/><br/>L'accesso temporaneo sulla porta HTTP 80 durante l'installazione dei componenti di Site Recovery per scaricare MySQL<br/><br/>L'accesso in uscita in corso sulla porta HTTPS 443 per la gestione delle repliche<br/><br/>L'accesso in uscita in corso sulla porta HTTPS 9443 per il traffico di replica; questa porta può essere modificata<br/><br/> Consentire questo URL per il download di MySQL: ``http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi``<br/><br/> Il server deve inoltre avere accesso a [questi URL](#requirements-for-url-access)
**Server di elaborazione** | Installato per impostazione predefinita nel server di configurazione<br/><br/> Agisce come un gateway di replica. Riceve i dati di replica dai computer di origine protetti, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia all'archiviazione di Azure. Gestisce anche l'installazione push del servizio Mobility nei computer protetti ed esegue l'individuazione automatica delle VM VMware. Man mano che la distribuzione cresce, è possibile aggiungere altri server di elaborazione dedicati e distinti per gestire i volumi più elevati di traffico di replica.
**Server master di destinazione** | Installato per impostazione predefinita nel server di configurazione. Gestisce i dati di replica durante il failback da Azure.
**Host vSphere** | Uno o più hypervisor VMware vSphere.<br/><br/>Gli hypervisor devono eseguire vSphere versione 6.0, 5.5 o 5.1 con gli aggiornamenti più recenti.<br/><br/>È consigliabile che gli host vSphere e i server vCenter si trovino nella stessa rete del server di elaborazione. Si tratta della rete in cui si trova il server di configurazione, a meno di avere configurato un server di elaborazione dedicato. 
**Server vCenter** | È consigliabile distribuire un server VMware vCenter per gestire gli host vSphere. Deve eseguire vCenter versione 6.0 o 5.5 con gli aggiornamenti più recenti.<br/><br/>**Limitazione**: Site Recovery non supporta le nuove funzionalità di vCenter e vSphere 6.0 come cross-vCenter vMotion, Virtual Volumes e Storage DRS. Site Recovery supporta unicamente le funzionalità disponibili anche nella versione 5.5

### <a name="vmware-vm-requirements-to-azure"></a>Requisiti della VM VMware (per Azure)

**Componente** | **Requisito**
--- | ---
**VM VMware** | Nelle VM replicate devono essere installati e in esecuzione gli strumenti VMware.<br/><br/> Le VM devono essere conformi ai [prerequisiti di Azure](site-recovery-support-matrix.md#support-for-azure-vms) per la creazione di VM di Azure.<br/><br/>La capacità dei singoli dischi nei computer protetti non deve superare 1023 GB. Una macchina virtuale può avere fino a 64 dischi (quindi fino a 64 TB). <br/><br/>Minimo 2 GB di spazio disponibile sull'unità di installazione per l'installazione del componente.<br/><br/>**Limitazione**: la protezione delle VM con dischi crittografati non è supportata.<br/><br/>**Limitazione**: i cluster guest in dischi condivisi non sono supportati.<br/><br/>La **porta 20004** deve essere aperta nel firewall locale della VM se si desidera abilitare la coerenza fra più VM.<br/><br/>I computer con avvio Unified Extensible Firmware Interface (UEFI)/Extensible Firmware Interface(EFI) non sono supportati.<br/><br/>I nomi delle macchine devono contenere da 1 a 63 caratteri, ovvero lettere, numeri e trattini. Il nome deve iniziare e terminare con una lettera o un numero. È possibile modificare il nome di Azure dopo aver abilitato la replica per una macchina.<br/><br/>Se la VM di origine dispone del gruppo NIC, questo viene convertito in una singola scheda NIC dopo il failover in Azure.<br/><br/>Se le macchine virtuali protette dispongono di un disco iSCSI, Site Recovery converte il disco iSCSI delle VM protette in un file VHD quando la VM esegue il failover in Azure. Se la destinazione iSCSI può essere raggiunta dalla macchina virtuale di Azure, questa si connette a tale destinazione, rendendo essenzialmente visibili due dischi: il disco rigido virtuale nella macchina virtuale di Azure e il disco iSCSI di origine. In questo caso è necessario disconnettere la destinazione iSCSI visualizzata nella macchina virtuale di Azure.
**VM che eseguono Windows** | I computer Windows devono eseguire un sistema operativo a 64 bit [supportato](site-recovery-support-matrix.md#support-for-replicated-machines).<br/><br/> Il sistema operativo deve essere installato nell'unità C:\<br/><br/> e il disco del sistema operativo deve essere un disco di base di Windows, non un disco dinamico. Il disco dati può essere dinamico.<br/><br/> Site Recovery supporta VM con un disco RDM. Se la macchina virtuale di origine e il disco RDM sono disponibili, durante il failback Site Recovery riutilizza il disco RDM. Se non sono disponibili, durante il failback Site Recovery crea un nuovo file VMDK per ogni disco.
**VM che eseguono Linux** | I computer Linux devono eseguire un [sistema operativo supportato](site-recovery-support-matrix.md#support-for-replicated-machines).<br/><br/> I file /etc/hosts nei computer protetti devono contenere le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede di rete.<br/><br/> Nome host, punti di montaggio, nomi dispositivo e percorsi di sistema di Linux e nomi file (ad esempio /etc/, /usr) dovranno essere specificati solo con caratteri dell'alfabeto latino.<br/><br/> L'archiviazione deve essere [supportata](site-recovery-support-matrix.md#support-for-replicated-machines)<br/><br/> Site Recovery supporta VM con un disco RDM.  Durante il failback per Linux, Site Recovery non riutilizza il disco RDM, ma crea un nuovo file VMDK per ogni disco RDM corrispondente.<br/><br/> Assicurarsi di configurare l'impostazione disk.enableUUID=true nei parametri di configurazione della VM in VMware. Se la voce non esiste, crearla. È necessaria per fornire un valore UUID coerente al file VMDK in modo che venga installato correttamente. L'aggiunta di questa impostazione assicura anche che solo le modifiche differenziali vengano ritrasferite all'ambiente locale durante il failback invece di una replica completa.



## <a name="physical-servers-replication-requirements-to-azure"></a>Requisiti per la replica dei server fisici (in Azure)

**Componente** | **Requisito**
--- | ---
**Server di configurazione** | Un computer fisico o una macchina virtuale locale che esegue Windows Server 2012 R2. Tutti i componenti locali di Site Recovery vengono installati in questo dispositivo.<br/><br/> Per la replica di computer fisici si può usare un server fisico.<br/><br/>  Il server di configurazione richiede l'accesso a Internet. È necessario l'accesso in uscita con le caratteristiche seguenti:<br/><br/> L'accesso temporaneo sulla porta HTTP 80 durante l'installazione dei componenti di Site Recovery per scaricare MySQL<br/><br/>L'accesso in uscita in corso sulla porta HTTPS 443 per la gestione delle repliche<br/><br/>L'accesso in uscita in corso sulla porta HTTPS 9443 per il traffico di replica; questa porta può essere modificata<br/><br/> Consentire questo URL per il download di MySQL: ``http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi``<br/><br/> Il server deve inoltre avere accesso a [questi URL](#requirements-for-url-access)
**Failback** | Il failback da Azure viene sempre eseguito su VM VMware anche se è stato replicato un server fisico. Se non si distribuisce il server di configurazione come VM VMware, si dovrà configurare una VM VMware per ricevere il traffico di failback prima che venga eseguito il failback.

### <a name="physical-machine-requirements-to-azure"></a>Requisiti del computer fisico (in Azure)

**Componente** | **Requisito**
--- | ---
**Server fisici che eseguono Windows** | I computer Windows devono eseguire un sistema operativo a 64 bit [supportato](site-recovery-support-matrix.md#support-for-replicated-machines).<br/><br/> Il sistema operativo deve essere installato nell'unità C:\<br/><br/> e il disco del sistema operativo deve essere un disco di base di Windows, non un disco dinamico. Il disco dati può essere dinamico.<br/><br/> Site Recovery supporta VM con un disco RDM. Se la macchina virtuale di origine e il disco RDM sono disponibili, durante il failback Site Recovery riutilizza il disco RDM. Se non sono disponibili, durante il failback Site Recovery crea un nuovo file VMDK per ogni disco.
**Server fisici che eseguono Linux** | I computer Linux devono eseguire un [sistema operativo supportato](site-recovery-support-matrix.md#support-for-replicated-machines).<br/><br/> I file /etc/hosts nei computer protetti devono contenere le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede di rete.<br/><br/> Nome host, punti di montaggio, nomi dispositivo e percorsi di sistema di Linux e nomi file (ad esempio /etc/, /usr) dovranno essere specificati solo con caratteri dell'alfabeto latino.<br/><br/> L'archiviazione deve essere [supportata](site-recovery-support-matrix.md#support-for-replicated-machines).

## <a name="hyper-v-replication-requirements-to-azure"></a>Requisiti per la replica di Hyper-V (in Azure)

**Componente** | **Requisito**
--- | ---
**VMM (facoltativo)** | È possibile facoltativamente replicare le VM in host Hyper-V gestiti su cloud VMM.<br/><br/> Se non si usa VMM, raccogliere uno o più host o cluster Hyper-V in siti Hyper-V e configurare la replica per un sito.<br/><br/> Se si usa VMM, deve essere eseguito su System Center 2012 R2.<br/><br/> Se si usa VMM, ogni server VMM deve avere uno o più cloud configurati. Un cloud deve contenere uno o più gruppi host VMM e uno o più server o cluster host Hyper-V in ogni gruppo host.<br/><br/> Il server VMM deve essere connesso a Internet, con accesso agli [URL necessari](#requirements-for-url-access).<br/><br/> Se nel server VMM sono presenti regole firewall basate sull'indirizzo IP, verificare che consentano la comunicazione con Azure.<br/><br/> Consentire gli [intervalli IP del data center di Azure ](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta (443) HTTPS.<br/><br/> Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali.
**Host Hyper-V** | I server host Hyper-V devono eseguire almeno **Windows Server 2012 R2** con ruolo Hyper-V o **Microsoft Hyper-V Server 2012 R2** e disporre degli ultimi aggiornamenti installati.<br/><br/> Il server Hyper-V deve contenere una o più macchine virtuali.<br/><br/>I server Hyper-V devono essere connessi a Internet, in modo diretto o tramite proxy.<br/><br/>Nei server Hyper-V è necessario avere installato le correzioni specificate nell'articolo [2961977](https://support.microsoft.com/kb/2961977).<br/><br/>I server host Hyper-V richiedono l'accesso a Internet per la replica dei dati in Azure, incluso l'accesso agli [URL richiesti](#requirements-for-url-access). È necessario inoltre consentire gli [intervalli IP del data center di Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta HTTPS (443).<br/><br/> Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali.<br/><br/> Se gli host Hyper-V sono in cloud VMM, assicurarsi di installare la correzione descritta in [KB 2961977](https://support.microsoft.com/en-us/kb/2961977)

### <a name="hyper-v-vm-requirements-to-azure"></a>Requisiti della VM Hyper-V (per Azure)

**Componente** | **Requisito**
--- | ---
**Conformità della VM** |Prima eseguire il failover di una macchina virtuale verificare che il nome che verrà assegnato alla macchina virtuale di Azure sia conforme ai [prerequisiti di Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements). È possibile modificare il nome dopo aver abilitato la replica per la macchina virtuale.
**Disco** | La capacità dei singoli dischi nei computer protetti non deve superare 1023 GB. Una macchina virtuale può avere fino a 64 dischi (quindi fino a 64 TB).<br/><br/> I cluster guest in dischi condivisi non sono supportati.
**UEFI** | L'avvio in modalità UEFI (Unified Extensible Firmware Interface)/EFI (Extensible Firmware Interface) non è supportato.
**Gruppo NIC** | Se la VM di origine dispone del gruppo NIC, questo viene convertito in una singola scheda NIC dopo il failover in Azure.
**Linux statico** | La protezione di macchine virtuali Hyper-V che eseguono Linux con un indirizzo IP statico non è supportata.



## <a name="vmwarephysical-servers-replication-requirements-secondary-site"></a>Requisiti per la replica dei server VMware/fisici (sito secondario)

Questo scenario viene gestito dal prodotto InMage, che può essere scaricato dall'insieme di credenziali di Site Recovery quando si sceglie di replicare VM VMware o server fisici in un sito secondario. Si noti che questo scenario non è disponibile nel portale di Azure.

**Componente** | **Requisito**
--- | ---
**Sito primario** | È necessario che un server di elaborazione gestisca la memorizzazione nella cache, la compressione e l'ottimizzazione dei dati. Questo server gestisce anche l'installazione push dell'agente unificato necessario in ogni computer che si desidera replicare.<br/><br/> Se si desidera replicare VM VMware sono necessari uno o più server VMware ESX/ESXi e, facoltativamente, un server VMware vCenter.
**Sito secondario** | È necessario un server di configurazione nel sito secondario per configurare e gestire l'ambiente.<br/><br/> Un server di destinazione master è installato nel sito secondario per contenere i dati replicati.<br/><br/> Un server vContinuum è installato per impostazione predefinita nel server di destinazione master e fornisce una console per gestire e monitorare l'ambiente.

### <a name="vmware-vmphysical-machine-requirements-secondary-site"></a>Requisiti della VM VMware/computer fisico (sito secondario)

Verificare i requisiti nella [matrice di supporto di InMage](https://aka.ms/asr-scout-cm).


## <a name="hyper-v-replication-requirements-secondary-site"></a>Requisiti per la replica di Hyper-V (sito secondario)

**Componente** | **Requisito**
--- | ---
**VMM** | Per replicare VM Hyper-V in un sito secondario, l'host Hyper-V deve essere gestito in cloud System Center VMM. <br/><br/> VMM deve eseguire almeno System Center 2012 SP1 con gli aggiornamenti più recenti.<br/><br/> Si consiglia di avere un server VMM nel sito primario e un altro server VMM nel sito secondario. È possibile eseguire la replica tra [cloud diversi sullo stesso server VMM](https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-single-vmm), ma in questo scenario saranno necessarie alcune operazioni di configurazione manuale.<br/><br/> Il server VMM deve essere connesso a Internet, con accesso agli [URL necessari](#requirements-for-url-access).<br/><br/> Se nel server VMM sono presenti regole firewall basate sull'indirizzo IP, verificare che consentano la comunicazione con Azure.<br/><br/> Consentire gli [intervalli IP del data center di Azure ](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta (443) HTTPS.<br/><br/> Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali.
**Hyper-V** | I server Hyper-V devono eseguire almeno Windows Server 2012 con ruolo Hyper-V e con gli ultimi aggiornamenti installati.<br/><br/> Il server Hyper-V deve contenere una o più VM<br/><br/> Gli host Hyper-V devono trovarsi nei gruppi host disponibili nei server VMM primario e secondario.<br/><br/> Se si esegue Hyper-V in un cluster in Windows Server 2012 R2 è necessario installare l'aggiornamento [2961977](https://support.microsoft.com/kb/2961977). Se si ha un cluster Hyper-V basato su indirizzi IP statici in Windows Server 2012, il broker del cluster non viene creato automaticamente se il cluster è basato su indirizzi IP statici. [Altre informazioni](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx) sulla configurazione manuale.

### <a name="hyper-v-vm-requirements-secondary-site"></a>Requisiti per la VM Hyper-V (sito secondario)

**Componente** | **Dettagli**
--- | ---
**Cloud VMM** | Le VM devono trovarsi in host Hyper-V su cloud VMM.






## <a name="url-access-requirements"></a>Requisiti di accesso agli URL

Gli URL seguenti dovranno essere disponibili dai server host Hyper-V, VMware e VMM.

**URL** | **Da VMM a VMM** | **Da VMM ad Azure** | **Da Hyper-V ad Azure** | **Da VMware ad Azure** 
--- | --- | --- | --- | ---
``*.accesscontrol.windows.net`` | Consenti | Consenti | Consenti | Consenti
``*.backup.windowsazure.com`` | Facoltativo | Consenti | Consenti | Consenti
``*.hypervrecoverymanager.windowsazure.com`` | Consenti | Consenti | Consenti | Consenti
``*.store.core.windows.net`` | Consenti | Consenti | Consenti | Consenti
``*.blob.core.windows.net`` | Facoltativo | Consenti | Consenti | Consenti
``https://www.msftncsi.com/ncsi.txt`` | Consenti | Consenti | Consenti | Consenti
``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | Facoltativo | Facoltativo | Facoltativo | Consentire download SQL
``time.windows.com`` | Consenti | Consenti | Consenti | Consenti
``time.nist.gov`` | Consenti | Consenti | Consenti | Consenti

## <a name="failover-requirements"></a>Requisiti di failover

**Scenario** | **Test** | **Pianificato** | **Non pianificato**
--- | --- | --- | ---
Da VMware ad Azure | Supportato | Non supportato | Supportato
Da fisico ad Azure | Supportato | Non supportato | Supportato
Da Hyper-V (VMM) ad Azure | Supportato | Supportato | Supportato
Da Hyper-V (non VMM) ad Azure | Supportato | Supportato | Non supportato
Da Hyper-V (VMM/cloud primario) a secondario | Supportato | Supportato | Supportato

## <a name="failback-requirements"></a>Requisiti di failback

**Scenario** | **Test** | **Pianificato** | **Non pianificato**
--- | --- | --- | ---
**Da Azure a VMware** | Non supportato | Non supportato | Supportato
**Da Azure a fisico** | Non supportato | Non supportato | Supportato solo per VMware
**Da Azure a VMM** | Non supportato | Supportato | Non supportato
**Da Azure ad Hyper-V (non VMM)** | Non supportato | Supportato | Non supportato
**Da Hyper-V (VMM/cloud secondario) a primario** | Supportato | Supportato | Supportato

### <a name="failback-from-azure-to-vmware-vms"></a>Failback da Azure a VM VMware

**Componente** | **Dettagli**
--- | ---
**Server di elaborazione temporaneo in Azure** | Se si vuole eseguire il failback da Azure dopo il failover, è necessario impostare una VM di Azure configurata come server di elaborazione per gestire la replica da Azure. Questa VM può essere eliminata al termine del failback.
**Connessione VPN** | Per eseguire il failback è necessaria una connessione VPN, oppure Azure ExpressRoute, configurata dalla rete di Azure al sito locale.
**Server di destinazione master locale separato** | Il server di destinazione master locale gestisce il failback. Il server di destinazione master viene installato per impostazione predefinita nel server di gestione, ma se si esegue il failback di volumi di traffico più grandi, è consigliabile configurare un server di destinazione master locale distinto a questo scopo.



### <a name="failback-from-azure-to-physical-machines"></a>Failback da Azure a computer fisici

**Componente** | **Dettagli**
--- | ---
**Failback da computer fisico a computer fisico** | Questa funzionalità non è supportata. Se si esegue il failover di server fisici in Azure e quindi si vuole eseguire il failback, sarà necessario eseguire il failback in una macchina virtuale VMware. Non è possibile eseguire il failback a un server fisico.
**VM** | È necessaria una VM VMware locale su cui eseguire il failback.
**Server di configurazione** | Se non è stato distribuito il server di configurazione locale come VM VMware, si deve configurare un server di destinazione master separato come VM VMware. Questa operazione è necessaria perché il server di destinazione master interagisce e si collega con le risorse di archiviazione VMware per ripristinare i dischi in una macchina virtuale VMware.



## <a name="deployment-optimization"></a>Ottimizzazione della distribuzione

Usare i suggerimenti seguenti per ottimizzare e ridimensionare la distribuzione.

- **Dimensioni del volume del sistema operativo**: quando si replica una macchina virtuale in Azure, il volume del sistema operativo deve essere inferiore a 1 TB. Se si dispone di volumi superiori, è possibile spostarli manualmente in un altro disco prima di iniziare la distribuzione.
- **Dimensioni dei dischi dati**: se si esegue la replica in Azure è possibile avere fino a 64 dischi dati in una macchina virtuale, ognuno con un massimo di 1 TB. È possibile eseguire in modo efficiente la replica e il failover di una macchina virtuale di circa 64 TB.
- **Limiti del piano di ripristino**: Site Recovery è in grado di supportare migliaia di macchine virtuali. I piani di ripristino sono progettati come modello per le applicazioni devono eseguire il failover insieme. Pertanto, il numero di macchine in un piano di ripristino viene limitato a 50.
- **Limiti dei servizi di Azure**: ogni sottoscrizione di Azure include un set di limiti predefiniti su core, servizi cloud e così via. È consigliabile eseguire un failover di test per convalidare la disponibilità di risorse nella sottoscrizione. È possibile modificare questi limiti tramite il supporto di Azure.
- **Pianificazione della capacità**: altre informazioni sulla [pianificazione della capacità](site-recovery-capacity-planner.md) per Site Recovery.
- **Larghezza di banda della replica**: in caso di larghezza di banda insufficiente, è opportuno notare quanto indicato di seguito.
- **ExpressRoute**: Site Recovery funziona con Azure ExpressRoute e con gli ottimizzatori WAN, ad esempio Riverbed. [Ulteriori informazioni](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) su ExpressRoute.
- **Traffico di replica**: Site Recovery esegue una replica iniziale intelligente utilizzando solo i blocchi di dati, non l'intero VHD. Solo le modifiche vengono replicate durante la replica in corso.
- **Traffico di rete**: è possibile controllare il traffico di rete utilizzato per la replica configurando [QoS di Windows](https://technet.microsoft.com/library/hh967468.aspx) con un criterio basato sull'indirizzo IP di destinazione e la porta.  Inoltre, se si esegue la replica ad Azure Site Recovery usando l'agente di backup di Azure è possibile configurare la limitazione per l'agente. [Altre informazioni](https://support.microsoft.com/kb/3056159).
- **RTO**: per misurare l'obiettivo del tempo di ripristino (RTO) che è possibile prevedere con Site Recovery, è consigliabile eseguire un failover di test e visualizzare i processi di Site Recovery per analizzare il tempo necessario per completare le operazioni. Se si sta eseguendo il failover ad Azure, per l'obiettivo RTO ottimale è consigliabile automatizzare tutte le azioni manuali mediante l'integrazione con i piani di automazione e di ripristino di Azure.
- **RPO**: Site Recovery supporta un obiettivo del punto di ripristino (RPO) quasi sincrono quando si esegue la replica ad Azure. Ciò presuppone sufficiente larghezza di banda tra il datacenter e Azure.



## <a name="next-steps"></a>Passaggi successivi
Dopo aver esaminato i requisiti di distribuzione generali, vedere i prerequisiti dettagliati e distribuire uno scenario.

* [Replicare le macchine virtuali VMWare locali in Azure](site-recovery-vmware-to-azure.md)
* [Replicare i server fisici in Azure](site-recovery-vmware-to-azure.md)
* [Replicare il server Hyper-V in cloud VMM in Azure](site-recovery-vmm-to-azure.md)
* [Replicare le macchine virtuali Hyper-V (senza VMM) in Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replicare le VM Hyper-V in un sito secondario](site-recovery-vmm-to-vmm.md)
* [Replicare le VM Hyper-V in un sito secondario con SAN](site-recovery-vmm-san.md)
* [Replicare le VM Hyper-V con un singolo server VMM](site-recovery-single-vmm.md)



<!--HONumber=Dec16_HO1-->


