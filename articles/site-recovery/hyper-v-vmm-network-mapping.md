---
title: Informazioni sul mapping di rete per la replica di macchine virtuali Hyper-V (con VMM) in Azure con Site Recovery | Microsoft Docs
description: Illustra come configurare il mapping di rete per la replica di macchine virtuali Hyper-V gestite in cloud VMM con Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/22/2018
ms.author: raynew
ms.openlocfilehash: 524de918bd24d51680110dc2af213bf328e349fd
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="prepare-network-mapping-for-hyper-v-vm-replication-to-azure"></a>Preparare il mapping di rete per la replica di VM Hyper-V in Azure


Questo articolo fornisce informazioni utili per la comprensione e la pianificazione del mapping di rete durante la replica di macchine virtuali Hyper-V in cloud System Center Virtual Machine Manager (VMM) in Azure o in un sito secondario tramite il [servizio Azure Site Recovery](site-recovery-overview.md).


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Preparare il mapping di rete per la replica in Azure

Quando si esegue la replica in Azure, il mapping di rete esegue il mapping tra reti VM in un server VMM e reti virtuali di Azure di destinazione. Il mapping esegue queste operazioni:
    -  **Connessione di rete**: assicura che le VM di Azure replicate vengano connesse alla rete mappata. Tutte le macchine virtuali che eseguono il failover nella stessa rete possono connettersi tra loro, anche se hanno eseguito il failover in piani di ripristino diversi.
    - **Gateway di rete**: se nella rete di Azure di destinazione è configurato un gateway di rete, le macchine virtuali possono connettersi ad altre macchine virtuali locali.

Il mapping di rete funziona nel modo seguente:

- Viene eseguito il mapping di una rete VM VMM di origine a una rete virtuale di Azure.
- Dopo il failover, le VM di Azure nella rete di origine verranno connesse alla rete virtuale di destinazione mappata.
- Le nuove VM aggiunte alla rete VM di origine vengono connesse alla rete di Azure mappata quando si verifica la replica.
- Se la rete di destinazione dispone di più subnet e una di esse ha lo stesso nome di una subnet in cui si trova la macchina virtuale di origine, la macchina virtuale di replica si connette a questa subnet di destinazione dopo il failover.
- Se non è presente una subnet di destinazione con un nome corrispondente, la macchina virtuale si connette alla prima subnet della rete.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Preparare il mapping di rete per la replica in un sito secondario

Quando si esegue la replica in un sito secondario, il mapping di rete esegue il mapping tra reti VM in un server VMM di origine e reti VM in un server VMM di destinazione. Il mapping esegue queste operazioni:

- **Connessione di rete**: esegue la connessione delle VM alle reti appropriate dopo il failover. La macchina virtuale di replica verrà connessa alla rete di destinazione di cui viene eseguito il mapping alla rete di origine.
- **Posizionamento ottimale delle macchine virtuali** : posiziona in modo ottimale le VM di replica nei server host Hyper-V. Le macchine virtuali di replica vengono posizionate negli host che possono accedere alle reti di macchine virtuali mappate.
- **Nessun mapping di rete**: se non si configura il mapping di rete, le macchine virtuali replicate non risulteranno connesse ad alcuna rete di macchina virtuale in seguito al failover.

Il mapping di rete funziona nel modo seguente:

- Il mapping di rete può essere configurato tra le reti VM in due server VMM o in un singolo server VMM, se due siti sono gestiti dallo stesso server.
- Se il mapping di rete è configurato correttamente e la replica è abilitata, una macchina virtuale presente nella posizione principale verrà connessa a una rete e la relativa replica nella posizione di destinazione verrà connessa alla rete mappata.
- Quando si seleziona una rete VM di destinazione durante il mapping di rete in Site Recovery, verranno visualizzati i cloud di origine VMM che usano la rete VM di origine insieme alle le reti VM di destinazione disponibili nei cloud di destinazione usati per la protezione.
- Se la rete di destinazione include più subnet e una di esse ha lo stesso nome della subnet in cui si trova la macchina virtuale di origine, la macchina virtuale di replica sarà connessa a tale subnet di destinazione dopo il failover. Se non è presente una subnet di destinazione con un nome corrispondente, la macchina virtuale sarà connessa alla prima subnet della rete.

## <a name="example"></a>Esempio

Di seguito è riportato un esempio per illustrare questo meccanismo. Si prenda come esempio un’organizzazione con due sedi, New York e Chicago.

**Posizione** | **Server VMM** | **Reti VM** | **Mappata a**
---|---|---|---
New York | VMM-NewYork| VMNetwork1-NewYork | Mappata a VMNetwork1-Chicago
 |  | VMNetwork2-NewYork | Non mappata
Chicago | VMM-Chicago| VMNetwork1-Chicago | Mappata a VMNetwork1-NewYork
 | | VMNetwork1-Chicago | Non mappata

Esempio:

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

### <a name="target-network-settings"></a>Impostazioni di rete di destinazione

In base a queste impostazioni, quando si seleziona la rete VM di destinazione, nella tabella seguente sono mostrate le opzioni che saranno disponibili.

**Selezionare** | **Cloud protetto** | **Protezione del cloud** | **Rete di destinazione disponibili**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Disponibile
 | GoldCloud1 | GoldCloud2 | Disponibile
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Non disponibile
 | GoldCloud1 | GoldCloud2 | Disponibile


Se la rete di destinazione dispone di più subnet e una di esse ha lo stesso nome della subnet in cui si trova la macchina virtuale di origine, la macchina virtuale di replica sarà connessa a tale subnet di destinazione dopo il failover. Se non è presente una subnet di destinazione con un nome corrispondente, la macchina virtuale sarà connessa alla prima subnet della rete.


### <a name="failback-behavior"></a>Comportamento di failback

Per vedere cosa succede in caso di failback (replica inversa), si supponga che venga eseguito il mapping di VMNetwork1-NewYork a VMNetwork1-Chicago, con le impostazioni seguenti.


**VM** | **Connessa alla rete VM**
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

- [Informazioni](hyper-v-vmm-networking.md) sulla configurazione dell'indirizzo IP dopo un failover in un sito VMM secondario.
- [Informazioni](concepts-on-premises-to-azure-networking.md) sulla configurazione dell'indirizzo IP dopo un failover in Azure.
