---
title: Velocità effettiva delle macchine virtuali di Azure | Microsoft Docs
description: Informazioni sulla velocità effettiva di rete delle macchine virtuali di Azure.
services: virtual-network
documentationcenter: na
author: steveesp
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/26/2019
ms.author: kumud,steveesp, mareat
ms.openlocfilehash: 9d74e53c754367ecfa63642514db93354fcadf25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153749"
---
# <a name="virtual-machine-network-bandwidth"></a>Larghezza di banda della rete di macchine virtuali

Azure offre un'ampia gamma di dimensioni e tipi di macchine virtuali, ognuna con una diversa combinazione di prestazioni. Tra queste prestazioni c'è la velocità effettiva (o larghezza di banda) della rete, misurata in megabit al secondo (Mbps). Poiché le macchine virtuali sono ospitate su hardware condiviso, la capacità di rete deve essere condivisa equamente tra le macchine virtuali che condividono lo stesso hardware. Alle macchine virtuali di dimensioni maggiori viene allocata una larghezza di banda relativamente maggiore rispetto alle macchine virtuali più piccole.
 
La larghezza di banda di rete allocata a ogni macchina virtuale è misurata sul traffico in uscita dalla macchina virtuale. Tutto il traffico di rete che esce dalla macchina virtuale viene conteggiato per il limite allocato, indipendentemente dalla destinazione. Se, ad esempio, una macchina virtuale ha un limite di 1.000 Mbps, tale limite si applica sia che il traffico in uscita sia destinato a un'altra macchina virtuale nella stessa rete virtuale sia all'esterno di Azure.
 
Il traffico in ingresso non viene misurato o limitato direttamente. Tuttavia, esistono altri fattori, ad esempio i limiti di CPU e di archiviazione, che possono influire sulle capacità di una macchina virtuale di elaborare i dati in ingresso.

La rete accelerata è una funzione progettata per migliorare le prestazioni di rete, tra cui la latenza, la velocità effettiva e l'utilizzo della CPU. Mentre la rete accelerata può migliorare la velocità effettiva di una macchina virtuale, è possibile farlo solo fino alla larghezza di banda allocata alla macchina virtuale. Sono disponibili altre informazioni sulle rete accelerata per macchine virtuali [Windows](create-vm-accelerated-networking-powershell.md) o [Linux](create-vm-accelerated-networking-cli.md).
 
Le macchine virtuali di Azure devono avere almeno un'interfaccia collegata, ma possono averne anche molte. La larghezza di banda allocata a una macchina virtuale è la somma di tutto il traffico in uscita in tutte le interfacce di rete associate alla macchina virtuale. In altre parole, la larghezza di banda allocata è per macchina virtuale, indipendentemente dal numero di interfacce di rete collegate alla macchina virtuale. Per informazioni su quante interfacce di rete sono supportate dalle diverse dimensioni di macchina virtuale di Azure, vedere le dimensioni delle VM [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure. 

## <a name="expected-network-throughput"></a>Velocità effettiva della rete prevista

La velocità effettiva in uscita prevista e il numero di interfacce di rete supportati da ogni dimensione di VM sono descritti in dettaglio nelle informazioni sulle dimensioni delle VM [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure. Selezionare un tipo, ad esempio utilizzo generico, quindi selezionare una serie di dimensioni nella pagina risultante, ad esempio la serie Dv2. Ciascuna serie dispone di una tabella con specifiche di rete nell'ultima colonna, denominata **Schede di interfaccia di rete max/prestazioni rete previste (Mbps)** . 

Il limite di velocità effettiva si applica alla macchina virtuale. La velocità effettiva è influenzata dai fattori seguenti:
- **Numero di interfacce di rete**: Il limite di larghezza di banda è cumulativo di tutto il traffico in uscita dalla macchina virtuale.
- **Rete accelerata**: Anche se la funzionalità può essere utile per ottenere il limite pubblicato, il limite non viene modificato.
- **Destinazione del traffico**: Tutte le destinazioni contano per il limite in uscita.
- **Protocollo**: Tutto il traffico in uscita su tutti i protocolli conta per il limite.

## <a name="network-flow-limits"></a>Limiti di flusso di rete

Oltre a larghezza di banda, il numero di connessioni di rete presente in una macchina virtuale in un determinato momento può influire sulle prestazioni di rete. Lo stack di rete di Azure mantiene lo stato per ogni direzione di una connessione TCP/UDP in strutture di dati denominato 'flussi'. Una connessione TCP/UDP tipica avrà 2 flussi creati, uno per l'entrata e l'altro per la direzione in uscita. 

Trasferimento dei dati tra gli endpoint richiede la creazione di flussi diversi oltre a quelli che eseguono il trasferimento dei dati. Alcuni esempi sono i flussi creati per la risoluzione DNS e i flussi creati per i probe di integrità di load balancer. Si noti che dispositivi di rete virtuale (Nva), ad esempio gateway, i proxy, firewall, vedere anche i flussi da creare per le connessioni terminate all'appliance e ha avuto origine dall'appliance. 

![Numero di flusso per la conversazione TCP attraverso un'appliance di inoltro](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>Limiti di flusso e raccomandazioni

Oggi, lo stack di rete di Azure supporta i flussi di rete totale 250 KB con buone prestazioni per le macchine virtuali con più di 8 core di CPU e 100 KB passa totale con buone prestazioni per le macchine virtuali con meno di 8 core di CPU. Oltre a questa rete limite peggioramento delle prestazioni normalmente per i flussi aggiuntivi fino a un limite rigido di 1 milione totale flussi, 500 KB in ingresso e 500 K in uscita, dopo il quale i flussi aggiuntivi vengono eliminati.

||Le macchine virtuali con < 8 core di CPU|Macchine virtuali con core CPU 8 +|
|---|---|---|
|<b>Buone prestazioni</b>|100K flussi |Flussi di 250 KB|
|<b>Riduzione delle prestazioni</b>|Di sopra di 100 KB flussi|Precedenza 250K flussi|
|<b>Limite di flusso</b>|Flussi di 1 milione|Flussi di 1 milione|

Le metriche sono disponibili nel [monitoraggio di Azure](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) per tenere traccia del numero di flussi di rete e la frequenza di creazione del flusso in istanze di macchina virtuale o VMSS.

![azure-monitor-flow-metrics.png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

Le velocità di terminazione e stabilire una connessione possono influire sulle prestazioni di rete come connessione definizione e la terminazione condivisioni CPU con le routine di elaborazione dei pacchetti. È consigliabile che si effettuino un benchmark carichi di lavoro in modelli di traffico previsto e scalare orizzontalmente carichi di lavoro in modo appropriato alle esigenze di prestazioni. 

## <a name="next-steps"></a>Passaggi successivi

- [Ottimizzare la velocità effettiva di rete per un sistema operativo di macchina virtuale](virtual-network-optimize-network-bandwidth.md)
- [Testare la velocità effettiva di rete](virtual-network-bandwidth-testing.md) per una macchina virtuale.
