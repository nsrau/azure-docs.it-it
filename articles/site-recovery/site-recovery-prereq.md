---
title: Prerequisiti per la replica in Azure con Azure Site Recovery | Microsoft Docs
description: In questo articolo vengono riepilogati i prerequisiti per la replica di macchine virtuali e computer fisici in Azure tramite il servizio Azure Site Recovery.
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
ms.date: 12/11/2016
ms.author: rajanaki
translationtype: Human Translation
ms.sourcegitcommit: 46ea97f5c13b7befc78466fae92a737107be8f83
ms.openlocfilehash: 31cae0cf625552c0e9547d0e514c6017c6a179ef

---

#  <a name="prerequisites-for-replication-to-azure-using-azure-site-recovery"></a>Prerequisiti per la replica in Azure con Azure Site Recovery

 Il servizio Azure Site Recovery contribuisce alla strategia BCDR orchestrando la replica dei server fisici locali e delle macchine virtuali nel cloud (Azure) o in un data center secondario. In caso di interruzioni nella località primaria, è possibile eseguire il failover a una località secondaria per mantenere disponibili app e carichi di lavoro. Sarà possibile tornare alla località primaria quando sarà di nuovo operativa. Per altre informazioni, vedere [Che cos'è Site Recovery?](site-recovery-overview.md)

In questo articolo vengono riepilogati i prerequisiti necessari per avviare la replica di Site Recovery in Azure.

È possibile inserire commenti nella parte inferiore di questo articolo oppure porre domande tecniche nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="azure-requirements"></a>Requisiti di Azure

**Requisito** | **Dettagli**
--- | ---
**Account di Azure** | Account [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
**Servizio Site Recovery** | [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi di Site Recovery. |
**Archiviazione di Azure** | È necessario un account di archiviazione di Azure per archiviare i dati replicati. L'account deve essere nella stessa area dell'insieme di credenziali di servizi di ripristino. I dati replicati vengono memorizzati in Archiviazione di Azure e le macchine virtuali di Azure vengono create quando si verifica il failover.<br/><br/> A seconda del modello di risorsa da usare per le macchine virtuali di Azure di cui si esegue il failover, l'account deve essere configurato in [modalità Resource Manager](../storage/storage-create-storage-account.md) o in [modalità classica](../storage/storage-create-storage-account-classic-portal.md).<br></br>È possibile usare l'[archiviazione con ridondanza geografica](../storage/storage-redundancy.md#geo-redundant-storage) o l'archiviazione con ridondanza locale. È consigliabile usare l'archiviazione con ridondanza geografica per una maggiore resilienza dei dati in caso di interruzione del servizio a livello di area o se non è possibile recuperare l'area primaria.<br/><br/> Se si esegue la replica di server fisici o VM VMware nel portale di Azure, è supportata l'archiviazione Premium. [Archiviazione Premium](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage) si usa in genere per le macchine virtuali che richiedono un livello di prestazioni di I/O costantemente elevato e bassa latenza per ospitare carichi di lavoro con numerose operazioni di I/O. Se si usa Archiviazione Premium per i dati replicati, sarà necessario anche un account di archiviazione standard per l'archiviazione dei log di replica in cui vengono acquisite le modifiche in corso ai dati locali.<br/><br/>
**Limiti di archiviazione** | Non è possibile spostare gli account di archiviazione usati da Site Recovery tra gruppi di risorse nella stessa sottoscrizione o in diverse sottoscrizioni.<br/><br/> La replica in account di archiviazione Premium non è attualmente supportata in India centrale e India meridionale.
**Rete di Azure** | È necessaria una rete di Azure a cui si connetteranno le macchine virtuali dopo il failover. Deve trovarsi nella stessa area dell'insieme di credenziali dei servizi di ripristino.<br/><br/> Nel portale di Azure è possibile creare le reti con i [modelli di Resource Manager](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) o di [distribuzione classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).<br/><br/> In caso di replica da VMM ad Azure, si configurerà il [mapping di rete](site-recovery-network-mapping.md) tra le reti di VM VMM e le reti di Azure in modo da garantire che le VM di Azure si connettano alle reti appropriate dopo il failover.
**Limitazioni di rete** | Non è possibile spostare gli account di rete usati da Site Recovery tra gruppi di risorse nella stessa sottoscrizione o in diverse sottoscrizioni.
**Mapping di rete** | Se si esegue la replica di macchine virtuali Hyper-V in cloud VMM, è necessario configurare il [mapping di rete](site-recovery-network-mapping.md) in modo che le macchine virtuali di Azure siano connesse alle reti appropriate se vengono create dopo il failover.

>[!NOTE]
>Nelle sezioni di seguito vengono descritti i prerequisiti per i vari componenti nell'ambiente del cliente. Per ulteriori informazioni sul supporto per configurazioni specifiche, leggere la [matrice di supporto.](site-recovery-support-matrix.md)
>

## <a name="disaster-recovery-of-vmware-virtual-machines-or-physical-windowslinux-servers-to-azure"></a>Ripristino di emergenza in Azure di macchine virtuali VMware o server Windows/Linux fisici
Di seguito sono elencati i componenti necessari per il ripristino di emergenza di macchine virtuali VMware o server di Windows/Linux fisici oltre a quelli indicati [in precedenza.](#Azure requirements)


1. **Server di configurazione o di elaborazione aggiuntiva**: è necessario configurare un computer locale come server di configurazione per coordinare le comunicazioni tra il sito locale e Azure e per gestire la replica dei dati. <br></br>

2. **Host VMware vCenter/vSphere**

| **Componente** | **Requisiti** |
| --- | --- |
| **vSphere** | Uno o più hypervisor VMware vSphere.<br/><br/>Gli hypervisor devono eseguire vSphere versione 6.0, 5.5 o 5.1 con gli aggiornamenti più recenti.<br/><br/>È consigliabile che gli host vSphere e i server vCenter si trovino nella stessa rete del server di elaborazione. Si tratta della rete in cui si trova il server di configurazione, a meno di avere configurato un server di elaborazione dedicato. |
| **vCenter** | È consigliabile distribuire un server VMware vCenter per gestire gli host vSphere. Deve eseguire vCenter versione 6.0 o 5.5 con gli aggiornamenti più recenti.<br/><br/>**Limitazione**: Site Recovery non supporta le nuove funzionalità di vCenter e vSphere 6.0 come cross-vCenter vMotion, Virtual Volumes e Storage DRS. Site Recovery supporta unicamente le funzionalità disponibili anche nella versione 5.5.||

3.**Prerequisiti dei computer replicati**


| **Componente** | **Requisiti** |
| --- | --- |
| **Macchine virtuali VMware locali** | Nelle VM replicate devono essere installati e in esecuzione gli strumenti VMware.<br/><br/> Le VM devono essere conformi ai [prerequisiti di Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) per la creazione di VM di Azure.<br/><br/>La capacità dei singoli dischi nei computer protetti non deve superare 1023 GB. <br/><br/>Minimo 2 GB di spazio disponibile sull'unità di installazione per l'installazione del componente.<br/><br/>La **porta 20004** deve essere aperta nel firewall locale della VM se si desidera abilitare la coerenza fra più VM.<br/><br/>I nomi delle macchine devono contenere da 1 a 63 caratteri, ovvero lettere, numeri e trattini. Il nome deve iniziare e terminare con una lettera o un numero. È possibile modificare il nome di Azure dopo aver abilitato la replica per una macchina.<br/><br/> |
| **Computer Windows (fisico o VMware)** | Nel computer deve essere in esecuzione un sistema operativo a 64 bit supportato: Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 con SP1 o versioni successive.<br/><br/> Il sistema operativo deve essere installato nell'unità C:\ e il disco del sistema operativo deve essere un disco di base di Windows, non un disco dinamico. Il disco dati può essere dinamico.<br/><br/>|
| **Computer Linux** (fisico o VMware) | È necessario un sistema operativo a 64 bit supportato: Red Hat Enterprise Linux 6.7,7.1,7.2; Centos 6.5, 6.6,6.7,7.0,7.1,7.2; Oracle Enterprise Linux 6.4, 6.5 che esegue il kernel compatibile Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>I file /etc/hosts nei computer protetti devono contenere le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede di rete.<br/><br/>Per connettersi a una macchina virtuale di Azure che esegue Linux dopo il failover usando un client Secure Shell (SSH), accertarsi che il servizio Secure Shell nel computer protetto sia impostato per l'avvio automatico all'avvio del sistema e che le regole del firewall permettano una connessione SSH a tale computer.<br/><br/>Nome host, punti di montaggio, nomi dispositivo e percorsi di sistema di Linux e nomi file (ad esempio /etc/, /usr) dovranno essere specificati solo con caratteri dell'alfabeto latino.<br/><br/>

## <a name="disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-vmm"></a>Ripristino di emergenza in Azure di macchine virtuali Hyper-V (non VMM)

Di seguito sono elencati i componenti necessari per il ripristino di emergenza di macchine virtuali Hyper-V in cloud VMM oltre a quelli indicati [in precedenza.](#Azure requirements)

| **Prerequisito** | **Dettagli** |
| --- | --- |
| **Host Hyper-V** |Uno o più server locali in cui è in esecuzione Windows Server 2012 R2 con tutti gli aggiornamenti più recenti e il ruolo Hyper-V abilitato o Microsoft Hyper-V Server 2012 R2.<br></br>Il server Hyper-V deve contenere una o più macchine virtuali<br></br>I server Hyper-V devono essere connessi a Internet, in modo diretto o tramite proxy.<br></br>Nei server Hyper-V devono essere state installate le correzioni indicate nell'articolo [KB2961977](https://support.microsoft.com/en-us/kb/2961977) della Microsoft Knowledge Base.
|**Provider e agente**| Durante la distribuzione di Azure Site Recovery verrà installato il provider di Azure Site Recovery. Durante l'installazione del provider verrà installato anche l'agente di Servizi di ripristino di Azure in ogni server Hyper-V che esegue le macchine virtuali da proteggere. <br></br>In tutti i server Hyper-V in un insieme di credenziali di Site Recovery devono essere installate le stesse versioni del provider e dell'agente.<br></br>Il provider dovrà connettersi ad Azure Site Recovery tramite Internet. Il traffico può essere inviato direttamente oppure tramite un proxy. Si noti che il proxy basato su HTTPS non è supportato. Il server proxy deve consentire l'accesso a: <br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/>Se nel server sono disponibili regole firewall basate sull'indirizzo IP, verificare che le regole consentano la comunicazione con Azure.<br></br> Consentire gli [intervalli IP del data center di Azure ](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta HTTPS (443).<br></br> Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (usati per il controllo di accesso e la gestione delle identità).


## <a name="disaster-recovery-of-hyper-v-virtual-machines-in-vmm-clouds-to-azure"></a>Ripristino di emergenza in Azure di macchine virtuali Hyper-V in cloud VMM

Di seguito sono elencati i componenti necessari per il ripristino di emergenza di macchine virtuali Hyper-V in cloud VMM oltre a quelli indicati [in precedenza.](#Azure requirements)

| **Prerequisito** | **Dettagli** |
| --- | --- |
| **VMM** |Uno o più server VMM in esecuzione su **System Center 2012 R2 o versione successiva**. Ogni server VMM deve essere configurato con uno o più cloud. <br></br>Ogni cloud deve contenere<br>- Uno o più gruppi host VMM.<br/>- Uno o più cluster o server host Hyper-V in ogni gruppo host.<br/><br/>[Altre informazioni](http://social.technet.microsoft.com/wiki/contents/articles/2729.how-to-create-a-cloud-in-vmm-2012.aspx) sulla configurazione dei cloud VMM. |
| **Hyper-V** |I server host Hyper-V devono eseguire almeno **Windows Server 2012 R2** con ruolo Hyper-V **Microsoft Hyper-V Server 2012 R2** e disporre degli ultimi aggiornamenti installati.<br/><br/> Il server Hyper-V deve contenere una o più macchine virtuali.<br/><br/> Il server host Hyper-V o il cluster che include le macchine virtuali da replicare deve essere gestito in un cloud VMM.<br/><br/>I server Hyper-V devono essere connessi a Internet, in modo diretto o tramite proxy.<br/><br/>Nei server Hyper-V è necessario avere installato le correzioni specificate nell'articolo [2961977](https://support.microsoft.com/kb/2961977).<br/><br/>I server host Hyper-V richiedono l'accesso a Internet per la replica dei dati in Azure. |
| **Provider e agente** |Durante la distribuzione di Azure Site Recovery vengono installati il provider di Azure Site Recovery nel server VMM e l'agente di Servizi di ripristino negli host Hyper-V. Il provider e l'agente devono connettersi ad Azure tramite Internet, in modo diretto o attraverso un proxy. Un proxy basato su HTTPS non è supportato. Il server proxy nel server VMM e gli host Hyper-devono poter accedere a: <br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>Se nel server VMM sono presenti regole firewall basate sull'indirizzo IP, verificare che consentano la comunicazione con Azure.<br/><br/> Consentire gli [intervalli IP del data center di Azure ](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta HTTPS (443).<br/><br/> Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (usati per il controllo di accesso e la gestione delle identità).<br/><br/> |

### <a name="replicated-machine-prerequisites"></a>Prerequisiti dei computer replicati
| **Componente** | **Dettagli** |
| --- | --- |
| **Macchine virtuali protette** | Site Recovery supporta tutti i sistemi operativi supportati da [Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).<br></br>Le VM devono essere conformi ai [prerequisiti di Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) per la creazione di VM di Azure. I nomi delle macchine devono contenere da 1 a 63 caratteri, ovvero lettere, numeri e trattini. Il nome deve iniziare e terminare con una lettera o un numero. <br></br>È possibile modificare il nome dopo aver abilitato la replica per la macchina virtuale. <br/><br/> La capacità dei singoli dischi nei computer protetti non deve superare 1023 GB. Una macchina virtuale può avere fino a 16 dischi (quindi fino a 16 TB).<br/><br/>


## <a name="disaster-recovery-of-hyper-v-virtual-machines-in-vmm-clouds-to-a-customer-owned-site"></a>Ripristino di emergenza di macchine virtuali Hyper-V in cloud VMM in un sito di proprietà del cliente

Di seguito sono elencati i componenti necessari per il ripristino di emergenza di macchine virtuali Hyper-V in cloud VMM in un sito di proprietà del cliente, oltre a quelli indicati [in precedenza.](#Azure requirements)

| **Componenti** | **Dettagli** |
| --- | --- |
| **VMM** |  È consigliabile eseguire la distribuzione di un server VMM nel sito primario e di un server VMM nel sito secondario.<br/><br/> È possibile [eseguire la replica tra cloud in un unico server VMM](site-recovery-single-vmm.md). A tale scopo, sono necessari almeno due cloud configurati sul server VMM.<br/><br/> I server VMM devono eseguire **almeno System Center 2012 SP1** con gli aggiornamenti più recenti.<br/><br/> Ogni server VMM deve avere almeno uno o più cloud. In tutti i cloud deve essere impostato il profilo della capacità Hyper-V. <br/><br/>I cloud devono contenere uno o più gruppi host VMM. [Altre informazioni](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric) sulla configurazione dei cloud VMM.<br/><br/> I server VMM richiedono l'accesso a Internet. |
| **Hyper-V** | I server Hyper-V devono eseguire **almeno Windows Server 2012 con ruolo Hyper-V** e con gli ultimi aggiornamenti installati.<br/><br/> Il server Hyper-V deve contenere una o più macchine virtuali.<br/><br/>  I server host Hyper-V devono trovarsi nei gruppi host disponibili nei cloud VMM primario e secondario.<br/><br/> Se si esegue Hyper-V in un cluster in Windows Server 2012 R2 è necessario installare l'[aggiornamento 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Se si esegue Hyper-V in un cluster basato su indirizzi IP statici in Windows Server 2012, il broker del cluster non viene creato automaticamente. È necessario configurare manualmente il broker del cluster. [Altre informazioni](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx). |
| **Provider** | Durante la distribuzione di Site Recovery, installare il provider di Azure Site Recovery nei server VMM. Il provider comunica con Site Recovery tramite HTTPS 443 per orchestrare la replica. La replica dei dati viene eseguita tra il server Hyper-V primario e quello secondario attraverso la rete LAN o una connessione VPN.<br/><br/> Il provider in esecuzione nel server VMM deve poter accedere agli URL seguenti:<br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>Consentire la comunicazione del firewall dai server VMM agli [intervalli IP del data center di Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e consentire il protocollo HTTPS (443). |



<!--HONumber=Jan17_HO2-->


