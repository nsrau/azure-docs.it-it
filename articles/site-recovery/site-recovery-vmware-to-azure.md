<properties
    pageTitle="Eseguire la replica di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery nel portale di Azure| Microsoft Azure"
    description="Descrive come distribuire Azure Site Recovery per orchestrare la replica, il failover e il ripristino di macchine virtuali VMware locali e server fisici Windows/Linux in Azure, tramite il portale di Azure"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="raynew"/>


# <a name="replicate-vmware-virtual-machines-and-physical-machines-to-azure-with-azure-site-recovery-using-the-azure-portal"></a>Eseguire la replica di macchine virtuali VMware e computer fisici in Azure con Azure Site Recovery tramite il portale di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](site-recovery-vmware-to-azure.md)
- [Azure - Classica](site-recovery-vmware-to-azure-classic.md)
- [Azure classico (legacy)](site-recovery-vmware-to-azure-classic-legacy.md)

Benvenuti in Azure Site Recovery. Questo articolo illustra come eseguire la replica di macchine virtuali VMware o server fisici Windows/Linux locali in Azure usando Azure Site Recovery nel portale di Azure.

> [AZURE.NOTE] Azure offre due diversi [modelli di distribuzione](../resource-manager-deployment-model.md) per creare e usare le risorse: Azure Resource Manager (ARM) e la distribuzione classica. Sono anche disponibili il portale di Azure classico, che supporta il modello di distribuzione classica, e il portale di Azure, che supporta entrambi i modelli di distribuzione.

Azure Site Recovery nel portale di Azure offre numerose nuove funzionalità:

- I servizi Backup di Azure e Azure Site Recovery sono combinati in un unico insieme di credenziali di Servizi di ripristino per consentire la configurazione e la gestione della continuità aziendale e del ripristino di emergenza da un'unica posizione. Nel dashboard unificato è possibile di monitorare e gestire le operazioni nei siti locali e nel cloud pubblico di Azure.
- Gli utenti con sottoscrizioni di Azure in cui è stato effettuato il provisioning del programma Cloud Solution Provider (CSP) ora possono gestire le operazioni di Site Recovery nel portale di Azure.
- Site Recovery nel portale di Azure può eseguire la replica di macchine virtuali in account di archiviazione di Azure Resource Manager. In caso di failover, Site Recovery crea macchine virtuali basate su Azure Resource Manager in Azure.
- Site Recovery continua a supportare la replica negli account di archiviazione della versione classica. In caso di failover, Site Recovery crea macchine virtuali con il modello di distribuzione classica.

Dopo aver letto questo articolo è possibile lasciare eventuali commenti nella sezione Disqus, nella parte inferiore della pagina. In caso di domande tecniche, visitare il [forum di Azure Site Recovery](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Panoramica

Le organizzazioni necessitano di una strategia di continuità aziendale e ripristino di emergenza per determinare come app, carichi di lavoro e dati possano rimanere in esecuzione e disponibili durante i periodi di inattività, pianificati o meno, e come ripristinare le normali condizioni di lavoro il prima possibile. La strategia di continuità aziendale e ripristino di emergenza deve garantire la sicurezza dei dati aziendali e la possibilità di recuperarli, oltre alla disponibilità costante dei carichi di lavoro in caso di emergenza.

Il servizio Azure Site Recovery contribuisce alla strategia BCDR orchestrando la replica dei server fisici locali e delle macchine virtuali sul cloud (Azure) o in un data center secondario. In caso di interruzioni nella località primaria, verrà eseguito il failover alla località secondaria per mantenere disponibili app e carichi di lavoro. Quando la località primaria sarà di nuovo operativa, si tornerà a tale località. Per altre informazioni, vedere [Che cos'è Azure Site Recovery?](site-recovery-overview.md)

Questo articolo fornisce tutte le informazioni necessarie per eseguire la replica di macchine virtuali VMware e server fisici Windows/Linux locali in Azure. Include una panoramica sull'architettura, informazioni sulla pianificazione e procedure di distribuzione per la configurazione di Azure, dei server locali, delle impostazioni di replica e della pianificazione della capacità. Dopo aver configurato l'infrastruttura è possibile abilitare la replica nei computer da proteggere e verificare il funzionamento del failover.

## <a name="business-advantages"></a>Vantaggi per le aziende

- Site Recovery garantisce la protezione esterna per i carichi di lavoro aziendali e le applicazioni in esecuzione in macchine virtuali VMware e server fisici.
- Il portale di Servizi di ripristino permette di configurare, gestire e monitorare le operazioni di replica, failover e ripristino da un'unica posizione.
- Site Recovery può individuare automaticamente le macchine virtuali VMware aggiunte agli host vSphere.
- È possibile eseguire facilmente il failover dall'infrastruttura locale in Azure ed eseguire il failback (ripristino) da Azure ai server delle macchine virtuali VMware nel sito locale.
- È possibile abilitare più macchine virtuali e creare gruppi di replica in modo che i carichi di lavoro dell'applicazione suddivisi a livelli tra più macchine virtuali vengano replicati contemporaneamente. Tutte le macchine virtuali in un gruppo di replica hanno punti di ripristino coerenti con l'arresto anomalo del sistema e coerenti con l'app quando si esegue il failover. Per il failover è possibile raccogliere più macchine virtuali in piani di ripristino, in modo che il failover dei carichi di lavoro delle applicazioni a più livelli venga eseguito contemporaneamente.

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

### <a name="windows(64-bit-only)"></a>Windows (solo versione a 64 bit)
- Windows Server 2008 R2 SP1+
- Windows Server 2012
- Windows Server 2012 R2

### <a name="linux-(64-bit-only)"></a>Linux (solo versione a 64 bit)
- Red Hat Enterprise Linux 6.7, 7.1, 7.2
- CentOS 6.5, 6.6, 6.7, 7.0, 7.1, 7.2
- Oracle Enterprise Linux 6.4 o 6.5 che esegue il kernel compatibile Red Hat o Unbreakable Enterprise Kernel versione 3 (UEK3)
- SUSE Linux Enterprise Server 11 SP3

## <a name="scenario-architecture"></a>Architettura dello scenario

Componenti dello scenario:

- **Server di configurazione**: computer locale che coordina le comunicazioni e gestisce i processi di ripristino e replica dei dati. In questo computer si eseguirà un singolo file di installazione per installare il server di configurazione e i componenti aggiuntivi seguenti:
    - **Server di elaborazione**: agisce come un gateway di replica. Riceve i dati di replica da computer di origine protetti, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia all'archiviazione di Azure. Gestisce anche l'installazione push del servizio Mobility nei computer protetti ed esegue l'individuazione automatica delle VM VMware. Il server di elaborazione predefinito viene installato nel server di configurazione. È possibile distribuire altri server di elaborazione autonomi per ridimensionare l'ambiente.
    - **Server di destinazione master**: gestisce i dati di replica durante il failback da Azure.

- **Servizio Mobility**: questo componente viene distribuito in ogni computer, ovvero macchina virtuale VMware o server fisico, di cui si vuole eseguire la replica in Azure. Acquisisce le scritture di dati nel computer e le inoltra al server di elaborazione.
- **Azure**: non è necessario creare macchine virtuali di Azure per gestire la replica e il failover in Azure.  È invece necessario avere una sottoscrizione di Azure, un account di archiviazione di Azure per l'archiviazione dei dati replicati e una rete virtuale di Azure in modo che le macchine virtuali di Azure siano connesse a una rete dopo il failover. L'account di archiviazione e la rete devono trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino.
- **Failback**: quando si è pronti per eseguire il failback da Azure al sito locale dopo un failover, è necessario creare una VM di Azure come server di elaborazione temporaneo, che può essere eliminata una volta completato il failback. Per il failback, è necessaria anche una connessione VPN, o Azure ExpressRoute, tra il sito locale e la rete di Azure in cui si trovano le macchine virtuali di Azure. Se il traffico di failback è elevato, potrebbe essere necessario configurare anche un computer server master di destinazione dedicato in locale. Per un traffico moderato, è possibile usare il server master di destinazione predefinito in esecuzione nel server di configurazione.


Il grafico seguente mostra l'interazione tra questi componenti.

![architettura](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**Figura 1: Macchine VMware/fisiche in Azure**

## <a name="azure-prerequisites"></a>Prerequisiti di Azure

Elementi necessari in Azure per la distribuzione di questo scenario:

**Prerequisito** | **Dettagli**
--- | ---
**Account di Azure**| È necessario un account [Microsoft Azure](http://azure.microsoft.com/) . È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi di Site Recovery.
**Archiviazione di Azure** | I dati replicati vengono memorizzati in Archiviazione di Azure e le macchine virtuali di Azure vengono create quando si verifica il failover. <br/><br/>Per archiviare i dati è necessario un account di archiviazione Standard o Premium nella stessa area dell'insieme di credenziali di Servizi di ripristino.<br/><br/>.È possibile usare un account di archiviazione con ridondanza locale o con ridondanza geografica. È consigliabile usare l'archiviazione con ridondanza geografica per una maggiore resilienza dei dati in caso di interruzione del servizio a livello di area o se non è possibile recuperare l'area primaria. [Altre informazioni](../storage/storage-redundancy.md)<br/><br/> [Archiviazione premium](../storage/storage-premium-storage.md) viene in genere usata per le macchine virtuali che richiedono un livello di prestazioni di I/O costantemente elevato e bassa latenza per ospitare carichi di lavoro con numerose operazioni di I/O.<br/><br/>  Se si vuole usare un account Premium per archiviare i dati replicati, sarà necessario anche un account di archiviazione standard per l'archiviazione dei log di replica in cui vengono acquisite le modifiche in corso ai dati locali.<br/><br/>  Si noti che gli account di archiviazione creati nel portale di Azure non possono essere spostati tra gruppi di risorse. La protezione per gli account di archiviazione Premium non è attualmente supportata in India centrale e India meridionale.<br/><br/> [Altre informazioni](../storage/storage-introduction.md) sul servizio di archiviazione di Azure.
**Rete di Azure** | È necessaria una rete virtuale di Azure a cui le macchine virtuali di Azure possano connettersi quando si verifica il failover. La rete virtuale di Azure deve risiedere nella stessa area dell'insieme di credenziali di Servizi di ripristino.
**Failback da Azure** | È necessario un server di elaborazione temporaneo è configurato come una macchina virtuale di Azure. È possibile crearlo quando si è pronti per eseguire il failback ed eliminarlo una volta completato il processo.<br/><br/>  Per eseguire il failback è necessaria una connessione VPN, oppure Azure ExpressRoute, dalla rete di Azure al sito locale.

## <a name="configuration-server/-scale-out-process-prerequisites"></a>Prerequisiti del server di configurazione/elaborazione con scalabilità orizzontale

Si dovrà configurare un computer locale come server di configurazione/elaborazione con la scalabilità orizzontale.

**Prerequisito** | **Dettagli**
--- | ---
**Server di configurazione/server di elaborazione con la scalabilità orizzontale**| È necessario un computer fisico o una macchina virtuale locale che esegue Windows Server 2012 R2. Tutti i componenti locali di Site Recovery vengono installati in questo dispositivo.<br/><br/>Per la replica di macchine virtuali VMware è consigliabile distribuire il server come una macchina virtuale VMware a disponibilità elevata. Se si esegue la replica di computer fisici, si può usare un server fisico.<br/><br/>  Il failback da Azure al sito locale viene sempre eseguito in macchine virtuali VMware indipendentemente dal fatto che il failover sia stato eseguito in macchine virtuali o in server fisici. Se non si distribuisce il server di configurazione come macchina virtuale VMware, si dovrà configurare un server di destinazione master separato come macchina virtuale VMware per ricevere il traffico di failback.<br/><br/>Se il server è una macchina virtuale VMware, il tipo di scheda di rete deve essere VMXNET3. Se si usa un diverso tipo di scheda di rete, è necessario installare un [aggiornamento VMware](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1) nel server vSphere 5.5.<br/><br/>Il server deve disporre di un indirizzo IP statico.<br/><br/>Il server non deve essere un controller di dominio.<br/><br/>Il nome host del server deve avere una lunghezza massima di 15 caratteri.<br/><br/>Il sistema operativo deve essere solo in lingua inglese.<br/><br/>  È necessario installare VMware vSphere PowerCLI 6.0. nel server di configurazione.<br/><br/>Il server di configurazione richiede l'accesso a Internet. È necessario l'accesso in uscita con le caratteristiche seguenti:<br/><br/>L'accesso temporaneo sulla porta HTTP 80 durante l'installazione dei componenti di Site Recovery per scaricare MySQL<br/><br/>L'accesso in uscita in corso sulla porta HTTPS 443 per la gestione delle repliche<br/><br/>L'accesso in uscita in corso sulla porta HTTPS 9443 per il traffico di replica; questa porta può essere modificata<br/><br/>Il server deve anche poter accedere ai seguenti URL per connettersi ad Azure: *.hypervrecoverymanager.windowsazure.com; *.accesscontrol.windows.net; *.backup.windowsazure.com; *.blob.core.windows.net; *.store.core.windows.net<br/><br/>Se nel server sono disponibili regole firewall basate sull'indirizzo IP, verificare che le regole consentano la comunicazione con Azure. È necessario consentire gli [Azure Datacenter IP Ranges](https://www.microsoft.com/download/confirmation.aspx?id=41653) e il protocollo HTTPS (443).<br/><br/>Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali.<br/><br/>Consentire questo URL per il download di MySQL: .http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi


## <a name="vmware-vcenter/vsphere-host-prerequisites"></a>Prerequisiti dell'host VMware vCenter/vSphere

**Prerequisito** | **Dettagli**
--- | ---
**vSphere**| Sono necessari uno o più hypervisor VMware vSphere.<br/><br/>Gli hypervisor devono eseguire vSphere versione 6.0, 5.5 o 5.1 con gli aggiornamenti più recenti.<br/><br/>È consigliabile che gli host vSphere e i server vCenter si trovino nella stessa rete del server di elaborazione. Si tratta della rete in cui si trova il server di configurazione, a meno di avere configurato un server di elaborazione dedicato.
**vCenter** | È consigliabile distribuire un server VMware vCenter per gestire gli host vSphere. Il server deve eseguire vCenter versione 6.0 o 5.5 con gli aggiornamenti più recenti.<br/><br/>Si noti che Site Recovery non supporta le nuove funzionalità di vCenter e vSphere 6.0 come cross-vCenter vMotion, Virtual Volumes e Storage DRS. Site Recovery supporta unicamente le funzionalità disponibili anche nella versione 5.5.


## <a name="protected-machine-prerequisites"></a>Prerequisiti dei computer protetti

**Prerequisito** | **Dettagli**
--- | ---
**Macchine virtuali VMware locali** | Nelle macchine virtuali VMware da proteggere devono essere installati e in esecuzione gli strumenti VMware.<br/><br/> Le macchine da proteggere devono essere conformi ai [prerequisiti di Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) per la creazione di VM di Azure.<br/><br/>La capacità dei singoli dischi nei computer protetti non deve superare 1023 GB. Una macchina virtuale può avere fino a 64 dischi (quindi fino a 64 TB). <br/><br/>La Protezione delle VM con dischi crittografati (radice e dischi dati) non è supportata.

I cluster guest in dischi condivisi non sono supportati.<br/><br/>La **porta 20004** deve essere lasciata aperta nelle VM protette se si intende abilitare la **coerenza dell'applicazione**.

L'avvio in modalità UEFI (Unified Extensible Firmware Interface)/EFI (Extensible Firmware Interface) non è supportato.<br/><br/>I nomi delle macchine devono contenere da 1 a 63 caratteri, ovvero lettere, numeri e trattini. Il nome deve iniziare e terminare con una lettera o un numero. È possibile modificare il nome di Azure dopo aver abilitato la replica per una macchina.<br/><br/>Se la VM di origine dispone del gruppo NIC, questo viene convertito in una singola scheda NIC dopo il failover in Azure.<br/><br/>Se le VM protette dispongono di un disco iSCSI, Site Recovery converte il disco iSCSI delle VM protette in un file VHD quando la VM esegue il failover in Azure. Se la destinazione iSCSI può essere raggiunta dalla macchina virtuale di Azure, questa si connette a tale destinazione, rendendo essenzialmente visibili due dischi: il disco rigido virtuale nella macchina virtuale di Azure e il disco iSCSI di origine. In questo caso è necessario disconnettere la destinazione iSCSI visualizzata nella macchina virtuale di Azure.
**Computer Windows (fisico o VMware)** | Nel computer deve essere in esecuzione un sistema operativo a 64 bit supportato: Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 con SP1 o versioni successive.<br/><br/> Il sistema operativo deve essere installato nell'unità C:\ e il disco del sistema operativo deve essere un disco di base di Windows, non un disco dinamico. Il disco dati può essere dinamico.<br/><br/>Site Recovery supporta VM con un disco RDM. Se la macchina virtuale di origine e il disco RDM sono disponibili, durante il failback Site Recovery riutilizza il disco RDM. Se non sono disponibili, durante il failback Site Recovery crea un nuovo file VMDK per ogni disco.
**Computer Linux** | È necessario un sistema operativo a 64 bit supportato: Red Hat Enterprise Linux 6.7,7.1,7.2; Centos 6.5, 6.6,6.7,7.0,7.1,7.2; Oracle Enterprise Linux 6.4, 6.5 che esegue il kernel compatibile Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>I file /etc/hosts nei computer protetti devono contenere le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede di rete.<br/><br/>Per connettersi a una macchina virtuale di Azure che esegue Linux dopo il failover usando un client Secure Shell (SSH), accertarsi che il servizio Secure Shell nel computer protetto sia impostato per l'avvio automatico all'avvio del sistema e che le regole del firewall permettano una connessione SSH a tale computer.<br/><br/>Nome host, punti di montaggio, nomi dispositivo e percorsi di sistema di Linux e nomi file (ad esempio /etc/, /usr) dovranno essere specificati solo con caratteri dell'alfabeto latino.<br/><br/>La protezione può essere abilitata solo per i computer Linux con le risorse di archiviazione seguenti: file system (EXT3, ETX4, ReiserFS e XFS); software per percorsi multipli - Device Mapper (Multipath); Archiviazione volumi: LVM2. I server fisici con archiviazione del controller HP CCISS non sono supportati. Il file system ReiserFS è supportato solo su SUSE Linux Enterprise Server 11 SP3.<br/><br/>Site Recovery supporta VM con un disco RDM.  Durante il failback per Linux, Site Recovery non riutilizza il disco RDM, ma crea un nuovo file VMDK per ogni disco RDM corrispondente.<br/><br/>Assicurarsi di configurare l'impostazione disk.enableUUID=true nei parametri di configurazione della VM in VMware. Se la voce non esiste, crearla. È necessaria per fornire un valore UUID coerente al file VMDK in modo che venga installato correttamente. L'aggiunta di questa impostazione assicura anche che solo le modifiche differenziali vengano ritrasferite all'ambiente locale durante il failback invece di una replica completa.
**Servizio Mobility** | **Windows**: per eseguire il push automatico del servizio Mobility alle VM che eseguono Windows, è necessario specificare un account amministratore, ovvero un amministratore locale nel computer Windows, per consentire al server di elaborazione di eseguire un'installazione push.<br/><br/> **Linux**: per eseguire il push automatico del servizio Mobility alle VM che eseguono Linux, è necessario creare un account che possa essere usato dal server di elaborazione per eseguire un'installazione push.<br/><br/> Per impostazione predefinita, vengono replicati tutti i dischi in un computer. Per [escludere un disco dalla replica](#exclude-disks-from-replication), è necessario installare manualmente il servizio Mobility nel computer prima di abilitare la replica.

## <a name="prepare-for-deployment"></a>Preparare la distribuzione

Per preparare la distribuzione è necessario:

1. [Configurare una rete di Azure](#set-up-an-azure-network) in cui saranno inserite le macchine virtuali di Azure quando vengono riattivate dopo il failover. Per il failback sarà anche necessario configurare una connessione VPN, oppure Azure ExpressRoute, dalla rete di Azure al sito locale.
2. [Configurare un account di archiviazione di Azure](#set-up-an-azure-storage-account) per i dati replicati.
3. [Preparare un account](#prepare-an-account-for-automatic-discovery) nel server vCenter o negli host vSphere per consentire a Site Recovery di rilevare automaticamente le macchine virtuali VMware aggiunte.
4. [Preparare il server di configurazione](#prepare-the-configuration-server) per assicurarsi che possa accedere agli URL necessari e installare vSphere PowerCLI 6.0.


### <a name="set-up-an-azure-network"></a>Configurare una rete di Azure

- La rete deve trovarsi nella stessa area di Azure della rete in cui verrà distribuito l'insieme di credenziali di Servizi di ripristino.
- A seconda del modello di risorsa da usare per le VM di Azure di cui si esegue il failover, la rete di Azure deve essere configurata in [modalità Azure Resource Manager](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) o in [modalità classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- Per eseguire il failback da Azure al sito VMware locale è necessaria una connessione VPN, o una connessione ExpressRoute di Azure, dalla rete di Azure in cui si trovano macchine virtuali di Azure replicate, alla rete locale in cui si trova il server di configurazione.
- [Informazioni](../vpn-gateway/vpn-gateway-site-to-site-create.md) sui modelli di distribuzione supportati per le connessioni VPN da sito a sito e su come [configurare una connessione](../vpn-gateway/vpn-gateway-site-to-site-create.md#create-your-virtual-network).
- In alternativa è possibile configurare [Azure ExpressRoute](../expressroute/expressroute-introduction.md). [Altre informazioni](../expressroute/expressroute-howto-vnet-portal-classic.md) sulla configurazione di una rete di Azure con ExpressRoute.

> [AZURE.NOTE] La [migrazione di reti](../resource-group-move-resources.md) all'interno dei gruppi di risorse con la stessa sottoscrizione o all'interno delle sottoscrizioni non è supportata per le reti usate per la distribuzione di Site Recovery.

### <a name="set-up-an-azure-storage-account"></a>Configurare un account di archiviazione di Azure

- Per contenere i dati replicati in Azure, è necessario un account di archiviazione di Azure Standard o Premium. L'account deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino. A seconda del modello di risorsa da usare per le VM di Azure di cui si esegue il failover, l'account deve essere configurato in [modalità Azure Resource Manager](../storage/storage-create-storage-account.md) o in [modalità classica](../storage/storage-create-storage-account-classic-portal.md).
- Se si usa un account Premium per i dati replicati, è necessario creare un account Standard aggiuntivo per l'archiviazione dei log di replica in cui vengono acquisite le modifiche in corso ai dati locali.  

> [AZURE.NOTE] [La migrazione degli account di archiviazione](../resource-group-move-resources.md) all'interno dei gruppi di risorse con la stessa sottoscrizione o all'interno delle sottoscrizioni non è supportata per gli account di archiviazione usati per la distribuzione di Site Recovery.

### <a name="prepare-an-account-for-automatic-discovery"></a>Preparare un account per l'individuazione automatica

Il server di elaborazione di Site Recovery può individuare automaticamente le macchine virtuali VMware negli host vSphere o in un server vCenter che gestisce gli host. Per eseguire l'individuazione automatica con Site Recovery sono necessarie le credenziali di accesso al server VMware. Questa istruzione non si applica se si esegue la replica solo di computer fisici.

1. Per usare un account dedicato per l'individuazione automatica, creare un ruolo, ad esempio Azure_Site_Recovery, a livello di vCenter con le [autorizzazioni necessarie](#vmware-account-permissions).
2. Creare un nuovo utente nell'host vSphere o nel server vCenter e assegnare il ruolo all'utente. In un secondo momento si comunicheranno queste credenziali a Site Recovery in modo che possa eseguire l'individuazione automatica.

    >[AZURE.NOTE] Un account utente di vCenter con un ruolo di sola lettura può eseguire il failover, ma non può arrestare i computer di origine protetti. Per arrestare queste macchine, è necessario il ruolo [Azure_Site_Recovery](#vmware-account-permissions). Se si sta eseguendo solo la migrazione delle macchine virtuali da VMware in Azure e non è necessario il failback, il ruolo di sola lettura è sufficiente.

### <a name="prepare-the-configuration-server"></a>Preparare il server di configurazione

1.  Assicurarsi che il computer usato come server di configurazione sia conforme ai [prerequisiti](#configuration-server-prerequisites). In particolare assicurarsi che il computer sia connesso a internet con le impostazioni seguenti:

    - Consentire l'accesso a questi URL: *.hypervrecoverymanager.windowsazure.com; *.accesscontrol.windows.net; *.backup.windowsazure.com; *.blob.core.windows.net; *.store.core.windows.net
    - Consentire l'accesso a [http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi](http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi) per scaricare MySQL.
    - Consentire la comunicazione del firewall con Azure tramite gli [intervalli di indirizzi IP del data center di Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e il protocollo HTTPS (443).

2.  Scaricare e installare [VMware vSphere PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) nel server di configurazione. Le altre versioni di PowerCLI non sono attualmente supportate, comprese le versioni R della versione 6.0.


## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Nuovo** > **Gestione** > **Backup e Site Recovery (OMS)**. In alternativa, è possibile fare clic su **Sfoglia** > **Insieme di credenziali dei servizi di ripristino** > **Aggiungi**.

    ![Nuovo insieme di credenziali](./media/site-recovery-vmware-to-azure/new-vault3.png)

3. In **Nome** specificare un nome descrittivo per identificare l'insieme di credenziali. Se è disponibile più di una sottoscrizione, selezionarne una.
4. [Creare un nuovo gruppo di risorse](../resource-group-template-deploy-portal.md) o selezionarne uno esistente. Specificare un'area di Azure. I computer verranno replicati in quest'area. Archiviazione di Azure e le reti usate per Site Recovery dovranno essere nella stessa area. Per informazioni sulla disponibilità a livello geografico e sulle aree supportate, vedere la pagina relativa ai [dettagli sui prezzi per Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Per accedere rapidamente all'insieme di credenziali dal dashboard, fare clic su **Aggiungi al dashboard** e quindi su **Crea**.

    ![Nuovo insieme di credenziali](./media/site-recovery-vmware-to-azure/new-vault-settings.png)

Il nuovo insieme di credenziali verrà visualizzato in **Dashboard** > **Tutte le risorse** e nel pannello **Insiemi di credenziali dei servizi di ripristino** principale.

## <a name="getting-started"></a>Introduzione

Le attività iniziali di Site Recovery sono progettate per poter essere subito operativi. Controlla i prerequisiti e descrive la procedura che è necessario seguire per la distribuzione di Site Recovery.

Selezionare il tipo di computer da replicare e la posizione in cui eseguire la replica. Configurare l'infrastruttura, compresi i server locali, le impostazioni di Azure, i criteri di replica e la pianificazione della capacità. Una volta completata l'infrastruttura, abilitare la replica per le macchine virtuali e i server fisici. È quindi possibile eseguire il failover per computer specifici o creare piani di ripristino per eseguire il failover di più computer.

Per avviare le attività iniziali, scegliere la modalità di distribuzione di Site Recovery. Il flusso delle attività iniziali è leggermente diverso a seconda dei requisiti di replica.


## <a name="step-1:-choose-your-protection-goals"></a>Passaggio 1: Scegliere gli obiettivi della protezione

Selezionare gli elementi da replicare e la posizione in cui eseguire la replica.

1. Nel pannello **Insiemi di credenziali dei servizi di ripristino** selezionare l'insieme di credenziali e fare clic su **Impostazioni**.
2. In **Impostazioni** > **Attività iniziali** fare clic su **Site Recovery** > **Passaggio 1: Preparare l'infrastruttura** > **Obiettivo di protezione**.

    ![Scegliere gli obiettivi](./media/site-recovery-vmware-to-azure/choose-goals.png)

3. In **Obiettivo di protezione** selezionare **In Azure** e scegliere **Sì, con VMware vSphere Hypervisor**. Fare quindi clic su **OK**.

    ![Scegliere gli obiettivi](./media/site-recovery-vmware-to-azure/choose-goals2.png)


## <a name="step-2:-set-up-the-source-environment"></a>Passaggio 2: Configurare l'ambiente di origine

Configurare il server di configurazione e registrarlo nell'insieme di credenziali di Servizi di ripristino. Se si esegue la replica di macchine virtuali VMware, specificare l'account VMware in uso per l'individuazione automatica.

1. Fare clic su **Passaggio 1: Preparare l'infrastruttura** > **Origine**. Se non è disponibile un server di configurazione, in **Prepara origine** fare clic su **+Server di configurazione** per aggiungerne uno.

    ![Impostare l'origine](./media/site-recovery-vmware-to-azure/set-source1.png)

2. Nel pannello **Aggiungi server** verificare che **Server di configurazione** sia visualizzato in **Tipo di server**.
3. Prima di configurare il server di configurazione verificare i [prerequisiti](#configuration-server-prerequisites). In particolare, controllare che il computer possa accedere gli URL necessari.
4.  Scaricare il file di installazione per l'Installazione unificata di Azure Site Recovery.
5.  Scaricare la chiave di registrazione dell'insieme di credenziali, che sarà necessaria quando si esegue l'Installazione unificata. La chiave è valida per cinque giorni dal momento in cui viene generata.

    ![Impostare l'origine](./media/site-recovery-vmware-to-azure/set-source2.png)

6.  Nel computer che si usa come server di configurazione, eseguire l'Installazione unificata per installare il server di configurazione, il server di elaborazione e il server master di destinazione.


### <a name="run-site-recovery-unified-setup"></a>Eseguire l'Installazione unificata di Site Recovery

1.  Eseguire il file di installazione per l'Installazione unificata.
2.  In **Prima di iniziare** selezionare l'opzione **Installare il server di configurazione e il server di elaborazione**.

    ![Prima di iniziare](./media/site-recovery-vmware-to-azure/combined-wiz1.png)

3. In **Licenza software di terze parti** fare clic su **Accetto** per scaricare e installare MySQL.

    ![Software di terze parti](./media/site-recovery-vmware-to-azure/combined-wiz105.PNG)

4. In **Registrazione** cercare e selezionare la chiave di registrazione scaricata dall'insieme di credenziali.

    ![Registrazione](./media/site-recovery-vmware-to-azure/combined-wiz3.png)

5. In **Impostazioni Internet** specificare in che modo il provider che esegue il server di configurazione si connetterà ad Azure Site Recovery tramite Internet.

    - Per connettersi al proxy attualmente configurato nel computer, selezionare **Connetti con le impostazioni proxy esistenti**.
    - Per fare in modo che il provider si connetta direttamente, selezionare **Connetti direttamente senza un proxy**.
    - Se per il proxy esistente è necessaria l'autenticazione o si vuole usare un proxy personalizzato per la connessione del provider, selezionare **Connetti con le impostazioni proxy personalizzate**.
        - Se si usa un proxy personalizzato è necessario specificare l'indirizzo, la porta e le credenziali.
        - Se si usa un proxy, è necessario che gli URL descritti nei [prerequisiti](#configuration-server-prerequisites)siano già consentiti.

    ![Firewall](./media/site-recovery-vmware-to-azure/combined-wiz4.png)

6. In **Controllo dei prerequisiti** il programma di installazione esegue un controllo per assicurarsi che l'installazione possa essere eseguita. Se viene visualizzato un avviso relativo al **controllo della sincronizzazione ora globale**, verificare che l'ora del clock di sistema, nelle impostazioni di **Data e ora**, corrisponda al fuso orario.

    ![Prerequisiti](./media/site-recovery-vmware-to-azure/combined-wiz5.png)

7. In **Configurazione MySQL** creare le credenziali per l'accesso all'istanza del server MySQL che verrà installata.

    ![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz6.png)

8. In **Dettagli ambiente** specificare se si vuole eseguire la replica di VM VMware. In caso affermativo, il programma di installazione verifica quindi se è installato PowerCLI 6.0.

    ![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz7.png)

9. In **Percorso di installazione** specificare il percorso di installazione dei file binari e di archiviazione della cache. È possibile selezionare un'unità con almeno 5 GB di spazio di archiviazione disponibile, ma è consigliabile usare un'unità cache con almeno 600 GB di spazio disponibile.

    ![Percorso di installazione](./media/site-recovery-vmware-to-azure/combined-wiz8.png)

10. In **Selezione rete** specificare il listener, ovvero la scheda di rete e la porta SSL, in cui il server di configurazione dovrà inviare e ricevere i dati di replica. È possibile modificare la porta predefinita (9443). Oltre a questa porta, un server web che orchestra le operazioni di replica userà la porta 443. La porta 443 non deve essere usata per ricevere il traffico di replica.


    ![Selezione rete](./media/site-recovery-vmware-to-azure/combined-wiz9.png)



11.  Esaminare le informazioni nella pagina **Riepilogo** e fare clic su **Installa**. Al termine dell'installazione verrà generata una passphrase. Sarà necessaria quando si abilita la replica, quindi copiarla e conservarla in un luogo sicuro.

    ![Riepilogo](./media/site-recovery-vmware-to-azure/combined-wiz10.png)

12.  Al termine della registrazione il server viene visualizzato nel pannello **Impostazioni** > **Server** nell'insieme di credenziali.



#### <a name="run-setup-from-the-command-line"></a>Eseguire l'installazione dalla riga di comando

È possibile configurare il server di configurazione dalla riga di comando:

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Al termine dell'installazione, per completare la registrazione:

1. Avviare un'applicazione denominata "Microsoft Azure Recovery Services Shell" dal menu di avvio di Windows.
2. Nella finestra di comando visualizzata eseguire il set di comandi seguente per configurare le impostazioni del server proxy.

        PS C:\Windows\System32\> $pwd = ConvertTo-SecureString -String ProxyUserPassword
        PS C:\Windows\System32\> Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\username -ProxyPassword $pwd
        PS C:\Windows\System32\> net stop obengine.exe

Parametri

- /ServerMode: obbligatorio. Specifica se devono essere installati i server di configurazione e di elaborazione o solo il server di elaborazione. Valori di input: CS, PS.
- InstallLocation: obbligatorio. Cartella in cui sono installati i componenti.
- /MySQLCredsFilePath: Obbligatorio. Percorso file in cui sono archiviate le credenziali del server MySQL. Il file deve essere nel formato:
    - [MySQLCredentials]
    - MySQLRootPassword = "<Password>"
    - MySQLUserPassword = "<Password>"
- /VaultCredsFilePath: obbligatorio. Percorso del file di credenziali dell'insieme di credenziali.
- /EnvType: obbligatorio. Tipo di installazione. Valori: VMware, NonVMware.
- /PSIP e /CSIP. obbligatorio. Indirizzo IP del server di elaborazione e del server di configurazione.
- /PassphraseFilePath: obbligatorio. Percorso del file della passphrase.
- /BypassProxy: facoltativo. Specifica che il server di configurazione si connette ad Azure senza un proxy.
- /ProxySettingsFilePath: facoltativo. Impostazioni proxy, il proxy predefinito richiede l'autenticazione o un proxy personalizzato. Il file deve essere nel formato:
    - [ProxySettings]
    - ProxyAuthentication = "Sì/No"
    - Proxy IP = "Indirizzo IP>"
    - ProxyPort = "<Port>"
    - ProxyUserName="<User Name>"
    - ProxyPassword="<Password>"
- DataTransferSecurePort: facoltativo. Numero di porta da usare per i dati di replica.
- SkipSpaceCheck: facoltativo. Ignora la verifica dello spazio per la cache.
- AcceptThirdpartyEULA: obbligatorio. Il flag implica l'accettazione delle condizioni di licenza di terze parti.
- ShowThirdpartyEULA: obbligatorio. Visualizza le condizioni di licenza di terze parti. Se specificato come input, tutti gli altri parametri vengono ignorati.

### <a name="add-the-vmware-account-used-for-automatic-discovery"></a>Aggiungere l'account VMware usato per l'individuazione automatica

 Durante la preparazione per la distribuzione è stato [creato un account VMware](#prepare-an-account-for-automatic-discovery) che Site Recovery può usare per l'individuazione automatica. Aggiungere questo account come segue:

1. Aprire **CSPSConfigtool.exe**. È disponibile come collegamento sul desktop e si trova nella cartella [PERCORSO DI INSTALLAZIONE]\home\svsystems\bin.
2. Fare clic su **Gestisci account** > **Aggiungi account**.

    ![Aggiungi account](./media/site-recovery-vmware-to-azure/credentials1.png)

3. In **Dettagli dell'account** aggiungere l'account che verrà usato per l'individuazione automatica. Possono trascorrere 15 minuti o più prima che il nome dell'account venga visualizzato nel portale. Per aggiornarlo immediatamente, fare clic su **Server di configurazione** > nome del server > **Aggiorna server**.

    ![Dettagli](./media/site-recovery-vmware-to-azure/credentials2.png)

### <a name="connect-to-vsphere-hosts-and-vcenter-servers"></a>Connettersi agli host vSphere e ai server vCenter

Se si esegue la replica di macchine virtuali VMware, connettersi agli host vSphere e ai server vCenter.

1. Verificare che il server di configurazione abbia accesso alla rete per gli host vSphere e i server vCenter.
2. Fare clic su **Preparare l'infrastruttura** > **Origine**. In **Prepara origine** selezionare il server di configurazione e fare clic su **+vCenter** per aggiungere un host vSphere o un server vCenter.
3. In **Aggiungi vCenter** specificare un nome descrittivo per il server vCenter o l'host vSphere e specificare l'indirizzo IP o il nome di dominio completo del server. Lasciare la porta 443, a meno che i server VMware siano configurati per l'ascolto delle richieste su una porta diversa. Selezionare quindi l'account che verrà usato per connettersi al server VMware. Fare clic su **OK**.

    ![VMware](./media/site-recovery-vmware-to-azure/vmware-server.png)

    >[AZURE.NOTE] Se si aggiunge il server vCenter o l'host vSphere con un account che non ha privilegi di amministratore per il server vCenter o il server host, assicurarsi che questi privilegi siano abilitati per gli account: Datacenter, Datastore, Folder, Host, Network, Resource, Virtual machine, vSphere Distributed Switch. Il server vCenter deve avere anche il privilegio Storage views.

Site Recovery si connette al server VMware usando le impostazioni specificate e individua le macchine virtuali.

## <a name="step-3:-set-up-the-target-environment"></a>Passaggio 3: Configurare l'ambiente di destinazione

Verificare di avere un 'account di archiviazione per la replica e una rete di Azure a cui le macchine virtuali di Azure si connetteranno dopo il failover.

1.  Fare clic su **Preparare l'infrastruttura** > **Destinazione** e selezionare la sottoscrizione di Azure che si vuole usare.
2.  Specificare il modello di distribuzione da usare per le macchine virtuali dopo il failover.
3.  Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.

    ![Destinazione](./media/site-recovery-vmware-to-azure/gs-target.png)

4.  Per creare un account di archiviazione con Azure Resource Manager, se non è già stato fatto, fare clic su **+ Account di archiviazione** per eseguire l'operazione inline.  Nel pannello **Crea account di archiviazione** specificare il nome, il tipo, la sottoscrizione e la località dell'account. L'account deve trovarsi nella stessa località dell'insieme di credenziali di Servizi di ripristino.

    ![Archiviazione](./media/site-recovery-vmware-to-azure/gs-createstorage.png)

    Si noti che:

    - Per creare un account di archiviazione con il modello di distribuzione classica, usare il portale di Azure. [Altre informazioni](../storage/storage-create-storage-account-classic-portal.md)
    - Se si usa un account di archiviazione Premium per i dati replicati, è necessario configurare un account di archiviazione Standard aggiuntivo per l'archiviazione dei log di replica in cui vengono acquisite le modifiche in corso ai dati locali.

    > [AZURE.NOTE] La protezione per gli account di archiviazione Premium non è attualmente supportata in India centrale e India meridionale.

4.  Selezionare una rete di Azure. Per creare una rete con Azure Resource Manager, se non è già stato fatto, fare clic su **+Rete** per eseguire l'operazione inline. Nel pannello **Crea rete virtuale** specificare il nome, l'intervallo di indirizzi, i dettagli della subnet, la sottoscrizione e la località della rete. La rete deve trovarsi nella stessa località dell'insieme di credenziali di Servizi di ripristino.

    ![Rete](./media/site-recovery-vmware-to-azure/gs-createnetwork.png)

    Per creare una rete con il modello di distribuzione classica è necessario usare il portale di Azure. [Altre informazioni](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="step-4:-set-up-replication-settings"></a>Passaggio 4: Configurare le impostazioni di replica

1. Per creare nuovi criteri di replica, fare clic su **Preparare l'infrastruttura** > **Impostazioni della replica** > **+Crea e associa**.
2. In **Creare e associare i criteri** specificare il nome dei criteri.
3. In **Soglia RPO**specificare il limite per RPO. Quando la replica continua supera questo limite, verranno generati avvisi.
5. In **Conservazione del punto di ripristino**specificare la durata in ore dell'intervallo di conservazione per ogni punto di ripristino. I computer protetti possono essere ripristinati in qualsiasi punto all'interno di un intervallo. Per le macchine replicate nell'archiviazione Premium è supportato un intervallo di conservazione fino a 24 ore.
6. In **Frequenza snapshot coerenti con l'app**specificare la frequenza, in minuti, per la creazione di punti di ripristino contenenti snapshot coerenti con l'applicazione.
7. Quando si creano criteri di replica, per impostazione predefinita vengono creati automaticamente criterio corrispondenti per il failback. Ad esempio se i criteri di replica sono **rep-policy** i criteri di failback saranno **rep-policy-failback**. Questi criteri non vengono usati fino a quando non si avvia un failback.  
8. Fare clic su **OK** per creare i criteri.

    ![Criteri di replica](./media/site-recovery-vmware-to-azure/gs-replication2.png)

9. Quando si creano nuovi criteri, questi vengono associati automaticamente al server di configurazione. Fare clic su **OK**.

    ![Criteri di replica](./media/site-recovery-vmware-to-azure/gs-replication3.png)


## <a name="step-5:-capacity-planning"></a>Passaggio 5: Pianificazione della capacità

Dopo aver configurato l'infrastruttura di base è possibile passare alla pianificazione della capacità e valutare se sono necessarie altre risorse.

In Site Recovery è disponibile lo strumento Capacity Planner, che permettere di allocare le risorse appropriate all'ambiente di origine, i componenti di Site Recovery, la rete e l'archiviazione. Lo strumento di pianificazione può essere eseguito in modalità rapida, per ottenere stime basate su un numero medio di macchine virtuali, dischi e risorse di archiviazione, oppure in modalità dettagliata, in cui è necessario inserire le cifre a livello di carico di lavoro. Prima di iniziare è necessario:

- Raccogliere informazioni sull'ambiente di replica, incluse le macchine virtuali, i dischi per ogni macchina virtuale e le risorse di archiviazione per ogni disco.
- Stimare la frequenza di modifica giornaliera (varianza) prevista per i dati replicati. Per eseguire questa operazione, è possibile usare [vSphere Replication Capacity Planning Appliance](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance) .

1.  Fare clic su **Download** per scaricare lo strumento e quindi eseguirlo. [Vedere l'articolo](site-recovery-capacity-planner.md) fornito con lo strumento.
2.  Al termine scegliere **Yes** in **completamento della pianificazione della capacità**

    ![Pianificazione della capacità](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)

Nella tabella seguente sono raccolti alcuni punti che facilitano la pianificazione della capacità per questo scenario.


**Componente** | **Dettagli**
--- | --- | ---
**Replica** | **Frequenza di modifica giornaliera massima**: un computer protetto può usare un solo server di elaborazione e la frequenza di modifica giornaliera gestita da un singolo server di elaborazione può arrivare fino a 2 TB. La massima frequenza di modifica dei dati giornaliera supportata per una macchina protetta è quindi di 2 TB.<br/><br/> **Velocità effettiva massima**: una macchina replicata può appartenere a un account di archiviazione in Azure. Un account di archiviazione standard può gestire fino a 20.000 richieste al secondo ed è consigliabile fare in modo che il numero di IOPS in una macchina di origine rimanga 20.000. Se, ad esempio, si ha un computer di origine con 5 dischi e ogni disco genera 120 IOPS (con una dimensione pari a 8.000) nell'origine, il computer rientra nel limite di Azure pari a 500 IOPS per disco. Il numero di account di archiviazione richiesto è uguale al numero di IOPS di origine totali diviso 20.000.
**Server di configurazione** | Il server di configurazione deve poter gestire la capacità della frequenza di modifica giornaliera in tutti i carichi di lavoro in esecuzione nei computer protetti e avere a disposizione una larghezza di banda sufficiente per eseguire la replica continua dei dati nell'archiviazione di Azure.<br/><br/>  Come procedura consigliata, il server di configurazione deve trovarsi nella stessa rete e nello stesso segmento LAN dei computer da proteggere. Può trovarsi in una rete diversa, ma i computer da proteggere devono avere la visibilità di rete L3 per il server.<br/><br/>  Le dimensioni consigliate per il server di configurazione sono riepilogate nella tabella seguente.
**Server di elaborazione** | Per impostazione predefinita, il primo server di elaborazione viene installato nel server di configurazione. È possibile distribuire altri server di elaborazione per ridimensionare l'ambiente. Si noti che:<br/><br/>  Il server di elaborazione riceve i dati di replica da computer protetti e li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia prima di inviarli ad Azure. Il computer server di elaborazione deve avere risorse sufficienti per eseguire queste attività.<br/><br/>  Il server di elaborazione usa una cache basata su disco. È consigliabile usare un disco a parte per la cache di almeno 600 GB per gestire le modifiche ai dati archiviate in caso di colli di bottiglia o interruzioni della rete.

### <a name="size-recommendations-for-the-configuration-server"></a>Dimensioni consigliate per il server di configurazione

**CPU** | **Memoria** | **Dimensione disco cache** | **Frequenza di modifica dei dati** | **Computer protetti**
--- | --- | --- | --- | ---
8 vCPU (2 socket * 4 core a@ 2,5 GHz) | 16 GB | 300 GB | 500 GB o inferiore | Replicare meno di 100 computer.
12 vCPU (2 socket * 6 core a@ 2,5 GHz) | 18 GB | 600 GB | Da 500 GB a 1 TB | Replicare tra 100 e 150 computer.
16 vCPU (2 socket * 8 core a@ 2,5 GHz) | 32 GB | 1 TB | Da 1 TB a 2 TB | Replicare tra 150 e 200 computer.
Distribuire un altro server di elaborazione | | | Superiore a 2 TB | Distribuire server di elaborazione aggiuntivi se si esegue la replica di più di 200 computer o la frequenza di modifica dei dati giornaliera è superiore a 2 TB.

Dove:

- Ogni computer di origine è configurato con 3 dischi da 100 GB.
- La risorsa di archiviazione di benchmarking usata per le misurazioni del disco della cache è di 8 unità SAS a 10.000 RPM con RAID 10.

### <a name="size-recommendations-for-the-process-server"></a>Dimensioni consigliate per il server di elaborazione

Se è necessario proteggere più di 200 computer o la frequenza di modifica giornaliera è superiore a 2 TB, è possibile aggiungere altri server di elaborazione per gestire il carico di replica. Per aumentare il numero di istanze è possibile:

- Aumentare il numero di server di configurazione. Ad esempio, è possibile proteggere fino a 400 computer con due server di configurazione.
- Aggiungere altri server di elaborazione e usarli per gestire il traffico al posto del server di gestione o in aggiunta al server di configurazione.

Questa tabella descrive uno scenario in cui:

- Non si prevede di usare il server di configurazione come server di elaborazione.
- È stato configurato un server di elaborazione aggiuntivo.
- Le macchine virtuali protette sono state configurate per l'uso del server di elaborazione aggiuntivo.
- Ogni computer di origine protetto è configurato con tre dischi da 100 GB.

**Server di configurazione** | **Server di elaborazione aggiuntivo**| **Dimensione disco cache** | **Frequenza di modifica dei dati** | **Computer protetti**
--- | --- | --- | --- | ---
8 vCPU (2 socket * 4 core a@ 2,5 GHz), 16 GB di memoria | 4 vCPU (2 socket * 2 core a@ 2,5 GHz), 8 GB di memoria | 300 GB | 250 GB o inferiore | Replicare un massimo di 85 computer.
8 vCPU (2 socket * 4 core a@ 2,5 GHz), 16 GB di memoria | 8 vCPU (2 socket * 4 core a@ 2,5 GHz), 12 GB di memoria | 600 GB | Da 250 GB a 1 TB | Replicare tra 85 e 150 computer.
12 vCPU (2 socket * 6 core a@ 2,5 GHz), 18 GB di memoria | 12 vCPU (2 socket * 6 core a@ 2,5 GHz), 24 GB di memoria | 1 TB | Da 1 TB a 2 TB | Replicare tra 150 e 225 computer.


Il modo in cui i server vengono adattati dipende dalle preferenze personali per il modello di scalabilità orizzontale o quello verticale.  L'aumento delle prestazioni si ottiene distribuendo alcuni server di configurazione e di elaborazione avanzati, mentre l'aumento del numero di istanze si ottiene distribuendo più server con meno risorse. Ad esempio, per proteggere 220 computer è possibile eseguire una di queste operazioni:

- Configurare il server di configurazione con 12 vCPU e 18 GB di memoria e un server di elaborazione aggiuntivo con 12 vCPU e 24 GB di memoria. Configurare quindi i computer protetti perché usino solo il server di elaborazione aggiuntivo.
- In alternativa, configurare due server di configurazione, 2 x 8 vCPU e 16 GB di memoria RAM, e due server di elaborazione aggiuntivi, 1 x 8 vCPU e 1 x 4 vCPU per gestire 135 + 85 (220) computer. Configurare quindi i computer protetti perché usino solo i server di elaborazione aggiuntivi.

[Seguire queste istruzioni](#deploy-additional-process-servers) per configurare un server di elaborazione aggiuntivo.

### <a name="network-bandwidth-considerations"></a>Considerazioni sulla larghezza di banda di rete

Lo strumento Capacity Planner può essere usato per calcolare la larghezza di banda necessaria per la replica iniziale e poi quella differenziale. Per controllare la quantità di larghezza di banda usata per la replica è possibile scegliere una delle opzioni seguenti.

- **Limitare la larghezza di banda**: il traffico VMware che viene replicato in Azure passa attraverso un server di elaborazione specifico. È possibile limitare la larghezza di banda nei computer eseguiti come server di elaborazione.
- **Influire sulla larghezza di banda**: è possibile influire sulla larghezza di banda usata per la replica tramite una coppia di chiavi del Registro di sistema:
    - Il valore del Registro di sistema **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\UploadThreadsPerVM** specifica il numero di thread usati per il trasferimento dati di un disco, durante la replica iniziale o differenziale. Un valore più elevato aumenta la larghezza di banda di rete usata per la replica.
    - Il valore **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DownloadThreadsPerVM** specifica il numero di thread usati per il trasferimento dati durante il failback.

#### <a name="throttle-bandwidth"></a>Limitare la larghezza di banda

1. Aprire lo snap-in di MMC Backup di Microsoft Azure nel computer che funge da server di elaborazione. Per impostazione predefinita, un collegamento a Backup di Microsoft Azure è disponibile sul desktop oppure in: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Nello snap-in fare clic su **Modifica proprietà**.

    ![Limitare la larghezza di banda](./media/site-recovery-vmware-to-azure/throttle1.png)

3. Nella scheda **Limitazione larghezza di banda rete** selezionare **Abilita la limitazione all'uso della larghezza di banda Internet per le operazioni di backup** e impostare i limiti per le ore lavorative e non lavorative. Gli intervalli validi sono compresi tra 512 Kbps e 102 Mbps al secondo.

    ![Limitare la larghezza di banda](./media/site-recovery-vmware-to-azure/throttle2.png)

È anche possibile usare il cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) per impostare la limitazione. Di seguito è riportato un esempio:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica che non è necessaria alcuna limitazione.


#### <a name="influence-network-bandwidth"></a>Influire sulla larghezza di banda di rete

1. Nel Registro di sistema passare a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
    - Per intervenire sul traffico della larghezza di banda in un disco di replica, modificare il valore di **UploadThreadsPerVM**oppure creare la chiave, se non esiste.
    - Per intervenire sulla larghezza di banda per il traffico di failback da Azure, modificare il valore di **DownloadThreadsPerVM**.
2. Il valore predefinito è 4. In una rete con provisioning eccessivo è necessario modificare i valori predefiniti di queste chiavi del Registro di sistema. Il valore massimo è 32. Monitorare il traffico per ottimizzare il valore.

## <a name="step-6:-replicate-applications"></a>Passaggio 6: Replicare applicazioni

Verificare che i computer da replicare siano preparati per l'installazione del servizio Mobility e quindi abilitare la replica.

### <a name="install-the-mobility-service"></a>Installare il servizio Mobility

Per abilitare la protezione per le macchine virtuali e i server fisici è necessario prima di tutto installare il servizio Mobility. È possibile procedere in due modi:

- **Push del server di elaborazione**: quando si abilita la replica in un computer, effettuare il push e installare il componente del servizio Mobility dal server di elaborazione. Si noti che l'installazione push non viene eseguita se i computer eseguono già una versione aggiornata del componente.
- **Push aziendale**: installare automaticamente il componente usando il processo push aziendale, ad esempio WSUS o System Center Configuration Manager. Configurare il server di configurazione prima di eseguire questa operazione.
- **Installazione manuale**: installare il componente manualmente in ogni computer da replicare. Configurare il server di configurazione prima di eseguire questa operazione.


#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Preparare il push automatico nei computer Windows

Di seguito viene illustrato come preparare i computer Windows in modo che il servizio Mobility possa essere installato automaticamente dal server di elaborazione.

1.  Creare un account utilizzabile dal server di elaborazione per accedere al computer. L'account deve avere privilegi di amministratore (locale o dominio) e viene usato solo per l'installazione push.

    >[AZURE.NOTE] Se non si usa un account di dominio, è necessario disabilitare il Controllo dell'accesso utente remoto nel computer locale. A questo scopo, aggiungere la voce DWORD entry LocalAccountTokenFilterPolicy con un valore di 1 nel Registro di sistema in HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. Per aggiungere la voce del Registro di sistema da un'interfaccia della riga di comando, digitare **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.

2.  Nell'istanza di Windows Firewall del computer da proteggere selezionare **Consenti app o funzionalità attraverso Windows Firewall**. Abilitare **Condivisione file e stampanti** e **Strumentazione gestione Windows**. Per i computer appartenenti a un dominio, è possibile configurare le impostazioni del firewall con un oggetto Criteri di gruppo.

    ![Impostazioni del firewall](./media/site-recovery-vmware-to-azure/mobility1.png)

2. Aggiungere l'account che è stato creato:

    - Aprire **cspsconfigtool**. È disponibile come collegamento sul desktop e si trova nella cartella [PERCORSO DI INSTALLAZIONE]\home\svsystems\bin.
    - Nella scheda **Gestisci account** fare clic su **Aggiungi account**.
    - Aggiungere l'account che è stato creato. Dopo aver aggiunto l'account, per abilitare la replica per un computer sarà necessario fornire le credenziali.


#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Preparare il push automatico nei server Linux

1.  Verificare che il computer Linux da proteggere sia supportato, come descritto in [Prerequisiti dei computer protetti](#protected-machine-prerequisites). Verificare che sia presente la connettività di rete tra il computer Linux e il server di elaborazione.

2.  Creare un account utilizzabile dal server di elaborazione per accedere al computer. L'account deve essere un utente root nel server Linux di origine e viene usato solo per l'installazione push.

    - Aprire **cspsconfigtool**. È disponibile come collegamento sul desktop e si trova nella cartella [PERCORSO DI INSTALLAZIONE]\home\svsystems\bin.
    - Nella scheda **Gestisci account** fare clic su **Aggiungi account**.
    - Aggiungere l'account che è stato creato. Dopo aver aggiunto l'account, per abilitare la replica per un computer sarà necessario fornire le credenziali.

3.  Assicurarsi che il file /etc/hosts nel server Linux di origine contenga le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede di rete.
4.  Installare i pacchetti openssh, openssh-server, openssl più recenti nel computer da replicare.
5.  Assicurarsi che SSH sia abilitato e in esecuzione sulla porta 22.
6.  Abilitare il sottosistema SFTP e l'autenticazione della password nel file sshd_config, come segue:

    - Accedere come utente ROOT.
    - Nel file /etc/ssh/sshd_config trovare la riga che inizia con **PasswordAuthentication**.
    - Rimuovere il commento dalla riga e modificare il valore da **no** a **yes**.
    - Trovare la riga che inizia con **Subsystem** e rimuovere il commento.

        ![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)


#### <a name="install-the-mobility-service-manually"></a>Installare manualmente il servizio Mobility

I programmi di installazione sono disponibili nel server di elaborazione in **C:\Programmi (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

Sistema operativo di origine | File di installazione del servizio Mobility
--- | ---
Windows Server (solo 64 bit) | Microsoft-ASR_UA_9.*.0.0_Windows_* release.exe
CentOS 6.5, 6.6, 6.7 (solo 64 bit) | Microsoft-ASR_UA_9.*.0.0_RHEL6-64_*release.tar.gz
CentOS 7.0, 7.1, 7.2 (solo 64 bit) | Microsoft-ASR_UA_9.*.0.0_RHEL7-64_*release.tar.gz
Red Had Enterprise Linux 6.7 (solo 64 bit) | Microsoft-ASR_UA_9.*.0.0_RHEL6-64_*release.tar.gz
Red Had Enterprise Linux 7.1, 7.2 (solo 64 bit) | Microsoft-ASR_UA_9.*.0.0_RHEL7-64_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3 (solo 64 bit) | Microsoft-ASR_UA_9.*.0.0_SLES11-SP3-64_*release.tar.gz
Oracle Enterprise Linux 6.4, 6.5 (solo 64 bit) | Microsoft-ASR_UA_9.*.0.0_OL6-64_*release.tar.gz


#### <a name="install-manually-on-a-windows-server"></a>Installare manualmente in un server Windows


1. Scaricare ed eseguire il relativo programma di installazione.
2. In **Prima di iniziare** selezionare **Servizio Mobility**.

    ![Servizio Mobility](./media/site-recovery-vmware-to-azure/mobility3.png)

3. In **Dettagli del nuovo server di configurazione** specificare l'indirizzo IP del server di configurazione e la passphrase generata durante l'esecuzione dell'Installazione unificata. Per recuperare la passphrase, eseguire **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –n** nel server di configurazione.

    ![Servizio Mobility](./media/site-recovery-vmware-to-azure/mobility6.png)

4. In **Percorso di installazione** lasciare l'impostazione predefinita e fare clic su **Avanti** per avviare l'installazione.
5. Monitorare lo stato dell'installazione in **Stato dell'installazione** e, se richiesto, riavviare il computer. Dopo l'installazione del servizio, potrebbero essere necessari circa 15 minuti per aggiornare lo stato nel portale.

È possibile eseguire l'installazione anche dalla riga di comando:

UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]

Dove:

- /Role. Obbligatorio. Specifica se deve essere installato il servizio Mobility.
- /InstallLocation. Obbligatorio. Specifica dove installare il servizio.
- /PassphraseFilePath. Obbligatorio. Passphrase del server di configurazione.
- /LogFilePath. Obbligatorio. Percorso dei file di installazione dei log.

#### <a name="uninstall-mobility-service-manually"></a>Disinstallare manualmente il servizio Mobility

Il servizio Mobility può essere disinstallato tramite Installazione applicazioni del Pannello di controllo o la riga di comando.

Il comando per disinstallare il servizio Mobility tramite la riga di comando è

    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}


#### <a name="install-manually-on-a-linux-server:"></a>Installare manualmente in un server Linux

1. Copiare l'archivio TAR appropriato in base alla tabella precedente nel computer Linux da replicare.
2. Aprire un programma shell ed estrarre l'archivio TAR compresso in un percorso locale eseguendo: `tar -xvzf Microsoft-ASR_UA_9.3.0.0*`
3. Creare un file passphrase.txt nella directory locale in cui è stato estratto il contenuto dell'archivio TAR. A questo scopo, copiare la passphrase da C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase nel server di configurazione e salvarla nel file passphrase.txt eseguendo *`echo <passphrase> >passphrase.txt`* nella shell.
4. Eseguire *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*per installare il servizio Mobility.
5. Specificare l'indirizzo IP interno del server di configurazione e assicurarsi che la porta 443 sia selezionata. Dopo l'installazione del servizio, potrebbero essere necessari circa 15 minuti per aggiornare lo stato nel portale.

**È possibile eseguire l'installazione anche dalla riga di comando**:

1. Copiare la passphrase da C:\Programmi (x86)\InMage Systems\private\connection nel server di configurazione e salvarla come "passphrase.txt" nel server di configurazione. Quindi eseguire questi comandi: In questo esempio l'indirizzo IP del server di configurazione è 104.40.75.37 e la porta HTTPS deve essere la 443:

Per eseguire l'installazione in un server di produzione:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Per eseguire l'installazione nel server di destinazione master:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


### <a name="enable-replication"></a>Abilitare la replica

#### <a name="before-you-start"></a>Prima di iniziare

Se si esegue la replica di macchine virtuali VMware, tenere presente quanto segue:

- Le macchine virtuali VMware vengono rilevate ogni 15 minuti e possono essere necessari più di 15 minuti perché vengano visualizzate nel portale dopo l'individuazione. Allo stesso modo, l'individuazione può richiedere anche più di 15 minuti quando si aggiunge un nuovo host vSphere o un server vCenter.
- Anche per le modifiche dell'ambiente nella macchina virtuale, ad esempio l'installazione degli strumenti VMware, possono essere necessari oltre 15 minuti per l'aggiornamento nel portale.
- È possibile controllare l'ora dell'ultima individuazione di VM VMware nel campo **Ora ultimo contatto** relativo al server vCenter o all'host vSphere del pannello **Server di configurazione**.
- Per aggiungere computer per la replica senza attendere l'individuazione pianificata, evidenziare il server di configurazione, senza selezionarlo, e scegliere il pulsante **Aggiorna** .
- Quando si abilita la replica, se il computer è preparato, il server di elaborazione installa automaticamente il servizio Mobility nel computer.

#### <a name="exclude-disks-from-replication"></a>Escludere dischi dalla replica

Quando si abilita la replica, per impostazione predefinita tutti i dischi in un computer vengono replicati. È possibile escludere dischi dalla replica. Ad esempio, è possibile evitare di replicare i dischi con dati temporanei o dati che vengono aggiornati ogni volta che un computer o un'applicazione viene riavviata, come pagefile.sys o tempdb di SQL Server. Per escludere i dischi, si noti quanto segue:

- È possibile escludere solo i dischi in cui è già installato il servizio Mobility. È necessario [installare manualmente il servizio Mobility](#install-the-mobility-service-manually) perché viene installato solo tramite il meccanismo di push dopo l'abilitazione della replica.
- Solo i dischi di base possono essere esclusi dalla replica. Non è possibile escludere i dischi del sistema operativo o dinamici.
- Dopo aver abilitato la replica, non è più possibile aggiungere o rimuovere dischi da replicare. Se si vuole aggiungere o escludere un disco, è necessario disabilitare la protezione per il computer e quindi riabilitarla.
- Se si esclude un disco necessario per il funzionamento di un'applicazione, dopo il failover in Azure è necessario crearlo manualmente in Azure per consentire l'esecuzione dell'applicazione replicata. In alternativa è possibile integrare Automazione di Azure in un piano di ripristino per creare il disco durante il failover del computer.
- Per i dischi creati manualmente in Azure verrà eseguito il failback. Ad esempio, se si esegue il failover di tre dischi e se ne creano due direttamente in Azure, verrà eseguito il failback di tutti e cinque. Non è possibile escludere i dischi creati manualmente dall'operazione di failback.

**Per abilitare la replica, procedere come descritto di seguito**.

1. Fare clic su **Passaggio 2: Eseguire la replica dell'applicazione** > **Origine**. Dopo aver abilitato la replica per la prima volta, è necessario fare clic su **+Replica** nell'insieme di credenziali per abilitare la replica per altri computer.
2. Nel pannello **Origine** > **Origine** selezionare il server di configurazione.
3. In **Tipo di computer** selezionare **Macchine virtuali** o **Computer fisici**.
4. In **vCenter/vSphere Hypervisor** selezionare il server vCenter che gestisce l'host di vSphere oppure selezionare l'host. Questa impostazione non si applica se si esegue la replica di computer fisici.
5. Selezionare il server di elaborazione. Se ancora non sono stati creati server di elaborazione aggiuntivi, questo sarà il nome del server di configurazione. Fare quindi clic su **OK**.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. In **Destinazione** selezionare la sottoscrizione dell'insieme di credenziali e in **Modello di distribuzione post-failover** selezionare il modello di failover da usare in Azure, in modalità classica o Resource Manager, dopo il failover.
7. Selezionare l'account di archiviazione di Azure da usare per la replica dei dati. Si noti che:

    - È possibile selezionare un account di archiviazione Standard o Premium. Se si seleziona un account Premium, si dovrà specificare un account di archiviazione Standard aggiuntivo per i log di replica in corso. L'account deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino.
    - Per usare un account di archiviazione diverso da quelli disponibili, è possibile [crearne uno](#set-up-an-azure-storage-account). Per creare un account di archiviazione con il modello di distribuzione Azure Resource Manager, fare clic su **Crea nuovo**. Per creare un account di archiviazione con il modello di distribuzione classica, usare il [portale di Azure](../storage/storage-create-storage-account-classic-portal.md).

8. Selezionare la rete di Azure e la subnet a cui dovranno connettersi le macchine virtuali di Azure attivate dopo il failover. La rete deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino. Scegliere **Configurare ora per le macchine virtuali selezionate** per applicare le impostazioni di rete a tutti i computer selezionati per la protezione. Scegliere **Configurare in seguito** per selezionare la rete di Azure per ogni computer. Se non è disponibile una rete, sarà necessario [crearla](#set-up-an-azure-network). Per creare una rete con il modello di distribuzione Azure Resource Manager, fare clic su **Crea nuovo**. Per creare una rete con il modello di distribuzione classica, usare il [portale di Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Selezionare una subnet, se applicabile. Fare quindi clic su **OK**.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/enable-replication3.png)

9. In **Macchine virtuali** > **Seleziona macchine virtuali** fare clic per selezionare le macchine virtuali da replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Fare quindi clic su **OK**.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/enable-replication5.png)

10. In **Proprietà** > **Configura proprietà**selezionare l'account che verrà usato dal server di elaborazione per installare automaticamente il servizio Mobility nel computer. Per impostazione predefinita, vengono replicati tutti i dischi. Fare clic su **Tutti i dischi** e deselezionare i dischi da non replicare. Fare quindi clic su **OK**. È possibile impostare proprietà aggiuntive in un secondo momento.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/enable-replication6.png)

11. In **Impostazioni della replica** > **Configura impostazioni di replica** verificare che siano selezionati i criteri di replica corretti. È possibile modificare le impostazioni dei criteri di replica in **Impostazioni** > **Criteri di replica** > nome dei criteri > **Modifica impostazioni**. Le modifiche apportate ai criteri saranno applicate ai computer nuovi e in fase di replica.

12. Abilitare la **Coerenza tra più VM** per raccogliere le macchine in un gruppo di replica e specificare un nome per il gruppo. Fare quindi clic su **OK**. Si noti che:

    - Le macchine virtuali in un gruppo di replica vengono replicate insieme hanno punti di ripristino condivisi coerenti con l'arresto anomalo del sistema e coerenti con l'app quando si esegue il failover.
    - È consigliabile raggruppare le macchine virtuali e i server fisici in modo da rispecchiare i carichi di lavoro. L'abilitazione della coerenza di più macchine virtuali può influire sulle prestazioni del carico di lavoro e deve essere utilizzata solo se i computer eseguono stesso carico di lavoro ed è necessaria la coerenza.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/enable-replication7.png)

13. Fare clic su **Abilita la replica**. È possibile tenere traccia dello stato del processo **Abilita protezione** in **Impostazioni** > **Processi** > **Processi di Site Recovery**. Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.

> [AZURE.NOTE] Se il computer è pronto per l'installazione push, il componente servizio Mobility verrà installato quando la protezione viene abilitata. Una volta installato il componente nel computer, un processo di protezione viene avviato e non riesce. Dopo l'errore, è necessario riavviare manualmente tutti i computer. Dopo il riavvio, il processo di protezione viene avviato nuovamente e viene eseguita la replica iniziale.

### <a name="view-and-manage-vm-properties"></a>Visualizzare e gestire le proprietà della macchina virtuale

È consigliabile verificare le proprietà del computer di origine. Tenere presente che il nome della VM di Azure deve essere conforme ai [requisiti delle macchine virtuali di Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

1. Fare clic su **Impostazioni** > **Elementi replicati** e selezionare il computer. Il pannello **Informazioni di base** visualizza informazioni sulle impostazioni e sullo stato dei computer.

2. In **Proprietà** sono disponibili le informazioni su replica e failover per la VM.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/test-failover2.png)

3. In **Calcolo e rete** > **Proprietà di calcolo** è possibile specificare le dimensioni di destinazione e il nome della VM di Azure. Se necessario, modificare il nome in modo che sia conforme ai requisiti di Azure.
È anche possibile visualizzare e aggiungere le informazioni sulla rete di destinazione, la subnet e l'indirizzo IP che verranno assegnati alla macchina virtuale di Azure. Tenere presente quanto segue:

    - È possibile impostare l'indirizzo IP di destinazione. Se non si specifica un indirizzo, il computer di cui è stato eseguito il failover usa DHCP. Se si imposta un indirizzo che non è disponibile al momento del failover, il failover non riesce. Se l'indirizzo è disponibile nella rete di failover di test, è possibile usare lo stesso indirizzo IP di destinazione per il failover di test.
    - Il numero di schede di rete dipende dalle dimensioni specificate per la macchina virtuale di destinazione, come illustrato di seguito:

        - Se il numero di schede di rete nella macchina di origine è minore o uguale al numero di schede consentite per la macchina di destinazione, la destinazione avrà lo stesso numero di schede dell’origine.
        - Se il numero di schede per la macchina virtuale di origine supera il numero consentito per le dimensioni di destinazione, verrà utilizzata la dimensione di destinazione massima.
        - Ad esempio, se una macchina di origine dispone di due schede di rete e le dimensioni della macchina di destinazione ne supportano quattro, la macchina di destinazione avrà due schede. Se la macchina di origine dispone di due schede ma le dimensioni di destinazione supportate ne consentono solo una, la macchina di destinazione avrà una sola scheda.     
    - Se la macchina virtuale ha più schede di rete, si connetteranno tutte alla stessa rete.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/test-failover4.png)

4. In **Dischi** è possibile visualizzare il sistema operativo e i dischi dati della VM che verranno replicati.


## <a name="step-7:-test-the-deployment"></a>Passaggio 7: Testare la distribuzione

Per testare la distribuzione è possibile eseguire un failover di test per una singola macchina virtuale o un piano di ripristino che contenga una o più macchine virtuali.


### <a name="prepare-for-failover"></a>Preparare il failover

- Per eseguire un failover di test, è consigliabile creare una nuova rete di Azure isolata dalla rete di Azure di produzione, ovvero il comportamento predefinito quando si crea una nuova rete in Azure. [Altre informazioni](site-recovery-failover.md#run-a-test-failover) sull'esecuzione dei failover di test.
- Per ottenere prestazioni ottimali quando si esegue un failover in Azure, installare l'agente di Azure nel computer protetto. Questo consente un avvio più veloce e facilita la risoluzione dei problemi. Installare l'agente [Linux](https://github.com/Azure/WALinuxAgent) o [Windows](http://go.microsoft.com/fwlink/?LinkID=394789).
- Per testare completamente la distribuzione è necessario che l'infrastruttura per il computer replicato funzioni come previsto. Per testare Active Directory e DNS è possibile creare una macchina virtuale come controller di dominio con DNS ed eseguirne la replica in Azure usando Azure Site Recovery. Per altre informazioni, vedere le [considerazioni sul failover di test di Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Assicurarsi che il server di configurazione sia in esecuzione. In caso contrario, il failover avrà esito negativo.
- Se sono stati esclusi dischi dalla replica, potrebbe essere necessario creare questi dischi manualmente in Azure dopo il failover in modo che l'applicazione venga eseguita come previsto.
- Per eseguire un failover non pianificato anziché un failover di test, tenere presente quanto segue:

    - È consigliabile arrestare i computer primari prima di eseguire un failover non pianificato. Questo permette di evitare che il computer di origine e quello di replica siano in esecuzione nello stesso momento. Se si esegue la replica di macchine virtuali VMware, è possibile specificare che Site Recovery dovrà arrestare le macchine di origine nel modo migliore possibile. L'esito positivo di questa operazione dipende dallo stato del sito primario. Per la replica di server fisici questa opzione non è disponibile.
    - Quando si esegue un failover non pianificato, la replica dei dati dai computer primari viene arrestata per evitare il trasferimento di dati differenziali a failover iniziato. Se si esegue un failover non pianificato in un piano di ripristino, verrà eseguito fino al completamento anche se si verifica un errore.

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparare la connessione alle macchine virtuali di Azure dopo il failover

Per connettersi alle macchine virtuali di Azure con RDP dopo il failover, seguire questa procedura:

**Nel computer locale prima del failover**:

- Per l'accesso tramite Internet, abilitare RDP e assicurarsi che vengano aggiunte regole TCP e UDP per **Internet pubblico** e che il protocollo RDP sia consentito in **Windows Firewall** -> **App e funzionalità consentite** per tutti i profili.
- Per l'accesso tramite una connessione da sito a sito, abilitare RDP nel computer e assicurarsi che il protocollo RDP sia consentito in **Windows Firewall** -> **App e funzionalità consentite** per le reti di **dominio** e **private**.
- Installare l' [agente della VM di Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) nel computer locale.
- [Installare manualmente il servizio Mobility](#install-the-mobility-service-manually) nei computer invece di usare il server di elaborazione per effettuare automaticamente il push del servizio. Questo avviene perché l'installazione push si verifica solo dopo che il computer è abilitato per la replica.
- Verificare che il criterio SAN del sistema operativo sia impostato su OnlineAll. [Altre informazioni]( https://support.microsoft.com/kb/3031135)
- Disabilitare il servizio IPSec prima di eseguire il failover.

**Nella VM di Azure dopo il failover**:

- Aggiungere un endpoint pubblico per il protocollo RDP (porta 3389) e specificare le credenziali per l'account di accesso.
- Assicurarsi che non siano presenti criteri di dominio che impediscono la connessione a una macchina virtuale tramite un indirizzo pubblico.
- Provare a connettersi. Se non è possibile connettersi verificare che la macchina virtuale sia in esecuzione. Per altri suggerimenti sulla risoluzione dei problemi, vedere questo [articolo](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


Per accedere a una macchina virtuale di Azure che esegue Linux dopo il failover con un client Secure Shell (ssh), seguire questa procedura:

**Nel computer locale prima del failover**:

- Assicurarsi che il servizio Secure Shell nella macchina virtuale di Azure sia impostato per l'avvio automatico all'avvio del sistema.
- Verificare che le regole firewall accettino la connessione SSH.

**Nella VM di Azure dopo il failover**:

- Le regole del gruppo di sicurezza di rete nella macchina virtuale sottoposta a failover e nella subnet di Azure a cui è connessa devono consentire le connessioni in ingresso alla porta SSH.
- È necessario creare un endpoint pubblico per consentire le connessioni in ingresso sulla porta SSH, che per impostazione predefinita è la porta TCP 22.
- Se la macchina virtuale è accessibile tramite una connessione VPN, Express Route o VPN da sito a sito, il client può essere usato per connettersi direttamente alla macchina virtuale tramite SSH.

**Nella VM Windows/Linux di Azure dopo il failover**:

Se è disponibile un gruppo di sicurezza di rete associato alla macchina virtuale o alla subnet a cui appartiene il computer, assicurarsi che il gruppo di sicurezza di rete abbia una regola in uscita che consente HTTP/HTTPS. Assicurarsi anche che il DNS della rete alla quale viene eseguito il failover della macchina virtuale sia configurato correttamente. In caso contrario potrebbe verificarsi il timeout con l'errore: "PreFailoverWorkflow task WaitForScriptExecutionTask timed out" (Timeout di WaitForScriptExecutionTask dell'attività PreFailoverWorkflow). Per comprendere questo in dettaglio, vedere la sezione sul ripristino nella [Guida al monitoraggio e alla risoluzione dei problemi](site-recovery-monitoring-and-troubleshooting.md#recovery).

## <a name="run-a-test-failover"></a>Eseguire un failover di test

1. Per eseguire il failover di una singola macchina, in **Impostazioni** > **Elementi replicati** fare clic sulla VM > sull'icona ** +Failover di test**.

    ![Failover di test](./media/site-recovery-vmware-to-azure/test-failover1.png)

2. Per eseguire il failover di un piano di ripristino, in **Impostazioni** > **Piani di ripristino** fare clic con il pulsante destro del mouse sul piano e quindi scegliere **Failover di test**. Per creare un piano di ripristino, [seguire queste istruzioni](site-recovery-create-recovery-plans.md).

3. In **Failover di test** selezionare la rete di Azure a cui dovranno connettersi le VM di Azure dopo il failover.
4. Fare clic su **OK** per iniziare il failover. Per tenere traccia dello stato del processo, fare clic sulla VM per visualizzarne le proprietà oppure fare clic sul processo **Failover di test** nel nome dell'insieme di credenziali > **Impostazioni** > **Processi** > **Processi di Site Recovery**.
5. Quando il failover raggiunge la fase **Completa test** , seguire questa procedura:

    1. Visualizzare la macchina virtuale di replica nel portale di Azure. Verificare che la macchina virtuale venga avviata correttamente.
    2. Se è stato impostato l'accesso alle macchine virtuali dalla rete locale, è possibile inizializzare una Connessione Desktop remoto alla macchina virtuale.
    3. Fare clic su **Completa test** per portarlo a termine.

        ![Failover di test](./media/site-recovery-vmware-to-azure/test-failover6.png)


    4. Fare clic su **Note** per registrare e salvare eventuali commenti associati al failover di test.
    5. Fare clic su **Failover di test completato** per pulire automaticamente l'ambiente di test. Al termine, lo stato del failover di test indicherà che l'operazione è **completata** .
    6.  A questo punto, eventuali macchine virtuali o elementi creati automaticamente da Site Recovery durante il failover di test vengono eliminati. Gli elementi aggiuntivi creati per il failover di test non vengono eliminati.

    > [AZURE.NOTE] Se un failover di test continua per più di due settimane, ne viene forzato il completamento.


6. Al termine del failover sarà possibile visualizzare la macchina virtuale di Azure di replica in **Macchine virtuali** nel portale di Azure. Assicurarsi che la macchina virtuale sia delle dimensioni appropriate, che sia connessa alla rete giusta e che sia in esecuzione.
7. Se sono state [preparate le connessioni dopo il failover](#prepare-to-connect-to-azure-vms-after-failover) , sarà possibile connettersi alla VM di Azure.

## <a name="monitor-your-deployment"></a>Monitorare la distribuzione

Per monitorare le impostazioni di configurazione, lo stato e l'integrità della distribuzione di Site Recovery, seguire questa procedura:

1. Fare clic sul nome dell'insieme di credenziali per accedere al dashboard **Informazioni di base** . In questo dashboard è possibile visualizzare i processi di Site Recovery, lo stato della replica, i piani di ripristino, l'integrità del server e gli eventi.  Il dashboard Informazioni di base può essere personalizzato con i riquadri e i layout più utili all'utente, incluso lo stato degli insiemi di credenziali di Backup e di Site Recovery.<br>
![Informazioni di base](./media/site-recovery-vmware-to-azure/essentials.png)

2. Nel riquadro **Integrità** è possibile monitorare i server VMM o di configurazione del sito in cui si verifica il problema e gli eventi generati da Site Recovery nelle ultime 24 ore.
3. È possibile gestire e monitorare la replica nei riquadri **Elementi replicati**, **Piani di ripristino** e **Processi di Site Recovery**. Per eseguire il drill-down dei processi, accedere a **Impostazioni** -> **Processi** -> **Processi di Site Recovery**.


## <a name="deploy-additional-process-servers"></a>Distribuire server di elaborazione aggiuntivi

Se è necessario ridimensionare la distribuzione oltre 200 computer di origine oppure la varianza totale giornaliera è superiore a 2 TB, sono necessari server di elaborazione aggiuntivi per gestire il volume di traffico.

Vedere [Dimensioni consigliate per il server di elaborazione](#size-recommendations-for-the-process-server) e quindi seguire queste istruzioni per configurare il server di elaborazione. Dopo aver configurato il server è possibile eseguire la migrazione dei computer di origine per usarlo.

### <a name="install-an-additional-process-server"></a>Installare un server di elaborazione aggiuntivo

1. In **Impostazioni** > **Server di Site Recovery** fare clic sul server di configurazione >**Server di elaborazione**.

    ![Aggiungere il server di elaborazione](./media/site-recovery-vmware-to-azure/migrate-ps1.png)

2. In **Server Type** click **Server di elaborazione (locale)**.

    ![Aggiungere il server di elaborazione](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

3. Scaricare il file di installazione per l'Installazione unificata di Site Recovery ed eseguirlo per installare il server di elaborazione e registrarlo nell'insieme di credenziali.
4. In **Prima di iniziare** selezionare **Add additional process servers to scale out deployment** (Aggiungere server di elaborazione per aumentare le istanze di distribuzione).
5. Completare la procedura guidata come per la [configurazione](#step-2-set-up-the-source-environment) del server di configurazione.

    ![Aggiungere il server di elaborazione](./media/site-recovery-vmware-to-azure/add-ps1.png)

6. In **Dettagli del server di configurazione** specificare l'indirizzo IP del server di configurazione e la passphrase. Per ottenere la passphrase, eseguire **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –n** nel server di configurazione.

    ![Aggiungere il server di elaborazione](./media/site-recovery-vmware-to-azure/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Eseguire la migrazione dei computer per usare il nuovo server di elaborazione

1. In **Impostazioni** > **Server di Site Recovery** fare clic sul server di configurazione e quindi espandere **Server di elaborazione**.

    ![Aggiornare il server di elaborazione](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

2. Fare clic con il pulsante destro del mouse sul server di elaborazione corrente e scegliere **Passa a**.

    ![Aggiornare il server di elaborazione](./media/site-recovery-vmware-to-azure/migrate-ps3.png)

3. In **Selezionare il server di elaborazione di destinazione** selezionare il nuovo server di elaborazione che si vuole usare e quindi selezionare le macchine virtuali che saranno gestite dal nuovo server di elaborazione. Fare clic sull'icona informazioni per ottenere informazioni sul server. Per consentire di prendere le decisioni relative al carico, viene visualizzato lo spazio medio necessario per replicare ogni macchina virtuale selezionata nel nuovo server di elaborazione. Fare clic sul segno di spunta per avviare la replica nel nuovo server di elaborazione.

## <a name="vmware-account-permissions"></a>Autorizzazioni dell'account VMware

Il server di elaborazione può rilevare automaticamente le macchine virtuali in un server vCenter. Per eseguire l'individuazione automatica è necessario [definire un ruolo (Azure_Site_Recovery)](#prepare-an-account-for-automatic-discovery) per consentire a Site Recovery di accedere al server VMware. Si noti che per eseguire la sola migrazione di computer VMware in Azure, senza eseguire il failback da Azure, è sufficiente definire un ruolo di sola lettura. Nella tabella seguente sono riepilogate le autorizzazioni per i ruoli necessarie.

**Ruolo** | **Dettagli** | **Autorizzazioni**
--- | --- | ---
Ruolo Azure_Site_Recovery | Individuazione di macchine virtuali VMware |Assegnare i privilegi seguenti per il server vCenter:<br/><br/>Datastore (Archivio dati) -> Allocate space (Alloca spazio), Browse datastore (Sfoglia archivio dati), Low level file operations (Operazioni file di livello basso), Remove file (Rimuovi file), Update virtual machine files (Aggiorna file macchina virtuale)<br/><br/>Network (Rete) -> Network assign (Assegnazione rete)<br/><br/>Resource (Risorsa) -> Assign virtual machine to resource pool (Assegna macchina virtuale a pool di risorse), Migrate powered off virtual machine (Esegui migrazione macchina virtuale spenta), Migrate powered on virtual machine (Esegui migrazione macchina virtuale accesa)<br/><br/>Tasks (Attività) -> Create task (Crea attività), Update task (Aggiorna attività)<br/><br/>Virtual machine (Macchina virtuale) ->  Configuration (Configurazione)<br/><br/>Virtual machine (Macchina virtuale) -> Interact (Interagisci) -> Answer question (Rispondi alla domanda), Device connection (Connessione dispositivo), Configure CD media (Configura supporto CD), Configure floppy media (Configura supporto floppy), Power off (Spegni), Power on (Accendi), VMware tools install (Installazione strumenti VMware)<br/><br/>Virtual machine (Macchina virtuale) -> Inventory (Inventario) -> Create (Crea), Register (Registra), Unregister (Annulla registrazione)<br/><br/>Virtual machine (Macchina virtuale) -> Provisioning -> Allow virtual machine download (Consenti download macchina virtuale), Allow virtual machine files upload (Consenti upload file macchina virtuale)<br/><br/>Macchina virtuale -> Snapshots -> Remove snapshots
Ruolo vCenter User | Individuazione della macchina virtuale VMware/Failover senza arresto della macchina virtuale di origine | Assegnare i privilegi seguenti per il server vCenter:<br/><br/>Data Center object (Oggetto data center)–> Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura) <br/><br/>L'utente viene assegnato a livello di data center e ha quindi accesso a tutti gli oggetti nel data center.  Se si vuole limitare l'accesso, assegnare il ruolo **No access** con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio, quali host vSphere, archivi dati, VM e reti.
Ruolo vCenter User | Failover e failback | Assegnare i privilegi seguenti per il server vCenter:<br/><br/>Data Center object (Oggetto data center) - Propagate to Child Object (Propaga a oggetto figlio), role=Azure_Site_Recovery (ruolo=Azure_Site_Recovery)<br/><br/>L'utente viene assegnato a livello di data center e ha quindi accesso a tutti gli oggetti nel data center.  Se si vuole limitare l'accesso, assegnare il ruolo **No access** con **Propagate to child object** (Propaga a oggetto figlio) all'oggetto figlio, quali host vSphere, archivi dati, VM e reti.  
## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni](site-recovery-failover.md) sui diversi tipi di failover.
- [Altre informazioni sul failback](site-recovery-failback-azure-to-vmware.md) per rendere nuovamente disponibili nell'ambiente locale i computer sottoposti a failover in esecuzione in Azure.

## <a name="third-party-software-notices-and-information"></a>Informazioni e comunicazioni sul software di terze parti

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.



<!--HONumber=Oct16_HO2-->


