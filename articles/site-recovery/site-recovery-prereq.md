---
title: Prerequisiti per la replica in Azure con Azure Site Recovery | Microsoft Docs
description: Informazioni sui prerequisiti per la replica di macchine virtuali e computer fisici in Azure tramite il servizio Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/23/2017
ms.author: rajanaki
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: ccc2b53e0824042c0f07b9fe63e8777aa68c6dc1
ms.contentlocale: it-it
ms.lasthandoff: 08/30/2017

---

#  <a name="prerequisites-for-replication-from-on-premises-to-azure-by-using-site-recovery"></a>Prerequisiti per la replica da locale ad Azure con Azure Site Recovery

> [!div class="op_single_selector"]
> * [Eseguire la replica da Azure ad Azure](site-recovery-azure-to-azure-prereq.md)
> * [Eseguire la replica da locale ad Azure](site-recovery-prereq.md)

Azure Site Recovery può essere utile a supporto dell'attuazione della strategia di continuità aziendale e ripristino di emergenza orchestrando la replica delle macchine virtuali di Azure in un'altra area di Azure. Site Recovery supporta anche la replica delle macchine virtuali e dei server fisici locali nel cloud (Azure) o in un data center secondario. In caso di interruzioni nella località primaria, è possibile eseguire il failover a una località secondaria per mantenere disponibili app e carichi di lavoro. Sarà possibile tornare alla località primaria non appena di nuovo operativa. Per altre informazioni su Site Recovery, vedere [Che cos'è Site Recovery?](site-recovery-overview.md).

In questo articolo sono riepilogati i prerequisiti per iniziare la replica di Site Recovery da un computer locale in Azure.

È possibile pubblicare qualsiasi commento nella parte inferiore di questo articolo. Si possono anche porre domande tecniche nel [forum di Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="azure-requirements"></a>Requisiti di Azure

**Requisito** | **Dettagli**
--- | ---
**Account di Azure** | [Account Microsoft Azure](http://azure.microsoft.com/). È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
**Servizio Site Recovery** | Per altre informazioni sui prezzi per il servizio Azure Site Recovery, vedere [Prezzi di Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/). |
**Archiviazione di Azure** | Per archiviare i dati replicati è necessario un account di Archiviazione di Azure. L'account di archiviazione deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino di Azure. In caso di failover, vengono create macchine virtuali di Azure.<br/><br/> A seconda del modello di risorsa da usare per il failover delle macchine virtuali di Azure, è possibile configurare un account con il [modello di distribuzione Azure Resource Manager](../storage/common/storage-create-storage-account.md) o con il [modello di distribuzione classica](../storage/common/storage-create-storage-account.md).<br/><br/>È possibile usare l'[archiviazione con ridondanza geografica](../storage/common/storage-redundancy.md#geo-redundant-storage) o locale. È consigliabile usare l'archiviazione con ridondanza geografica, che permette di garantire una maggiore resilienza dei dati in caso di interruzione del servizio a livello di area o se non è possibile ripristinare l'area primaria.<br/><br/> È possibile usare un account standard di Archiviazione di Azure oppure l'archiviazione Premium di Azure. L'[archiviazione Premium](https://docs.microsoft.com/azure/storage/storage-premium-storage) viene in genere usata per le macchine virtuali che richiedono un livello di prestazioni di I/O costantemente elevato e bassa latenza. Con l'archiviazione Premium una macchina virtuale può ospitare carichi di lavoro con uso intensivo di I/O. Se si usa l'archiviazione Premium per i dati replicati, è necessario anche un account di archiviazione standard. Quest'ultimo permette di archiviare i log di replica in cui vengono acquisite le modifiche in corso ai dati locali.<br/><br/>
**Limiti di archiviazione** | Non è possibile spostare gli account di archiviazione usati in Site Recovery in un gruppo di risorse diverso oppure spostarli o usarli con un'altra sottoscrizione.<br/><br/> La replica in account di archiviazione Premium non è attualmente disponibile in India centrale e India meridionale.
**Rete di Azure** | È necessaria una rete di Azure a cui le macchine virtuali di Azure possano connettersi dopo il failover. La rete di Azure deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino.<br/><br/> Nel portale di Azure è possibile creare una rete di Azure tramite il [modello di distribuzione Resource Manager](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) o il [modello di distribuzione classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).<br/><br/> Se si esegue la replica da System Center Virtual Machine Manager (VMM) in Azure, è necessario configurare il mapping di rete tra le reti VM di VMM e le reti di Azure. In questo modo si garantisce che le macchine virtuali di Azure si connettano alle reti appropriate dopo il failover.
**Limitazioni di rete** | Non è possibile spostare gli account di rete usati in Site Recovery in un gruppo di risorse diverso oppure spostarli o usarli con un'altra sottoscrizione.
**Mapping di rete** | Se si esegue la replica di macchine virtuali di Microsoft Hyper-V nei cloud VMM, è necessario configurare il mapping di rete. In questo modo si garantisce che le macchine virtuali di Azure si connettano alle reti appropriate quando vengono create dopo il failover.

> [!NOTE]
> Nelle sezioni seguenti vengono descritti i prerequisiti per i vari componenti dell'ambiente del cliente. Per altre informazioni sul supporto per configurazioni specifiche, vedere la [matrice di supporto](site-recovery-support-matrix.md).
>

## <a name="disaster-recovery-of-vmware-vms-or-physical-windows-or-linux-servers-to-azure"></a>Ripristino di emergenza in Azure di macchine virtuali VMware o server Windows/Linux fisici
I componenti seguenti sono necessari per il ripristino di emergenza di macchine virtuali VMware o di server fisici Windows o Linux. Si tratta di componenti aggiuntivi rispetto a quelli descritti in [Requisiti Azure](#azure-requirements).


### <a name="configuration-server-or-additional-process-server"></a>Server di configurazione o server di elaborazione aggiuntivo

Configurare un computer locale come server di configurazione per orchestrare le comunicazioni tra il sito locale e Azure. Il computer locale gestisce anche la replica dei dati. <br/></br>

*   **Prerequisiti dell'host VMware vCenter o vSphere**

    | **Componente** | **Requisiti** |
    | --- | --- |
    | **vSphere** | Sono necessari uno o più hypervisor VMware vSphere.<br/><br/>Gli hypervisor devono eseguire vSphere versione 6.0, 5.5 o 5.1 con gli aggiornamenti più recenti.<br/><br/>È consigliabile che gli host vSphere e i server vCenter siano entrambi nella stessa rete del server di elaborazione. Se non è stato configurato un server di elaborazione dedicato, si tratta della rete in cui si trova il server di configurazione. |
    | **vCenter** | È consigliabile distribuire un server VMware vCenter per gestire gli host vSphere. Deve eseguire vCenter versione 6.0 o 5.5 con gli aggiornamenti più recenti.<br/><br/>**Limitazione**: Site Recovery non supporta la replica tra istanze di vCenter vMotion. Neanche l'archiviazione DRS e l'archiviazione vMotion sono supportate nella macchina virtuale di destinazione master in seguito a un'operazione di riprotezione.||

* **Prerequisiti dei computer replicati**

    | **Componente** | **Requisiti** |
    | --- | --- |
    | **Macchine virtuali locali** (macchine virtuali di VMware) | Nelle macchine virtuali replicate devono essere installati e in esecuzione gli strumenti VMware.<br/><br/> Le macchine virtuali devono soddisfare i [prerequisiti di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) per la creazione di una macchina virtuale di Azure.<br/><br/>La capacità del disco in ogni macchina protetta non può essere più di 1.023 GB. <br/><br/>Minimo 2 GB di spazio disponibile sull'unità di installazione richiesto per l'installazione del componente.<br/><br/>Se si vuole abilitare la coerenza fra più macchine virtuali, la porta 20004 deve essere aperta nel firewall locale della macchina virtuale.<br/><br/>I nomi delle macchine devono avere una lunghezza da 1 a 63 caratteri e si possono usare lettere, numeri e trattini. Il nome deve iniziare e terminare con una lettera o un numero. <br/><br/>È possibile modificare il nome di Azure dopo aver abilitato la replica per una macchina.<br/><br/> |
    | **Computer Windows (fisico o VMware)** | La macchina deve eseguire uno dei seguenti sistemi operativi a 64 bit supportati: <br/>- Windows Server 2012 R2<br/>- Windows Server 2012<br/>- Windows Server 2008 R2 con SP1 o versione successiva<br/><br/> Il sistema operativo deve essere installato nell'unità C e il disco del sistema operativo deve essere un disco di base di Windows, non un disco dinamico. Il disco dati può essere dinamico.<br/><br/>|
    | **Computer Linux** (fisico o VMware) | La macchina deve eseguire uno dei seguenti sistemi operativi a 64 bit supportati: <br/>- Red Hat Enterprise Linux da 5.2 a 5.11, da 6.1 a 6.9, da 7.0 a 7.3<br/>- CentOS: da 5.2 a 5.11, da 6.1 a 6.9, da 7.0 a 7.3<br/>- Server Ubuntu 14.04 LTS (per un elenco delle versioni supportate del kernel per Ubuntu, vedere i [sistemi operativi supportati](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions))<br/>- Server Ubuntu 16.04 LTS (per un elenco delle versioni supportate del kernel per Ubuntu, vedere i [sistemi operativi supportati](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions))<br/>- Debian 7 o Debian 8<br/>- Oracle Enterprise Linux 6.5 o 6.4 che esegue il kernel compatibile Red Hat o Unbreakable Enterprise Kernel versione 3 (UEK3)<br/>- SUSE Linux Enterprise Server 11 SP4 o SUSE Linux Enterprise Server 11 SP3<br/><br/>I file /etc/hosts nei computer protetti devono contenere le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede di rete.<br/><br/>Dopo il failover, se si vuole connettersi a una macchina virtuale di Azure che esegue Linux tramite un client Secure Shell (SSH), assicurarsi che il servizio SSH nella macchina virtuale protetta sia impostato per l'avvio automatico all'avvio del sistema. Verificare inoltre che le regole del firewall consentano una connessione SSH alla macchina virtuale protetta.<br/><br/>Il nome host, i punti di montaggio, i nomi di dispositivo, i percorsi di sistema di Linux e i nomi di file (ad esempio /etc/ e /usr) dovranno essere specificati solo con caratteri dell'alfabeto latino.<br/><br/>Le seguenti directory (se impostate come partizioni o file system separati) devono essere tutte nello stesso disco (il disco del sistema operativo) nel server di origine:<br/>- / (root)<br/>- /boot<br/>- /usr<br/>- /usr/local<br/>- /var<br/>- /etc<br/><br/>Attualmente, le funzionalità di XFS v5, come il checksum dei metadati, non sono supportate da Site Recovery nei file system XFS. Assicurarsi che i file system XFS non usino alcuna funzionalità v5. È possibile usare l'utilità xfs_info per controllare il superblocco XFS per la partizione. Se **ftype** è impostato su **1**, vengono usate le funzionalità XFS v5.<br/><br/>Nei server Red Hat Enterprise Linux 7 e CentOS 7 l'utilità lsof deve essere installata e disponibile.<br/><br/>


## <a name="disaster-recovery-of-hyper-v-vms-to-azure-no-vmm"></a>Ripristino di emergenza di macchine virtuali Hyper-V in Azure (non VMM)

Per il ripristino di emergenza delle macchine virtuali Hyper-V nei cloud VMM sono necessari i componenti seguenti. Si tratta di componenti aggiuntivi rispetto a quelli descritti in [Requisiti Azure](#azure-requirements).

| **Prerequisito** | **Dettagli** |
| --- | --- |
| **Host Hyper-V** |Uno o più server locali in cui è in esecuzione Windows Server 2012 R2 con tutti gli aggiornamenti più recenti e il ruolo Hyper-V abilitato o Microsoft Hyper-V Server 2012 R2.<br/><br/>I server Hyper-V devono contenere una o più macchine virtuali.<br/><br/>I server Hyper-V devono essere connessi a Internet, in modo diretto o tramite proxy.<br/><br/>Nei server Hyper-V devono essere installate le correzioni descritte nell'articolo della Knowledge Base [2961977](https://support.microsoft.com/kb/2961977).
|**Provider e agente**| Durante la distribuzione di Site Recovery viene installato il provider di Azure Site Recovery. L'installazione del provider include anche l'installazione dell'agente di Servizi di ripristino di Azure in ogni server Hyper-V che esegue le macchine virtuali da proteggere. <br/><br/>In tutti i server Hyper-V in un insieme di credenziali di Site Recovery devono essere installate le stesse versioni del provider e dell'agente.<br/><br/>Il provider dovrà connettersi a Site Recovery tramite Internet. Il traffico può essere inviato direttamente oppure tramite un proxy. Si noti che il proxy basato su HTTPS non è supportato. Il server proxy deve consentire l'accesso agli URL seguenti:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/>Se nel server sono disponibili regole firewall basate sull'indirizzo IP, assicurarsi che le regole consentano la comunicazione con Azure.<br/><br/> Consentire gli [intervalli IP del data center di Azure ](https://www.microsoft.com/download/confirmation.aspx?id=41653) e HTTPS (porta 443).<br/><br/> Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (usati per il controllo di accesso e la gestione delle identità).


## <a name="disaster-recovery-of-hyper-v-vms-in-vmm-clouds-to-azure"></a>Ripristino di emergenza in Azure di macchine virtuali Hyper-V in cloud VMM

Per il ripristino di emergenza delle macchine virtuali Hyper-V nei cloud VMM sono necessari i componenti seguenti. Si tratta di componenti aggiuntivi rispetto a quelli descritti in [Requisiti Azure](#azure-requirements).

| **Prerequisito** | **Dettagli** |
| --- | --- |
| **Virtual Machine Manager** |Devono essere disponibili uno o più server VMM in esecuzione su System Center 2012 R2 o versione successiva. Ogni server VMM deve essere configurato con uno o più cloud. <br/><br/>Un cloud deve includere:<br/>- Uno o più gruppi host VMM.<br/>- Uno o più cluster o server host Hyper-V in ogni gruppo host.<br/><br/>Per altre informazioni sulla configurazione di cloud VMM, vedere [How to Create a Cloud in VMM 2012](http://social.technet.microsoft.com/wiki/contents/articles/2729.how-to-create-a-cloud-in-vmm-2012.aspx) (Come creare un cloud in VMM 2012). |
| **Hyper-V** |I server host Hyper-V devono eseguire almeno Windows Server 2012 R2 con il ruolo Hyper-V abilitato o Microsoft Hyper-V Server 2012 R2, con gli ultimi aggiornamenti installati.<br/><br/> Un server Hyper-V deve contenere una o più macchine virtuali.<br/><br/> Il server host Hyper-V o il cluster che include le macchine virtuali da replicare deve essere gestito in un cloud VMM.<br/><br/>I server Hyper-V devono essere connessi a Internet, in modo diretto o tramite proxy.<br/><br/>Nei server Hyper-V devono essere installate le correzioni descritte nell'articolo della Knowledge Base [2961977](https://support.microsoft.com/kb/2961977).<br/><br/>I server host Hyper-V richiedono l'accesso a Internet per la replica dei dati in Azure. |
| **Provider e agente** |Durante la distribuzione di Azure Site Recovery, installare il provider di Azure Site Recovery nel server VMM. Installare l'agente di Servizi di ripristino negli host Hyper-V. Il provider e l'agente devono connettersi ad Azure direttamente tramite Internet o attraverso un proxy. Un proxy basato su HTTPS non è supportato. Il server proxy nel server VMM e gli host Hyper-V devono poter accedere a: <br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>Se nel server VMM sono disponibili regole del firewall basate sull'indirizzo IP, assicurarsi che le regole consentano la comunicazione con Azure.<br/><br/> Consentire gli [intervalli IP del data center di Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta HTTPS (443).<br/><br/>Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (usati per il controllo di accesso e la gestione delle identità).<br/><br/> |

### <a name="replicated-machine-prerequisites"></a>Prerequisiti dei computer replicati

| **Componente** | **Dettagli** |
| --- | --- |
| **Macchine virtuali protette** | Site Recovery supporta tutti i sistemi operativi supportati da [Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).<br/><br/>Le macchine virtuali devono soddisfare i [prerequisiti di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) per la creazione di una macchina virtuale di Azure. I nomi delle macchine devono avere una lunghezza da 1 a 63 caratteri e si possono usare lettere, numeri e trattini. Il nome deve iniziare e terminare con una lettera o un numero. <br/><br/>È possibile modificare il nome della macchina virtuale dopo aver abilitato la replica per la macchina virtuale. <br/><br/> La capacità del disco per ogni macchina protetta non può essere più di 1.023 GB. Una macchina virtuale può avere fino a 16 dischi (fino a 16 TB).<br/><br/>


## <a name="disaster-recovery-of-hyper-v-vms-in-vmm-clouds-to-a-customer-owned-site"></a>Ripristino di emergenza di macchine virtuali Hyper-V in cloud VMM in un sito di proprietà del cliente

Per il ripristino di emergenza delle macchine virtuali Hyper-V in cloud VMM in un sito di proprietà del cliente sono necessari i componenti seguenti. Si tratta di componenti aggiuntivi rispetto a quelli descritti in [Requisiti Azure](#azure-requirements).

| **Componente** | **Dettagli** |
| --- | --- |
| **Virtual Machine Manager** |  È consigliabile eseguire la distribuzione di un server VMM sia nel sito primario che nel sito secondario.<br/><br/> È possibile [eseguire la replica tra cloud in un unico server VMM](site-recovery-vmm-to-vmm.md#prepare-for-single-server-deployment). Per eseguire la replica tra cloud in un singolo server VMM, sono necessari almeno due cloud configurati nel server VMM.<br/><br/> I server VMM devono eseguire almeno System Center 2012 SP1 con gli aggiornamenti più recenti.<br/><br/> Ogni server VMM deve avere uno o più cloud. In tutti i cloud deve essere impostato il profilo della capacità Hyper-V. <br/><br/>I cloud devono contenere uno o più gruppi host VMM. Per altre informazioni sulla configurazione di cloud VMM, vedere [Preparare la distribuzione di Azure Site Recovery](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric). |
| **Hyper-V** | I server Hyper-V devono eseguire almeno Windows Server 2012 con il ruolo Hyper-V abilitato e con gli ultimi aggiornamenti installati.<br/><br/> Un server Hyper-V deve contenere una o più macchine virtuali.<br/><br/>  I server host Hyper-V devono trovarsi nei gruppi host disponibili nei cloud VMM primario e secondario.<br/><br/> Se si esegue Hyper-V in un cluster in Windows Server 2012 R2, si consiglia di installare l'aggiornamento descritto nell'articolo della Knowledge Base [2961977](https://support.microsoft.com/kb/2961977).<br/><br/> Se si esegue Hyper-V in un cluster basato su indirizzi IP statici in Windows Server 2012, non viene creato automaticamente un gestore cluster. È necessario configurare manualmente il gestore cluster. Per altre informazioni sul gestore cluster, vedere [Configure Replica Broker Role (Cluster to Cluster Replication)](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx) (Configurare il ruolo di gestore cluster per la replica da cluster a cluster). |
| **Provider** | Durante la distribuzione di Site Recovery, installare il provider di Azure Site Recovery nei server VMM. Il provider comunica con Site Recovery su HTTPS (porta 443) per orchestrare la replica. La replica dei dati viene eseguita tra il server Hyper-V primario e quello secondario sulla rete LAN o tramite una connessione VPN.<br/><br/> Il provider in esecuzione nel server VMM deve poter accedere agli URL seguenti:<br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>Il provider di Site Recovery deve consentire la comunicazione del firewall dai server VMM agli [intervalli IP dei data center di Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e il protocollo HTTPS (porta 443). |


## <a name="url-access"></a>Accesso a URL
Gli URL seguenti dovranno essere disponibili dai server host Hyper-V, VMware e VMM:

|**URL** | **Da VMM a VMM** | **Da VMM ad Azure** | **Da Hyper-V ad Azure** | **Da VMware ad Azure** |
|--- | --- | --- | --- | --- |
|``*.accesscontrol.windows.net`` | Consenti | Consenti | Consenti | Consenti |
|``*.backup.windowsazure.com`` | Facoltativo | Consenti | Consenti | Consenti |
|``*.hypervrecoverymanager.windowsazure.com`` | Consenti | Consenti | Consenti | Consenti |
|``*.store.core.windows.net`` | Consenti | Consenti | Consenti | Consenti |
|``*.blob.core.windows.net`` | Facoltativo | Consenti | Consenti | Consenti |
|``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | Facoltativo | Facoltativo | Facoltativo | Consentire download SQL |
|``time.windows.com`` | Consenti | Consenti | Consenti | Consenti|
|``time.nist.gov`` | Consenti | Consenti | Consenti | CONSENTI |

