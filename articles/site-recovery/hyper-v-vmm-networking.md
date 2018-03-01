---
title: Configurare l'indirizzo IP per la connessione a un sito secondario locale dopo il failover con Azure Site Recovery | Microsoft Docs
description: Descrive come configurare l'indirizzo IP per la connessione alle macchine virtuali in un sito secondario locale dopo il failover con Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: rayne
ms.openlocfilehash: 531705bc704b3366c1c670ecf07c809ade67bc55
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>Configurare l'indirizzo IP per la connessione a un sito secondario locale dopo il failover

Dopo avere eseguito il failover delle macchine virtuali Hyper-V nei cloud System Center Virtual Machine Manager (VMM)in un sito secondario, è necessario riuscire a connettersi alle macchine virtuali di replica. In questo articolo viene indicato come eseguire questa operazione. 

## <a name="connection-options"></a>Opzioni di connessione

Dopo il failover, è possibile gestire gli indirizzi IP per le macchine virtuali di replica in un paio di modi: 

- **Mantenere lo stesso indirizzo IP dopo il failover**: in questo scenario la macchina virtuale di replica ha lo stesso indirizzo IP di quella primaria. Questa operazione semplifica i problemi correlati alla rete dopo il failover, ma richiede alcuni interventi di infrastruttura.
- **Usare un indirizzo IP diverso dopo il failover**: in questo scenario la macchina virtuale ottiene un nuovo indirizzo IP dopo il failover. 
 

## <a name="retain-the-ip-address"></a>Mantenere l'indirizzo IP

Se si desidera mantenere gli indirizzi IP dal sito primario, dopo il failover nel sito secondario è possibile:

- Distribuire una subnet estesa tra il database primario e i siti secondari.
- Eseguire un failover completo della subnet dal server primario al sito secondario. È necessario aggiornare le route per indicare la nuova posizione degli indirizzi IP.


### <a name="deploy-a-stretched-subnet"></a>Distribuire una subnet estesa

In una configurazione estesa la subnet è disponibile contemporaneamente nel sito primario e in quello secondario. Quando in una subnet con estensione si sposta una macchina e la configurazione degli indirizzi IP (livello 3) per il sito secondario, la rete indirizza automaticamente il traffico alla nuova posizione. 

- Dalla prospettiva del livello 2 (data-link layer), è necessario disporre di apparecchiature di rete in grado di gestire una VLAN estesa.
- Estendendo la VLAN, il dominio di errore potenziale si estende da entrambi i lati. Questo diventa un singolo punto di errore. Nonostante sia improbabile, in tale scenario potrebbe non essere possibile isolare un evento imprevisto come una broadcast storm. 


### <a name="fail-over-a-subnet"></a>Failover di una subnet

È possibile eseguire un failover di un'intera subnet per ottenere i vantaggi della subnet estesa senza estenderla realmente. In questa soluzione una subnet è disponibile nel sito di origine o di destinazione, ma non in entrambi contemporaneamente.

- Per mantenere lo spazio degli indirizzi IP in caso di failover, è possibile programmare l'infrastruttura del router per lo spostamento delle subnet da un sito a un altro.
- Quando si verifica un failover, le subnet si spostano con le rispettive macchine virtuali associate.
- Il principale svantaggio di questo approccio è che, in caso di errore, è necessario spostare l'intera subnet.

#### <a name="example"></a>Esempio

Di seguito è riportato un esempio di failover su subnet completo. 

- Prima del failover nel sito primario sono presenti applicazioni in esecuzione nella subnet 192.168.1.0/24.
- Durante il failover, tutte le macchine virtuali di questa subnet passano al sito secondario. Gli indirizzi IP vengono conservati. 
- Le route tra tutti i siti devono essere modificate in modo da rispecchiare il fatto che tutte le macchine virtuali appartenenti alla subnet 192.168.1.0/24 sono state spostate nel sito secondario.

Il grafico seguente mostra le subnet prima e dopo il failover.


**Prima del failover**

![Prima del failover](./media/hyper-v-vmm-networking/network-design2.png)

**Dopo il failover**

![Dopo il failover](./media/hyper-v-vmm-networking/network-design3.png)

Dopo il failover, Site Recovery alloca un indirizzo IP per ogni interfaccia di rete nella macchina virtuale. L'indirizzo viene allocato dal pool di indirizzi IP statici nella rete pertinente, per ogni istanza di macchina virtuale.

- Se il pool di indirizzi IP nel sito secondario è uguale a quello del sito di origine, Site Recovery alloca lo stesso indirizzo IP (della macchina virtuale di origine) nella macchina virtuale di replica. L'indirizzo IP è riservato in VMM, ma non è impostato come indirizzo IP di failover nell'host Hyper-V. L'indirizzo IP di failover nell'host Hyper-V viene impostato subito prima del failover.
- Se lo stesso indirizzo IP non è disponibile, Site Recovery dovrà allocare un altro indirizzo IP disponibile dal pool.
- Se le macchine virtuali usano DHCP, Site Recovery non gestisce gli indirizzi IP. È necessario verificare che il server DHCP nel sito secondario possa allocare gli indirizzi dallo stesso intervallo del sito di origine.

### <a name="validate-the-ip-address"></a>Convalidare l'indirizzo IP

Dopo aver abilitato la protezione per una macchina virtuale, è possibile usare i seguenti script di esempio per verificare l'indirizzo assegnato alla macchina virtuale. Lo stesso indirizzo IP viene impostato come indirizzo IP di failover e assegnato alla macchina vituale al momento del failover:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="use-a-different-ip-address"></a>Usare un indirizzo IP diverso

In questo scenario, vengono modificati gli indirizzi IP delle macchine virtuali di cui eseguire il failover. Lo svantaggio di questa soluzione è la manutenzione necessaria.  Potrebbe essere necessario aggiornare le voci DNS e della cache. Questo può determinare tempi di inattività, che possono essere ridotti come segue:

- Usare valori TTL bassi per le applicazioni Intranet.
- Usare lo script seguente in un piano di ripristino Site Recovery per un aggiornamento tempestivo del server DNS. Se si usa la registrazione DNS dinamica non è necessario lo script.

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

In questo esempio sono disponibili diversi indirizzi IP nei siti primari e secondari, oltre a un terzo sito da cui è possibile accedere alle applicazioni ospitate nel sito primario o di ripristino.

- Prima del failover, le app sono ospitate nella subnet 192.168.1.0/24 nel sito primario.
- Dopo il failover, le app sono configurate nella subnet 172.16.1.0/24 nel sito secondario.
- I tre siti sono mutualmente accessibili.
- Dopo il failover, le app verranno ripristinate nella subnet di ripristino.
- In questo scenario non occorre eseguire il failover dell'intera subnet e non sono necessarie modifiche per riconfigurare le route della VPN o di rete. Il failover e alcuni aggiornamenti DNS verificheranno che le applicazioni rimangano accessibili.
- Se il DNS è configurato per consentire aggiornamenti dinamici, le macchine virtuali si registreranno con il nuovo indirizzo IP una volta avviate dopo il failover.

**Prima del failover**

![Indirizzo IP diverso - Prima del failover](./media/hyper-v-vmm-networking/network-design10.png)

**Dopo il failover**

![Indirizzo IP diverso - Dopo il failover](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>Passaggi successivi

[Eseguire un failover](hyper-v-vmm-failover-failback.md)

