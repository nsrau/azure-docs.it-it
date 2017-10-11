---
title: Pianificare la rete per la replica Hyper-V in un sito VMM secondario con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive la pianificazione della rete durante la replica di una macchina virtuale Hyper-V in un sito System Center VMM secondario con Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 095f2d73-994e-4fc0-96f2-e03a7d72e492
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: raynew
ms.openlocfilehash: a1f3f6e6cba074647195e2b0cbcdc7b4f3dec475
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="step-3-plan-networking-for-hyper-v-vm-replication-to-a-secondary-vmm-site"></a>Passaggio 3: Pianificare la rete per la replica della macchina virtuale Hyper-V in un sito secondario

Dopo aver analizzato i prerequisiti di distribuzione, leggere questo articolo per pianificare la rete quando si replicano in un sito secondario macchine virtuali Hyper-V gestite nei cloud di System Center Virtual Machine Manager (VMM) usando [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure. 

È possibile inviare commenti nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="network-mapping-overview"></a>Panoramica del mapping di rete

Il mapping di rete viene usato durante la replica delle macchine virtuali Hyper-V (gestite in VMM) in un centro dati secondario. Il mapping di rete esegue il mapping tra reti VM in un server VMM di origine e reti VM in un server VMM di destinazione. Il mapping esegue queste operazioni:

- **Connessione di rete**: esegue la connessione delle VM alle reti appropriate dopo il failover. La macchina virtuale di replica verrà connessa alla rete di destinazione di cui viene eseguito il mapping alla rete di origine.
- **Posizionamento ottimale**: posiziona in modo ottimale le VM di replica nei server host Hyper-V. Le macchine virtuali di replica vengono posizionate negli host che possono accedere alle reti di macchine virtuali mappate.
- **Nessun mapping di rete**: se non si configura il mapping di rete, le macchine virtuali replicate non risulteranno connesse ad alcuna rete di macchina virtuale in seguito al failover.


### <a name="example"></a>Esempio

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

#### <a name="cloud-protection-settings"></a>Impostazioni di protezione del cloud

**Cloud protetto** | **Protezione del cloud** | **Rete logica (New York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>ND</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>ND</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

#### <a name="logical-and-vm-network-settings"></a>Impostazioni di rete VM e logica

**Posizione** | **Rete logica** | **Rete VM associata**
---|---|---
New York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

#### <a name="target-network-settings"></a>Impostazioni di rete di destinazione

In base a queste impostazioni, quando si seleziona la rete VM di destinazione, nella tabella seguente sono mostrate le opzioni che saranno disponibili.

**Selezionare** | **Cloud protetto** | **Protezione del cloud** | **Rete di destinazione disponibili**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Disponibile
 | GoldCloud1 | GoldCloud2 | Disponibile
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Non disponibile
 | GoldCloud1 | GoldCloud2 | Disponibile


Se la rete di destinazione dispone di più subnet e una di esse ha lo stesso nome della subnet in cui si trova la macchina virtuale di origine, la macchina virtuale di replica sarà connessa a tale subnet di destinazione dopo il failover. Se non è presente una subnet di destinazione con un nome corrispondente, la macchina virtuale sarà connessa alla prima subnet della rete.


#### <a name="failback-behavior"></a>Comportamento di failback

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



## <a name="prepare-for-network-mapping"></a>Preparare il mapping di rete

1. Nei server VMM di origine e destinazione è necessario disporre di una rete logica associata ai cloud di origine e destinazione. 
2. Nei server di origine e destinazione è necessario disporre di una rete della macchina virtuale collegata alla rete logica.
3. Le macchine virtuali sugli host Hyper-V nella località di origine devono essere collegate alla rete della macchina virtuale di origine. Se si usa solo un singolo server VMM, è possibile configurare il mapping tra le reti delle macchine virtuali nello stesso server.

Di seguito è riportato ciò che accede se si configura il mapping di rete durante la distribuzione di Site Recovery:

- Quando si configura il mapping di rete e si seleziona una rete della macchina virtuale di destinazione, verranno visualizzati i cloud di origine VMM che usano la rete della macchina virtuale di origine insieme alle reti della macchina virtuale di destinazione disponibili nei cloud di destinazione.
- - Se il mapping di rete è configurato correttamente e la replica è abilitata, una macchina virtuale di origine verrà connessa alla rete della macchina virtuale di origine e la relativa replica nella località di destinazione verrà connessa alla rete mappata della macchina virtuale.
- Se la rete di destinazione dispone di più subnet e una di esse ha lo stesso nome della subnet in cui si trova la macchina virtuale di origine, la macchina virtuale di replica sarà connessa a tale subnet di destinazione dopo il failover. Se non è presente una subnet di destinazione con un nome corrispondente, la macchina virtuale sarà connessa alla prima subnet della rete.

## <a name="connect-to-vms-after-failover"></a>Connettersi alle macchine virtuali dopo il failover

Quando si pianifica la strategia di replica e di failover, una delle domande principali che ci si deve porre riguarda la modalità di connessione alla replica dopo il failover. Vi sono due possibilità: 

- **Usare un indirizzo IP diverso**: è possibile scegliere di usare un indirizzo IP diverso per la macchina virtuale replicata. In questo scenario, la VM ottiene un nuovo indirizzo IP dopo il failover ed è necessario un aggiornamento del DNS.
- **Mantenere lo stesso indirizzo IP**: è possibile usare lo stesso indirizzo IP per la macchina virtuale di replica. Il mantenimento degli stessi indirizzi IP semplifica il ripristino riducendo i problemi di rete dopo il failover. 

## <a name="retain-ip-addresses"></a>Mantenere gli indirizzi IP

Se si desidera mantenere gli indirizzi IP dal sito primario dopo il failover nel sito secondario, è possibile eseguire un failover completo della subnet e aggiornare le route per indicare la nuova località degli indirizzi IP, o in alternativa distribuire una subnet estesa tra i siti primari e di ripristino.

### <a name="stretched-subnet"></a>Subnet estesa

In una subnet estesa, la subnet è disponibile contemporaneamente sia nel sito primario che in quello secondario. Se si sposta un server e la relativa configurazione IP (livello 3) nel sito secondario, la rete instraderà automaticamente il traffico nella nuova località. 

Dalla prospettiva del livello 2 (data-link layer), è necessario disporre di apparecchiature di rete in grado di gestire una VLAN estesa. Inoltre, estendendo la VLAN, si estende il dominio di errore potenziale in entrambi i siti, creando in sostanza un singolo punto di errore. Tuttavia, l'eventualità di una broadcast storm impossibile da isolare è alquanto improbabile. 


### <a name="subnet-failover"></a>Failover sulla subnet

È possibile eseguire un failover su subnet per ottenere i vantaggi della subnet estesa senza effettivamente estenderla. In questa soluzione una subnet sarà disponibile nel sito di origine o destinazione, ma non in entrambi contemporaneamente. Per mantenere lo spazio degli indirizzi IP in caso di failover, è possibile programmare l'infrastruttura del router per lo spostamento delle subnet da un sito a un altro. Quando si verifica il failover, le subnet si spostano con le macchine virtuali associate. Il principale svantaggio è che in caso di errore è necessario spostare l'intera subnet.

### <a name="example"></a>Esempio

Di seguito è riportato un esempio di failover su subnet completo. Il sito primario dispone di applicazioni in esecuzione nella subnet 192.168.1.0/24. In caso di failover, viene eseguito il failover di tutte le macchine virtuali in questa subnet nel sito secondario. Gli indirizzi IP vengono conservati. Le route devono essere modificate in modo da rispecchiare il fatto che tutte le macchine virtuali appartenenti alla subnet 192.168.1.0/24 sono state spostate nel sito secondario.

Il grafico seguente mostra le subnet prima e dopo il failover:

- Prima del failover, la subnet 192.168.0.1/24 è attiva nel sito di origine e diventa attiva nel sito secondario dopo il failover.
- Le route tra il sito primario e il sito di ripristino, il terzo sito e il sito primario e il terzo sito e il sito di ripristino dovranno essere modificate in modo appropriato.

**Prima del failover**

![Prima del failover](./media/vmm-to-vmm-walkthrough-network/network-design2.png)

**Dopo il failover**

![Dopo il failover](./media/vmm-to-vmm-walkthrough-network/network-design3.png)

Di seguito è illustrato ciò che accade dopo il failover:

- Site Recovery consente di allocare un indirizzo IP per ciascuna interfaccia di rete nella macchina virtuale dal pool di indirizzi IP statici nella rete pertinente per ciascuna istanza VMM.
- Se il pool di indirizzi IP nel sito secondario è uguale a quello del sito di origine, Site Recovery alloca lo stesso indirizzo IP (della macchina virtuale di origine) alla macchina virtuale di replica. L'indirizzo IP è riservato in VMM, ma non è impostato come indirizzo IP di failover nell'host Hyper-V. L'indirizzo IP di failover nell'host Hyper-V viene impostato subito prima del failover.
- Se lo stesso indirizzo IP non è disponibile, Site Recovery dovrà allocare un altro indirizzo IP disponibile dal pool.
- Se le macchine virtuali usano DHCP, Site Recovery non gestisce gli indirizzi IP. È necessario verificare che il server DHCP nel sito secondario possa allocare l'indirizzo dallo stesso intervallo come sito di origine.

### <a name="validate-the-ip-address"></a>Convalidare l'indirizzo IP

Dopo aver abilitato la protezione per una macchina virtuale, è possibile usare i seguenti script di esempio per verificare l'indirizzo assegnato alla macchina virtuale. Lo stesso indirizzo IP verrà impostato come indirizzo IP di failover e assegnato alla macchina vituale al momento del failover:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="changing-ip-addresses"></a>Modifica degli indirizzi IP

In questo scenario, vengono modificati gli indirizzi IP delle macchine virtuali di cui eseguire il failover. Lo svantaggio di questa soluzione è la manutenzione necessaria. In genere, DNS verrà aggiornato dopo l'avvio delle macchine virtuali di replica. Le voci DNS potrebbero dover essere modificate nelle rete e le voci memorizzate nella cache aggiornate. Ciò può comportare tempi di inattività. Il tempo di inattività può essere contrastato come indicato di seguito:

- Usare valori TTL bassi per le applicazioni Intranet.
- Usare lo script seguente in un piano di ripristino Site Recovery per aggiornare il server DNS in modo da garantire un aggiornamento tempestivo. Se si usa la registrazione DNS dinamica non è necessario lo script.

    ```
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>Esempio 

Esaminiamo uno scenario in cui si prevede di usare diversi indirizzi IP nei siti primari e di recupero. In questo esempio sono disponibili diversi indirizzi IP nei siti primari e secondari, inoltre vi è un terzo sito da cui accedere alle applicazioni ospitate nel sito primario o di ripristino.

- Prima del failover, le app sono ospitate sulla subnet 192.168.1.0/24 nel sito primario e sono configurate per essere spostate sulla subnet 172.16.1.0/24 nel sito secondario dopo un failover.
- La configurazione dei percorsi di rete o delle connessioni VPN è avvenuta in modo corretto, pertanto tutti e tre i siti dispongono dell'accesso reciproco.
- Dopo il failover, le app verranno ripristinate nella subnet di ripristino. In questo scenario non occorre eseguire il failover dell'intera subnet e non sono necessarie modifiche per riconfigurare le route della VPN o di rete. Il failover e alcuni aggiornamenti DNS verificheranno che le applicazioni rimangano accessibili.
- Se il DNS è configurato per consentire aggiornamenti dinamici, le macchine virtuali si registreranno con il nuovo indirizzo IP una volta avviate dopo il failover.

**Prima del failover**

![Indirizzo IP diverso - Prima del failover](./media/vmm-to-vmm-walkthrough-network/network-design10.png)

**Dopo il failover**

![Indirizzo IP diverso - Dopo il failover](./media/vmm-to-vmm-walkthrough-network/network-design11.png)



## <a name="next-steps"></a>Passaggi successivi

Andare al [Passaggio 4: Preparare VMM e Hyper-V](vmm-to-vmm-walkthrough-vmm-hyper-v.md).


