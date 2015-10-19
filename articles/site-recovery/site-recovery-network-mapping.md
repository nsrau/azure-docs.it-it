<properties
	pageTitle="Mapping della rete di Site Recovery di Azure | Microsoft Azure"
	description="Azure Site Recovery coordina la replica, il failover e il ripristino delle macchine virtuali e dei server fisici ubicati nei server locali in Azure o in un sito locale secondario."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="10/07/2015"
	ms.author="raynew"/>


# Mapping della rete di Site Recovery di Azure


Azure Site Recovery favorisce l'attuazione della strategia di continuità aziendale e ripristino di emergenza orchestrando le operazioni di replica, failover e ripristino delle macchine virtuali e dei server fisici. Per informazioni sui possibili scenari di distribuzione consultare [Panoramica di Site Recovery](site-recovery-overview.md).


## Informazioni sull’articolo

Il mapping di rete è un elemento importante quando si distribuiscono VMM e Site Recovery. Tale mapping consente di posizionare in modo ottimale le macchine virtuali nei server host Hyper-V di destinazione e assicura che le macchine virtuali replicate vengano connesse alle reti appropriate dopo il failover. In questo articolo viene descritto il mapping di rete e vengono forniti un paio di esempi che consentono di comprendere il funzionamento del mapping di rete.


In caso di domande, è possibile pubblicarle nel [forum relativo ai Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Panoramica

Il modo in cui è possibile impostare il mapping di rete dipende dal proprio scenario di distribuzione di Site Recovery.



- **Da server VMM locale a server VMM locale**: il mapping di rete tra le reti della macchina virtuale in un server VMM di origine e le reti della macchina virtuale VM in un server VMM di destinazione per eseguire i seguenti scopi:

	- **Connettere le macchine virtuali dopo il failover**: assicura che macchine virtuali saranno connesse alle reti appropriate dopo il failover. La macchina virtuale di replica verrà connessa alla rete di destinazione di cui viene eseguito il mapping alla rete di origine.
	- **Posizionare le macchine virtuali di replica in server host**: posizionare in modo ottimale le macchine virtuali di replica nei server host Hyper-V. Le macchine virtuali di replica saranno posizionate negli host che possono accedere alle reti di macchine virtuali mappate.
	- **Nessun mapping di rete**--se non si configura il mapping di rete, le macchine virtuali replicate non saranno connesse ad alcuna rete di macchina virtuale dopo il failover.

- **Da server VMM locale ad Azure**: mapping di rete tra reti di macchina virtuale nel server VMM di origine e reti Azure di destinazione ai seguenti scopi:
	- **Connettere le macchine virtuali dopo il failover**: tutte le macchine virtuali della stessa rete possono connettersi l’una all’altra, a prescindere dal piano di ripristino di appartenenza.
	- **Gateway di rete**-se nella rete di Azure di destinazione è configurato un gateway di rete, le macchine virtuali possono connettersi ad altre macchine virtuali locali.
	- **Nessun mapping di rete**-se non si configura il mapping di rete, solo le macchine virtuali di cui viene eseguito il failover appartenenti allo stesso piano di ripristino possono connettersi tra loro dopo il failover in Azure.

## Reti VM

Una rete logica VMM fornisce una visualizzazione astratta dell'infrastruttura di rete fisica. Le reti VM forniscono un'interfaccia di rete in modo che le macchine virtuali possano connettersi alle reti logiche. Una rete logica richiede almeno una rete VM. Quando si inserisce una macchina virtuale in un cloud per la protezione, tale macchina deve essere connessa a una rete VM che si collega a una rete logica associata al cloud. Per altre informazioni, consultare:

- [Reti logiche (parte 1)](http://blogs.technet.com/b/scvmm/archive/2013/02/14/networking-in-vmm-2012-sp1-logical-networks-part-i.aspx)
- [Reti virtuali in VMM 2012 SP1](http://blogs.technet.com/b/scvmm/archive/2013/01/08/virtual-networking-in-vmm-2012-sp1.aspx)

## Esempio

Il mapping di rete può essere configurato tra le reti VM in due server VMM o in un singolo server VMM, se due siti sono gestiti dallo stesso server. Se il mapping di rete è configurato correttamente e la replica è abilitata, una macchina virtuale presente nella posizione principale verrà connessa a una rete e la relativa replica nella posizione di destinazione verrà connessa alla rete mappata.

Se le reti sono state configurate correttamente in VMM, quando si seleziona una rete VM di destinazione durante il mapping di rete, verranno visualizzati i cloud di origine VMM che utilizzano la rete VM di origine insieme alle le reti VM di destinazione disponibili nei cloud di destinazione utilizzati per la protezione.

Di seguito è riportato un esempio per illustrare questo meccanismo. Si prenda come esempio un’organizzazione con due sedi, New York e Chicago.

**Posizione** | **Server VMM** | **Reti VM** | **Mappata a**
---|---|---|---
New York | VMM-NewYork| VMNetwork1-NewYork | Mappata a VMNetwork1-Chicago
 | | VMNetwork2-NewYork | Non mappata
Chicago | VMM-Chicago| VMNetwork1-Chicago | Mappata a VMNetwork1-NewYork
 | | VMNetwork1-Chicago | Non mappata

Con questo esempio:

- Quando viene creata una macchina virtuale di replica per una macchina virtuale connessa a VMNetwork1-NewYork, essa verrà connessa a VMNetwork1-Chicago.
- Quando una macchina virtuale di replica viene creata per VMNetwork2-NewYork o VMNetwork2-Chicago, non verrà connessa ad alcuna rete.

Di seguito viene indicato in che modo vengono configurati i cloud VMM e in che modo le reti logiche vengono associate ai cloud.

### Impostazioni di protezione del cloud

**Cloud protetto** | **Protezione del cloud** | **Rete logica (New York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>ND</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>ND</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### Impostazioni di rete VM e logica

**Posizione** | **Rete logica** | **Rete VM associata**
---|---|---
New York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### Reti di destinazione

In base a queste impostazioni, quando si seleziona la rete VM di destinazione, nella tabella seguente sono mostrate le opzioni che saranno disponibili.

**Selezionare** | **Cloud protetto** | **Protezione del cloud** | **Rete di destinazione disponibili**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Disponibile
 | GoldCloud1 | GoldCloud2 | Disponibile
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Non disponibile
 | GoldCloud1 | GoldCloud2 | Disponibile



## Più subnet

Se la rete di destinazione dispone di più subnet e una di esse ha lo stesso nome della subnet in cui si trova la macchina virtuale di origine, la macchina virtuale di replica sarà connessa a tale subnet di destinazione dopo il failover. Se non è presente una subnet di destinazione con un nome corrispondente, la macchina virtuale sarà connessa alla prima subnet della rete.


### Failback

Per vedere cosa succede in caso di failback (replica inversa), si supponga che venga eseguito il mapping di VMNetwork1-NewYork a VMNetwork1-Chicago, con le impostazioni seguenti.


**Macchina virtuale** | **Connessa alla rete VM**
---|---
VM1 | VMNetwork1-Network
VM2 (replica di VM1) | VMNetwork1-Chicago

Con queste impostazioni esaminare cosa accade in un paio di scenari possibili.

**Scenario** | **Risultato**
---|---
Nessuna modifica nelle proprietà della rete di VM-2 dopo il failover | VM-1 rimane connessa alla rete di origine.
Le proprietà di rete di VM-2 cambiano dopo il failover e la macchina virtuale viene disconnessa | VM-1 viene disconnessa
Le proprietà di rete di VM-2 cambiano dopo il failover e la macchina virtuale viene connessa a VMNetwork2-Chicago | Se non viene eseguito il mapping di VMNetwork2-Chicago, VM-1 verrà disconnessa
Il mapping di rete di VMNetwork1-Chicago viene modificato | VM-1 verrà connessa alla rete ora mappata a VMNetwork1-Chicago


## Passaggi successivi

Ora che si dispone di una comprensione migliore del mapping di rete, iniziare a leggere le [procedure consigliate](site-recovery-best-practices.md) di preparazione alla distribuzione.

<!---HONumber=Oct15_HO2-->