
<properties
	pageTitle="Configurare la protezione con un singolo server VMM"
	description="Azure Site Recovery coordina la replica, il failover e il ripristino delle macchine virtuali ubicate nei cloud VMM locali in Azure o in un cloud VMM secondario."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="05/04/2015"
	ms.author="raynew"/>

#  Configurare la protezione con un singolo server VMM

## Panoramica

Azure Site Recovery favorisce la strategia di continuità aziendale e ripristino di emergenza \(BCDR\) gestendo la replica, il failover e il ripristino delle macchine virtuali in diversi scenari di distribuzione. Per un elenco completo degli scenari di distribuzione, vedere [Panoramica di Azure Site Recovery](site-recovery-overview.md).

Se si dispone solo di un singolo server VMM nell'infrastruttura, è possibile distribuire Site Recovery per replicare le macchine virtuali in cloud VMM in Azure oppure è possibile replicare tra cloud in un singolo server VMM. Si consiglia di eseguire questa procedura solo se non è possibile distribuire due server VMM \(uno in ciascun sito\) poiché failover e ripristino non sono automatici in questa distribuzione. Per il ripristino è necessario eseguire manualmente il failover del server VMM dall'esterno della console di Azure Site Recovery \(tramite Replica Hyper-V nella console di gestione di Hyper-V\).

È possibile configurare la replica usando un singolo server VMM in due modi:

### Distribuzione autonoma

Distribuire un server VMM autonomo come macchina virtuale in un sito primario e replicare questa macchina virtuale in un sito secondario con Site Recovery e Replica Hyper-V. Per ridurre i tempi di inattività, SQL Server può essere installato nella macchina virtuale VMM. Se VMM utilizza un Server SQL remoto è necessario ripristinare tale server prima di ripristinare il server VMM.

![Server VMM virtuale autonomo](./media/site-recovery-single-vmm/SingleVMMStandalone.png)

### Distribuzione cluster

Per assicurare la disponibilità elevata, VMM può essere distribuito come macchina virtuale in un cluster di failover di Windows. Questa operazione è utile se carichi di lavoro critici sono gestiti da VMM, in quanto garantisce la disponibilità del carico di lavoro e protegge da failover hardware dell'host in cui VMM è in esecuzione. Per distribuire un singolo server VMM con Site Recovery, la macchina virtuale VMM deve essere distribuita in un cluster esteso in siti geograficamente separati. Il database di SQL Server usato da VMM deve essere protetto con gruppi di disponibilità AlwaysOn di SQL Server con una replica nel sito secondario. Se si verifica un ripristino di emergenza, il server VMM e il corrispondente SQL Server sono sottoposti a failover e accessibili dal sito secondario.

![Server VMM virtuale cluster](./media/site-recovery-single-vmm/SingleVMMCluster.png)


## Prima di iniziare

- I passaggi della procedura dettagliata illustrano come distribuire Site Recovery con un singolo server VMM autonomo.
- Prima di iniziare la distribuzione, verificare che i [prerequisiti](site-recovery-vmm-to-vmm.md/#before-you-start) siano disponibili.
- Il singolo server VMM deve disporre di almeno due cloud configurati. Un cloud fungerà da cloud protetto e l'altro eseguirà la protezione.
- I cloud che si desidera proteggere devono contenere i seguenti elementi:
	- Uno o più gruppi host VMM
	- Uno o più server host Hyper-V in ciascun gruppo host.
	- Una o più macchine virtuali Hyper V in ciascun server host.

Nel caso di problemi di configurazione di questo scenario, inviare le proprie domande al [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).



## Configurare una distribuzione a server singolo

1. Se VMM non è stato distribuito, configurare VMM in una macchina virtuale con un database SQL Server installato. Leggere i [requisiti di sistema](https://technet.microsoft.com/library/dn771747.aspx) 
2. Configurare almeno due cloud nel server VMM. Per altre informazioni, consultare:

	- [Novità del cloud privato con System Center 2012 R2 VMM](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=) e [VMM 2012 e i cloud](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html). 
	- [Configurazione dell'infrastruttura cloud VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
	- [Creazione di un cloud privato in VMM](https://technet.microsoft.com/library/jj860425.aspx) e [Procedura dettagliata: creazione di cloud privati con System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).
3. Aggiungere il server host Hyper-V di origine in cui si trova la macchina virtuale che si desidera proteggere al cloud che si intende proteggere \(cloud di origine\). Aggiungere il server di host Hyper-V di destinazione al cloud nel server VMM che fornirà la protezione.
4. [Creare](site-recovery-vmm-to-vmm.md/#step-1-create-a-site-recovery-vault) un insieme di credenziali di Azure Site Recovery e generare una chiave di registrazione per l’insieme di credenziali.
4. [Installare](site-recovery-vmm-to-vmm.md/#step-3-install-the-azure-site-recovery-provider) il provider di Azure Site Recovery nel server VMM e registrare il server nell'insieme di credenziali. 
5. Verificare che i cloud vengano visualizzati nel portale di Site Recovery e [configurare le impostazioni di protezione del cloud](site-recovery-vmm-to-vmm.md/#step-4-configure-cloud-protection-settings).
	- In **Percorso di origine** e **Percorso di destinazione** specificare il nome del singolo server VMM.
	- In **Metodo di replica**, selezionare **Attraverso la rete** per la replica iniziale perché i cloud si trovano nello stesso server.

6. Facoltativamente, [configurare il mapping di rete](site-recovery-vmm-to-vmm.md/#step-5-configure-network-mapping):

	- In **Origine** e **Destinazione** specificare il nome del singolo server VMM.
	- In **Rete nell'origine** selezionare la rete VM configurata per il cloud che si vuole proteggere.
	- In **Rete nella destinazione** selezionare la rete VM configurata per il cloud che si vuole utilizzare per la protezione.
	- Il mapping di rete può essere configurato tra due reti di macchine virtuali \(VM\) sullo stesso server VMM. Se la stessa rete VMM esiste in due siti diversi, è possibile eseguire il mapping tra le stesse reti.
7. [Abilitare la protezione](site-recovery-vmm-to-vmm.md/#step-7-enable-virtual-machine-protection) per le macchine virtuali nel cloud VMM che si vuole proteggere. 
7. Nella console di gestione di Hyper-V, configurare la replica per la macchina virtuale VMM con Replica Hyper-V. La macchina virtuale VMM non deve essere aggiunta ai cloud VMM.


## Failover e ripristino

### Creare piani di ripristino

I piani di ripristino raggruppano insieme le macchine virtuali che devono essere sottoposte a failover e ripristinate contemporaneamente.

1. Quando si crea un piano di ripristino, in **Origine** e **Destinazione** specificare il nome del singolo server VMM. In **Seleziona macchine virtuali**, verranno visualizzate le macchine virtuali associate al cloud primario.
2. Quindi [creare e personalizzare i piani di ripristino](https://msdn.microsoft.com/library/azure/dn337331.aspx).


### Ripristino

In caso di emergenza, i carichi di lavoro possono essere ripristinati tramite la procedura seguente:

1. Eseguire il failover manuale della macchina virtuale VMM di replica nel sito di ripristino dalla console di gestione di Hyper-V.
2. Dopo che la macchina virtuale VMM è stata ripristinata, è possibile accedere alla console di gestione ripristino di Hyper-V dal portale ed eseguire un failover non pianificato delle macchine virtuali dal sito primario al sito di ripristino.
3.  Al termine del failover non pianificato gli utenti possono accedere a tutte le risorse nel sito primario.


 

<!---HONumber=58_postMigration-->