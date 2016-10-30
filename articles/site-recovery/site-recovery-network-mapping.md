<properties
    pageTitle="Preparare il mapping di rete per la protezione di macchine virtuali Hyper-V con VMM in Azure Site Recovery | Microsoft Azure"
    description="Configurare il mapping di rete per replicare in Azure o in un sito secondario macchine virtuali Hyper-V presenti in un data center locale."
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
    ms.workload="storage-backup-recovery"
    ms.date="10/05/2016"
    ms.author="raynew"/>



# <a name="prepare-network-mapping-for-hyper-v-virtual-machine-protection-with-vmm-in-azure-site-recovery"></a>Preparare il mapping di rete per la protezione delle macchine virtuali Hyper-V con VMM in Azure Site Recovery

Azure Site Recovery favorisce l'attuazione della strategia di continuità aziendale e ripristino di emergenza orchestrando le operazioni di replica, failover e ripristino delle macchine virtuali e dei server fisici.

Questo articolo descrive il mapping di rete, che consente di configurare le impostazioni di rete in modo ottimale quando si usa Site Recovery per replicare le macchine virtuali Hyper-V presenti in cloud VMM tra due data center locali o tra un data center locale e Azure. Le informazioni presenti in questo articolo non sono valide se si replicano macchine virtuali Hyper-V senza un cloud VMM o si replicano macchine virtuali VMware o server fisici.

Per inviare commenti o domande è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Overview

Si ricorre al mapping di rete quando si distribuisce Azure Site Recovery per replicare macchine virtuali Hyper-V in Azure o in un data center secondario usando la replica Hyper-V o SAN.

- **Replica di macchine virtuali Hyper-V in cloud VMM tra due data center locali**: mapping tra reti di macchine virtuali su un server VMM di origine e reti di macchine virtuali su un server VMM di destinazione per eseguire le operazioni seguenti.

    - **Connettere le macchine virtuali dopo il failover**: assicura che macchine virtuali saranno connesse alle reti appropriate dopo il failover. La macchina virtuale di replica verrà connessa alla rete di destinazione di cui viene eseguito il mapping alla rete di origine.
    - **Posizionare le macchine virtuali di replica in server host**: posizionare in modo ottimale le macchine virtuali di replica nei server host Hyper-V. Le macchine virtuali di replica saranno posizionate negli host che possono accedere alle reti di macchine virtuali mappate.
    - **Nessun mapping di rete**: se non si configura il mapping di rete, le macchine virtuali replicate non risulteranno connesse ad alcuna rete di macchina virtuale in seguito al failover.

- **Replica in Azure delle macchine virtuali Hyper-V presenti in un cloud VMM locale**: mapping tra reti di macchine virtuali sul server VMM di origine e reti Azure di destinazione per eseguire le operazioni seguenti.
    - **Connettere le macchine virtuali dopo il failover**: tutte le macchine virtuali della stessa rete possono connettersi l’una all’altra, a prescindere dal piano di ripristino di appartenenza.
    - **Gateway di rete**: se nella rete di Azure di destinazione è configurato un gateway di rete, le macchine virtuali possono connettersi ad altre macchine virtuali locali.
    - **Nessun mapping di rete**: se non si configura il mapping di rete, solo le macchine virtuali di cui viene eseguito il failover appartenenti allo stesso piano di ripristino possono connettersi tra loro dopo il failover in Azure.


## <a name="network-mapping-example"></a>Esempio di mapping di rete

Il mapping di rete può essere configurato tra le reti VM in due server VMM o in un singolo server VMM, se due siti sono gestiti dallo stesso server. Se il mapping di rete è configurato correttamente e la replica è abilitata, una macchina virtuale presente nella posizione principale verrà connessa a una rete e la relativa replica nella posizione di destinazione verrà connessa alla rete mappata.

Se le reti sono state configurate correttamente in VMM, quando si seleziona una rete VM di destinazione durante il mapping di rete, verranno visualizzati i cloud di origine VMM che utilizzano la rete VM di origine insieme alle le reti VM di destinazione disponibili nei cloud di destinazione utilizzati per la protezione.

Di seguito è riportato un esempio per illustrare questo meccanismo. Si prenda come esempio un’organizzazione con due sedi, New York e Chicago.

**Posizione** | **Server VMM** | **Reti VM** | **Mappata a**
---|---|---|---
New York | VMM-NewYork| VMNetwork1-NewYork | Mappata a VMNetwork1-Chicago
 |  | VMNetwork2-NewYork | Non mappata
Chicago | VMM-Chicago| VMNetwork1-Chicago | Mappata a VMNetwork1-NewYork
 | | VMNetwork2-Chicago | Non mappata

Con questo esempio:

- Quando viene creata una macchina virtuale di replica per una macchina virtuale connessa a VMNetwork1-NewYork, essa verrà connessa a VMNetwork1-Chicago.
- Quando una macchina virtuale di replica viene creata per VMNetwork2-NewYork o VMNetwork2-Chicago, non verrà connessa ad alcuna rete.

Di seguito viene indicato in che modo vengono configurati i cloud VMM e in che modo le reti logiche vengono associate ai cloud.

### <a name="cloud-protection-settings"></a>Impostazioni di protezione del cloud

**Cloud protetto** | **Protezione del cloud** | **Rete logica (New York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>ND</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>ND</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Impostazioni di rete VM e logica

**Posizione** | **Rete logica** | **Rete VM associata**
---|---|---
New York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### <a name="target-networks"></a>Reti di destinazione

In base a queste impostazioni, quando si seleziona la rete VM di destinazione, nella tabella seguente sono mostrate le opzioni che saranno disponibili.

**Selezionare** | **Cloud protetto** | **Protezione del cloud** | **Rete di destinazione disponibili**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Disponibile
 | GoldCloud1 | GoldCloud2 | Disponibile
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Non disponibile
 | GoldCloud1 | GoldCloud2 | Disponibile



## <a name="multiple-subnets"></a>Più subnet

Se la rete di destinazione dispone di più subnet e una di esse ha lo stesso nome della subnet in cui si trova la macchina virtuale di origine, la macchina virtuale di replica sarà connessa a tale subnet di destinazione dopo il failover. Se non è presente una subnet di destinazione con un nome corrispondente, la macchina virtuale sarà connessa alla prima subnet della rete.


### <a name="failback"></a>Failback

Per vedere cosa succede in caso di failback (replica inversa), si supponga che venga eseguito il mapping di VMNetwork1-NewYork a VMNetwork1-Chicago, con le impostazioni seguenti.


**Macchina virtuale** | **Connessa alla rete VM**
---|---
VM1 | VMNetwork1-Network
VM2 (replica di VM1) | VMNetwork1-Chicago

Con queste impostazioni esaminare cosa accade in un paio di scenari possibili.

**Scenario** | **Risultato**
---|---
Nessuna modifica nelle proprietà della rete di VM-2 dopo il failover | VM-1 rimane connessa alla rete di origine.
Le proprietà di rete di VM-2 cambiano dopo il failover e la macchina virtuale viene disconnessa  | VM-1 viene disconnessa
Le proprietà di rete di VM-2 cambiano dopo il failover e la macchina virtuale viene connessa a VMNetwork2-Chicago | Se non viene eseguito il mapping di VMNetwork2-Chicago, VM-1 verrà disconnessa
Il mapping di rete di VMNetwork1-Chicago viene modificato | VM-1 verrà connessa alla rete ora mappata a VMNetwork1-Chicago


## <a name="next-steps"></a>Passaggi successivi

Dopo aver acquisito familiarità con il mapping di rete, è ora possibile [prepararsi alla distribuzione di Site Recovery](site-recovery-best-practices.md).



<!--HONumber=Oct16_HO2-->


