<properties
    pageTitle="Funzionamento di Azure Site Recovery | Microsoft Azure"
    description="Questo articolo offre una panoramica dell'architettura di Site Recovery"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/13/2016"
    ms.author="raynew"/>


# <a name="how-does-azure-site-recovery-work?"></a>Funzionamento di Azure Site Recovery

Questo articolo illustra l'architettura del servizio Azure Site Recovery e i componenti necessari per il funzionamento.

Per inviare commenti o domande è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Overview

È necessaria una strategia per la continuità aziendale e il ripristino di emergenza (BCDR, Business Continuity and Disaster Recovery) per garantire il funzionamento e la disponibilità dei carichi di lavoro e dei dati durante i periodi di inattività pianificati e non pianificati e per ripristinare prima possibile le normali condizioni di lavoro. 

Site Recovery è un servizio di Azure che contribuisce all'attuazione della strategia BCDR. Orchestra la replica delle macchine virtuali e dei server fisici locali nel cloud di Azure o in un data center secondario. In caso di interruzioni nella località primaria, verrà eseguito il failover alla località secondaria per mantenere disponibili app e carichi di lavoro. Quando la località primaria sarà di nuovo operativa, si tornerà a tale località. Per una panoramica sull'argomento, vedere [Che cos'è Site Recovery?](site-recovery-overview.md)

## <a name="site-recovery-in-the-azure-portal"></a>Site Recovery nel portale di Azure

Azure offre due [modelli di distribuzione](../resource-manager-deployment-model.md) diversi per la creazione e l'uso delle risorse: il modello Azure Resource Manager e il modello di gestione classica dei servizi. Sono anche disponibili il [portale di Azure classico](https://manage.windowsazure.com/), che supporta il modello di distribuzione classica, e il [portale di Azure](https://portal.azure.com), che supporta entrambi i modelli di distribuzione.

- Site Recovery è disponibile sia nel portale classico che nel portale di Azure.
- Nel portale di Azure classico è possibile supportare Site Recovery con il modello di gestione classica dei servizi.
- Nel portale di Azure è possibile supportare il modello di distribuzione classica o il modello Azure Resource Model. 

Le informazioni in questo articolo sono applicabili al modello di distribuzione classica e al modello di distribuzione tramite il portale di Azure. La tabella seguente presenta un riepilogo delle differenze.

**Replica** | **Portale di Azure** | **Portale classico**
--- | --- | ---
**Replica di macchine virtuali VMware in Azure** | Processo di distribuzione semplificata.<br/><br/> Eseguire il failover delle macchine virtuali in risorse di archiviazione classiche o basate su Resource Manager.<br/><br/> Eseguire la replica in risorse di archiviazione classiche o basate su Resource Manager.<br/><br/> Usare reti classiche o di Resource Manager per connettere le macchine virtuali di Azure dopo il failover.<br/><br/> Usare l'archiviazione con ridondanza locale o l'archiviazione con ridondanza geografica. | Eseguire il failover solo in risorse di archiviazione classiche.<br/><br/> Usare reti classiche solo per connettere le macchine virtuali dopo il failover.<br/><br/> Usare l'archiviazione con ridondanza geografica.
**Replica di macchine virtuali Hyper-V, senza VMM, in Azure** | Processo di distribuzione semplificata.<br/><br/> Eseguire il failover delle macchine virtuali in risorse di archiviazione classiche o basate su Resource Manager.<br/><br/> Eseguire la replica in risorse di archiviazione classiche o basate su Resource Manager.<br/><br/> Usare reti classiche o di Resource Manager per connettere le macchine virtuali di Azure dopo il failover.
**Replica di macchine virtuali Hyper-V, con VMM, in Azure** | Processo di distribuzione semplificata.<br/><br/> Eseguire il failover delle macchine virtuali in risorse di archiviazione classiche o basate su Resource Manager.<br/><br/> Eseguire la replica in risorse di archiviazione classiche o basate su Resource Manager.<br/><br/> Usare reti classiche o di Resource Manager per connettere le macchine virtuali di Azure dopo il failover.<br/><br/> È necessario configurare il mapping di rete | Eseguire il failover solo in risorse di archiviazione classiche.<br/><br/> Usare reti classiche solo per connettere le macchine virtuali dopo il failover.
**Replica di macchine virtuali Hyper-V, con VMM, in un sito secondario** | Processo di distribuzione semplificata.<br/><br/> Eseguire il failover delle macchine virtuali in risorse di archiviazione classiche o basate su Resource Manager.<br/><br/> Eseguire la replica in risorse di archiviazione classiche o basate su Resource Manager.<br/><br/> Usare reti classiche o di Resource Manager per connettere le macchine virtuali di Azure dopo il failover.<br/><br/> È necessario configurare il mapping di rete | Eseguire il failover solo in risorse di archiviazione classiche.<br/><br/> Usare reti classiche solo per connettere le macchine virtuali dopo il failover.<br/><br/> È possibile configurare il mapping dell'archiviazione. <br/><br/> La replica SAN non è supportata.



## <a name="deployment-scenarios"></a>Scenari di distribuzione

È possibile distribuire Site Recovery per orchestrare la replica in diversi scenari:

- **Replicare macchine virtuali VMware**: è possibile replicare le macchine virtuali VMware locali nell'archiviazione di Azure o in un data center secondario.
- **Replicare computer fisici**: è possibile replicare i computer fisici che eseguono Windows o Linux nell'archiviazione di Azure o in un data center secondario. Il processo di replica dei computer fisici è quasi uguale al processo di replica delle macchine virtuali VMware.
- **Replicare macchine virtuali Hyper-V**: se gli host Hyper-V sono gestiti in cloud VMM di System Center, è possibile replicare le macchine virtuali in Azure o in un data center secondario. Se gli host non sono gestiti da VMM, è possibile eseguire la replica solo in Azure. È possibile replicare le macchine virtuali Hyper-V non gestite da VMM nell'archiviazione di Azure.
- **Eseguire la migrazione di macchine virtuali**: è possibile usare Site Recovery per [eseguire la migrazione di macchine virtuali IaaS di Azure](site-recovery-migrate-azure-to-azure.md) tra aree geografiche oppure per [eseguire la migrazione di istanze Windows di AWS](site-recovery-migrate-aws-to-azure.md) in macchine virtuali IaaS di Azure. La replica completa non è attualmente supportata. È possibile eseguire la replica per la migrazione, ovvero il failover, ma non è possibile eseguire il failback. 

Site Recovery può replicare la maggior parte delle app in esecuzione su queste VM e questi server fisici. Per un riepilogo completo delle app supportate, vedere [Carichi di lavoro che è possibile proteggere con Azure Site Recovery](site-recovery-workload.md)


## <a name="replicate-vmware-virtual-machines-to-azure"></a>Replicare le macchine virtuali VMWare locali in Azure

![Avanzato](./media/site-recovery-components/arch-enhanced.png)

**Componente** | **Dettagli**
--- | ---
**Azzurro** | **Account**: è necessario un account Azure.<br/><br/> **Archiviazione**: per archiviare i dati replicati, è necessario un account di archiviazione di Azure. È possibile usare un account classico o un account di archiviazione di Resource Manager. L'account può essere di tipo archiviazione con ridondanza locale o archiviazione con ridondanza geografica quando si esegue la distribuzione nel portale di Azure. I dati replicati vengono memorizzati nell'archiviazione di Azure e le macchine virtuali di Azure vengono attivate quando si verifica il failover.<br/><br/> **Rete**: è necessaria una rete virtuale di Azure a cui le macchine virtuali di Azure possano connettersi quando vengono create in fase di failover. Nel portale di Azure è possibile creare reti con il modello di gestione classica dei servizi o con il modello Resource Manager.
**Server di configurazione locale** |  È necessario un computer locale con Windows Server 2012 R2 che esegue il server di configurazione e altri componenti di Site Recovery.<br/><br/> Deve trattarsi di una macchina virtuale VMware a disponibilità elevata.<br/><br/> I componenti del server includono:<br/><br/> **Server di configurazione**: per coordinare la comunicazione tra l'ambiente locale e Azure e gestire la replica e il ripristino dei dati.<br/><br/> **Server di elaborazione**: agisce come un gateway di replica. Riceve i dati di replica dai computer di origine protetti, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia all'archiviazione di Azure. Gestisce anche l'installazione push del servizio Mobility nei computer protetti ed esegue l'individuazione automatica delle VM VMware. Man mano che la distribuzione cresce, è possibile aggiungere altri server di elaborazione dedicati e distinti per gestire i volumi più elevati di traffico di replica.<br/><br/> **Server di destinazione master**: gestisce i dati di replica durante il failback da Azure.
**Server di virtualizzazione locali** | Uno o più host vSphere. È anche consigliabile usare un server vCenter per gestire gli host.
**Macchine virtuali da replicare** | In ogni macchina virtuale VMware che si vuole replicare in Azure deve essere installato il componente del servizio Mobility. Questo servizio acquisisce le scritture di dati nel computer e le inoltra al server di elaborazione. Questo componente può essere installato manualmente oppure si può effettuarne il push e installarlo automaticamente tramite il server di elaborazione quando si abilita la replica per un computer.

- [Altre informazioni](site-recovery-vmware-to-azure.md#azure-prerequisites) sui requisiti per la distribuzione tramite il portale di Azure.
- [Altre informazioni](site-recovery-failback-azure-to-vmware.md) sul failback nel portale di Auzre.


## <a name="replicate-physical-servers-to-azure"></a>Replicare i server fisici in Azure

![Avanzato](./media/site-recovery-components/arch-enhanced.png)

**Componente** | **Dettagli**
--- | ---
**Azzurro** | **Account**: è necessario un account Azure.<br/><br/> **Archiviazione**: per archiviare i dati replicati, è necessario un account di archiviazione di Azure. È possibile usare un account classico o un account di archiviazione di Resource Manager. L'account può essere di tipo archiviazione con ridondanza locale o archiviazione con ridondanza geografica quando si esegue la distribuzione nel portale di Azure. I dati replicati vengono memorizzati nell'archiviazione di Azure e le macchine virtuali di Azure vengono attivate quando si verifica il failover.<br/><br/> **Rete**: è necessaria una rete virtuale di Azure a cui le macchine virtuali di Azure possano connettersi quando vengono create in fase di failover. Nel portale di Azure è possibile creare reti con il modello di gestione classica dei servizi o con il modello Resource Manager.
**Server di configurazione locale** |  È necessario un computer locale con Windows Server 2012 R2 che esegue il server di configurazione e altri componenti di Site Recovery.<br/><br/> Il server può essere fisico o virtuale.<br/><br/> I componenti del server includono:<br/><br/> **Server di configurazione**: per coordinare la comunicazione tra l'ambiente locale e Azure e gestire la replica e il ripristino dei dati.<br/><br/> **Server di elaborazione**: agisce come un gateway di replica. Riceve i dati di replica dai computer di origine protetti, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia all'archiviazione di Azure. Gestisce anche l'installazione push del servizio Mobility nei computer protetti ed esegue l'individuazione automatica delle VM VMware. Man mano che la distribuzione cresce, è possibile aggiungere altri server di elaborazione dedicati e distinti per gestire i volumi più elevati di traffico di replica.<br/><br/> **Server di destinazione master**: gestisce i dati di replica durante il failback da Azure.
**Macchine da replicare** | In ogni computer fisico Windows o Linux che si vuole replicare in Azure deve essere installato il componente del servizio Mobility. Questo servizio acquisisce le scritture di dati nel computer e le inoltra al server di elaborazione. Questo componente può essere installato manualmente oppure si può effettuarne il push e installarlo automaticamente tramite il server di elaborazione quando si abilita la replica per un computer.
**Failback** | Il failback da computer fisico a computer fisico non è supportato. Se si esegue il failover di server fisici in Azure e quindi si vuole eseguire il failback, sarà necessario eseguire il failback in una macchina virtuale VMware. Non è possibile eseguire il failback a un server fisico. È necessaria una macchina virtuale di Azure in cui eseguire il failback. Se il server di configurazione non è stato distribuito come macchina virtuale VMware, sarà necessario configurare un server di destinazione master separato come macchina virtuale VMware. Questa operazione è necessaria perché il server di destinazione master interagisce e si collega con le risorse di archiviazione VMware per ripristinare i dischi in una macchina virtuale VMware.


- [Altre informazioni](site-recovery-vmware-to-azure.md#azure-prerequisites) sui requisiti per la distribuzione tramite il portale di Azure.
- [Altre informazioni](site-recovery-failback-azure-to-vmware.md) sul failback nel portale di Auzre.


## <a name="replicate-hyper-v-vms-not-managed-by-vmm-to-azure"></a>Eseguire la replica di macchine virtuali Hyper-V non gestite da VMM in Azure

![Da sito Hyper-V ad Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

**Componente** | **Dettagli**
--- | ---
**Azzurro** | **Account**: è necessario un account Azure.<br/><br/> **Archiviazione**: per archiviare i dati replicati, è necessario un account di archiviazione di Azure. È possibile usare un account classico o un account di archiviazione di Resource Manager. Deve trattarsi di un account di archiviazione con ridondanza geografica. I dati replicati vengono memorizzati nell'archiviazione di Azure e le macchine virtuali di Azure vengono attivate quando si verifica il failover.<br/><br/> **Rete**: è necessaria una rete virtuale di Azure a cui le macchine virtuali di Azure possano connettersi quando vengono create in fase di failover.
**Host e macchine virtuali Hyper-V** | Uno o più host Hyper-V che eseguono una o più macchine virtuali.<br/><br/> Il provider di Site Recovery e l'agente di Servizi di ripristino vengono installati in ogni host durante la distribuzione.

- [Altre informazioni](site-recovery-hyper-v-site-to-azure.md#azure-prerequisites) sui requisiti per la distribuzione tramite il portale di Azure.
- [Altre informazioni](site-recovery-hyper-v-site-to-azure-classic.md#azure-prerequisites) sui requisiti per la distribuzione tramite il portale classico.



## <a name="replicate-hyper-v-vms-managed-by-vmm-to-azure"></a>Eseguire la replica di macchine virtuali Hyper-V gestite da VMM in Azure


![Da VMM ad Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

**Componente** | **Dettagli**
--- | ---
**Azzurro** | **Account**: è necessario un account Azure.<br/><br/> **Archiviazione**: per archiviare i dati replicati, è necessario un account di archiviazione di Azure. È possibile usare un account classico o un account di archiviazione di Resource Manager. Deve trattarsi di un account di archiviazione con ridondanza geografica. I dati replicati vengono memorizzati nell'archiviazione di Azure e le macchine virtuali di Azure vengono attivate quando si verifica il failover.<br/><br/> **Rete**: è necessaria una rete virtuale di Azure a cui le macchine virtuali di Azure possano connettersi quando vengono create in fase di failover.
**Server VMM** | Sono necessari uno o più server VMM locali, con uno o più cloud privati. Il provider di Site Recovery viene installato in ogni server durante la distribuzione.
**Host e macchine virtuali Hyper-V** | Uno o più host Hyper-V che eseguono una o più macchine virtuali.<br/><br/> L'agente di Servizi di ripristino viene installato in ogni host durante la distribuzione.


- [Altre informazioni](site-recovery-vmm-to-azure.md#azure-requirements) sui requisiti per la distribuzione tramite il portale di Azure.
- [Altre informazioni](site-recovery-vmm-to-azure-classic.md#before-you-start) sui requisiti per la distribuzione tramite il portale classico.




## <a name="replicate-vmware-virtual-machines-or-physical-server-to-a-secondary-site"></a>Eseguire la replica di server fisici o macchine virtuali VMware in un sito secondario

![Da VMware a VMware](./media/site-recovery-components/vmware-to-vmware.png)


**Componente** | **Dettagli**
--- | ---
**Azzurro** | **Account**: questo scenario viene distribuito con InMage Scout. Per ottenerlo, è necessario avere un account Azure.<br/><br/> Dopo aver creato un insieme di credenziali di Site Recovery, scaricare InMage Scout e installare gli aggiornamenti più recenti per configurare la distribuzione.
**Sito primario** | **Server di elaborazione**: configurare il componente del server di elaborazione nel sito primario per gestire la memorizzazione nella cache, la compressione e l'ottimizzazione dei dati. Gestisce anche l'installazione push di Unified Agent nei computer da proteggere.<br/><br/> **VMware ESX/ESXi e vCenter**: è necessario un hypervisor VMware EXS/ESXi e, facoltativamente, un server VMware vCenter.<br/><br/> **Macchine virtuali** | Durante la distribuzione Unified Agent viene installato nei computer da proteggere. L'agente funge da provider di comunicazioni tra tutti i componenti.
**Sito secondario** | **Server di configurazione**: il server di configurazione è il primo componente da installare e viene installato nel sito secondario per gestire, configurare e monitorare la distribuzione mediante il sito Web di gestione o la console vContinuum. È presente un solo server di configurazione in una distribuzione e deve essere installato in un computer che esegue Windows Server 2012 R2.<br/><br/> **Server vContinuum (sito secondario)**: viene installato nella stessa posizione del server di configurazione. Fornisce una console per la gestione e il monitoraggio dell'ambiente protetto. In un'installazione predefinita il server vContinuum è il primo server di destinazione master e include l'installazione di Unified Agent.<br/><br/> **Server di destinazione master**: questo server contiene i dati replicati. Riceve i dati dal server di elaborazione e crea un computer di replica nel sito secondario e include i punti di conservazione dei dati. Il numero di server di destinazione master necessari dipende dal numero di computer da proteggere. Se si vuole eseguire il failback nel sito primario, dovrà essere disponibile anche un server di destinazione master.


Per replicare server fisici o macchine virtuali VMware in un sito secondario, scaricare InMage Scout incluso nella sottoscrizione di Azure Site Recovery. È possibile scaricarlo dal portale di Azure o dal portale di Azure classico.

Configurare i server del componente in ogni sito, ovvero configurazione, elaborazione, destinazione master, e installare Unified Agent nei computer da replicare. Dopo la replica iniziale, l'agente installato in ogni computer invia le modifiche della replica differenziale al server di elaborazione. Il server di elaborazione ottimizza i dati e li trasferisce nel server di destinazione master nel sito secondario. Il server di configurazione gestisce il processo di replica.


## <a name="replicate-hyper-v-vms-managed-by-vmm-to-a-secondary-site"></a>Eseguire la replica di macchine virtuali Hyper-V gestite da VMM in un sito secondario

![Da sito locale a sito locale](./media/site-recovery-components/arch-onprem-onprem.png)

**Componente** | **Dettagli**
--- | ---
**Azzurro** | **Account**: è necessario un account Azure.
**Server VMM** | È consigliabile distribuire un server VMM in un sito primario e uno nel sito secondario. Ogni server necessita di uno o più cloud privati.<br/><br/> Durante la distribuzione si installa il provider di Azure Site Recovery nel server VMM.
**Host e macchine virtuali Hyper-V** | Uno o più host Hyper-V in esecuzione nei cloud VMM nei siti primario e secondario<br/><br/> Ogni host deve avere una o più macchine virtuali da replicare.<br/><br/>. L'agente di Servizi di ripristino viene installato in ogni host durante la distribuzione.

- [Altre informazioni](site-recovery-vmm-to-vmm.md#azure-prerequisites) sui requisiti di distribuzione nel portale di Azure.
- [Altre informazioni](site-recovery-vmm-to-vmm-classic.md#before-you-start) sui requisiti di distribuzione nel portale di Azure classico.


## <a name="replicate-hyper-v-vms-managed-by-vmm-to-a-secondary-site-using-san-replication"></a>Eseguire la replica di macchine virtuali Hyper-V gestite da VMM in un sito secondario con la replica SAN

![Replica SAN](./media/site-recovery-components/arch-onprem-onprem-san.png)

È possibile replicare macchine virtuali Hyper-V gestite in cloud VMM in un sito secondario usando la replica SAN con il portale classico. Questo scenario non è attualmente supportato nel portale di Azure.

**Componente** | **Dettagli**
--- | ---
**Azzurro** | **Account**: è necessario un account Azure.
**Server VMM** | È consigliabile distribuire un server VMM in un sito primario e uno nel sito secondario. Ogni server necessita di uno o più cloud privati.<br/><br/> Durante la distribuzione si installa il provider di Azure Site Recovery nel server VMM.
**SAN** | Un array SAN supportato gestito dal server VMM primario.<br/><br/> La rete SAN deve condividere un'infrastruttura di rete con un altro array SAN nel sito secondario.
**Host e macchine virtuali Hyper-V** | Uno o più host Hyper-V in esecuzione nei cloud VMM nei siti primario e secondario<br/><br/> Ogni host deve avere una o più macchine virtuali da replicare.<br/><br/>. L'agente di Servizi di ripristino viene installato in ogni host durante la distribuzione.

In questo scenario durante la distribuzione di Site Recovery installare il provider di Azure Site Recovery nei server VMM. Il provider coordina e orchestra la replica con il servizio Site Recovery su Internet. I dati vengono replicati tra le matrici di archiviazione primaria e secondaria mediante la replica SAN sincrona.

[Altre informazioni](site-recovery-vmm-san.md#before-you-start) sui requisiti di distribuzione.

## <a name="hyper-v-protection-lifecycle"></a>Ciclo di vita di protezione di Hyper-V

Questo flusso di lavoro mostra il processo di protezione, replica e failover delle macchine virtuali Hyper-V.

1. **Abilitare la protezione**: configurare l'insieme di credenziali di Site Recovery, configurare le impostazioni di replica per un cloud VMM o un sito Hyper-V e abilitare la protezione per le VM. Viene avviato un processo denominato **Abilita protezione**, che è possibile monitorare nella scheda **Processi**. Il processo verifica che il computer sia conforme ai prerequisiti e quindi richiama il metodo [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) , che configura la replica in Azure con le impostazioni configurate. Il processo **Abilita protezione** richiama anche il metodo [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) per inizializzare una replica completa della VM.
2. **Replica iniziale**: viene eseguito uno snapshot macchina virtuale e i dischi rigidi virtuali vengono replicati uno alla volta, fino a quando non sono stati tutti copiati in Azure o nel data center secondario. Il tempo necessario per completare l'operazione dipende dalle dimensioni della VM, dalla larghezza di banda della rete e dal metodo di replica iniziale. Se vengono apportate modifiche al disco mentre è in corso la replica iniziale, Hyper-V Replica Replication Tracker tiene traccia delle modifiche sotto forma di log di replica di Hyper-V (con estensione hrl), che si trovano nella stessa cartella dei dischi. A ogni disco è associato un file con estensione hrl, che verrà inviato alla risorsa di archiviazione secondaria. Si noti che lo snapshot e i file di log utilizzano risorse del disco durante l'esecuzione della replica iniziale. Al termine della replica iniziale, lo snapshot macchina virtuale viene eliminato e le modifiche differenziali al disco nel log vengono sincronizzate e unite.
3. **Finalizzare la protezione**: al termine della replica iniziale, il processo **Finalizza protezione** configura la rete e altre impostazioni successive alla replica, in modo che la macchina virtuale risulti protetta. Se si esegue la replica in Azure, potrebbe essere necessario modificare le impostazioni della macchina virtuale in modo che sia pronta per il failover. A questo punto è possibile eseguire un failover di test per controllare che tutto funzioni come previsto.
4. **Replica**: dopo la replica differenziale iniziale inizia la sincronizzazione, in base alle impostazioni della replica.
    - **Errore di replica**: se la replica differenziale non riesce e una replica completa risulta costosa a livello di larghezza di banda o del tempo richiesto, verrà eseguita la risincronizzazione. Ad esempio, se i file con estensione hrl raggiungono il 50% delle dimensioni del disco, la VM verrà contrassegnata per la risincronizzazione. La risincronizzazione riduce al minimo la quantità di dati inviati calcolando i checksum delle macchine virtuali di origine e di destinazione e inviando solo il relativo differenziale. Al termine della risincronizzazione, riprendere la replica differenziale. Per impostazione predefinita, la risincronizzazione è pianificata per l'esecuzione automatica negli orari non lavorativi, ma è possibile risincronizzare manualmente una macchina virtuale.
    - **Errore di replica**: se si verifica un errore di replica, per impostazione predefinita viene effettuato un nuovo tentativo. Se si tratta di un errore irreversibile, ad esempio un errore di autenticazione o di autorizzazione oppure se lo stato del computer di replica non è valido, non verranno effettuati nuovi tentativi. Se si tratta di un errore reversibile, ad esempio un errore di rete o spazio su disco o memoria insufficiente, verranno eseguiti nuovi tentativi a intervalli crescenti (ogni 1, 2, 4, 8, 10 e quindi 30 minuti).
4. **Failover pianificati e non pianificati**: è possibile eseguire failover pianificati e non pianificati in base alle esigenze. Se si esegue un failover pianificato, le VM di origine vengono arrestate per assicurare che non si verifichino perdite di dati. Dopo la creazione, le VM di replica sono in uno stato di commit in sospeso. È necessario eseguirne il commit per completare il failover, a meno che non si stia eseguendo la replica con SAN. In questo caso, il commit è automatico. Quando il sito primario è attivo e in esecuzione, è possibile eseguire il failback. Se è stata eseguita la replica in Azure, la replica inversa sarà automatica. In caso contrario, è possibile attivare una replica inversa manualmente.


![flusso di lavoro](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Passaggi successivi

[Preparare la distribuzione](site-recovery-best-practices.md)



<!--HONumber=Oct16_HO2-->


