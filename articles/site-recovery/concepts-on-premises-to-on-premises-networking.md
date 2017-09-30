---
title: "Connettività delle macchine virtuali dopo il failover in un sito secondario con Azure Site Recovery | Microsoft Docs"
description: Linee guida relative ai requisiti di rete per la connessione alle macchine virtuali dopo il failover in un sito secondario con Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 67d73590-185c-49b2-a097-597bf54747a9
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 7b27fc568c77b44ab2366d297ca9e7685439143e
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="vm-connectivity-after-failover-to-a-secondary-site"></a>Connettività delle macchine virtuali dopo il failover in un sito secondario

Dopo aver analizzato i prerequisiti di distribuzione, leggere questo articolo per pianificare la rete quando si replicano in un sito secondario macchine virtuali Hyper-V gestite nei cloud di System Center Virtual Machine Manager (VMM) usando [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure. 

È possibile inviare commenti nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Panoramica

Quando si pianifica la strategia di replica e di failover, una delle domande principali che ci si deve porre riguarda la modalità di connessione alla replica dopo il failover. Vi sono due possibilità: 

- **Usare un indirizzo IP diverso**: è possibile scegliere di usare un indirizzo IP diverso per la macchina virtuale replicata. In questo scenario, la VM ottiene un nuovo indirizzo IP dopo il failover ed è necessario un aggiornamento del DNS.
- **Mantenere lo stesso indirizzo IP**: è possibile usare lo stesso indirizzo IP per la macchina virtuale di replica. Il mantenimento degli stessi indirizzi IP semplifica il ripristino riducendo i problemi di rete dopo il failover. 

## <a name="retaining-ip-addresses"></a>Mantenimento degli indirizzi IP

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





