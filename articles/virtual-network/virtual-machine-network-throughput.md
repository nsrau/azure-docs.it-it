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
ms.author: steveesp
ms.reviewer: kumud, mareat
ms.openlocfilehash: f5694e18d5743118e2b6e73708dd3acb17151198
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67874932"
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
- **Rete accelerata**: Sebbene la funzionalità possa essere utile per raggiungere il limite pubblicato, non modifica il limite.
- **Destinazione del traffico**: Tutte le destinazioni vengono conteggiate verso il limite in uscita.
- **Protocollo**: Tutto il traffico in uscita su tutti i protocolli viene conteggiato fino al limite.

## <a name="network-flow-limits"></a>Limiti dei flussi di rete

Oltre alla larghezza di banda, il numero di connessioni di rete presenti in una macchina virtuale in un determinato momento può influire sulle prestazioni della rete. Lo stack di rete di Azure mantiene lo stato di ogni direzione di una connessione TCP/UDP in strutture di dati denominate "Flows". Per una connessione TCP/UDP tipica vengono creati 2 flussi, uno per il traffico in ingresso e un altro per la direzione in uscita. 

Il trasferimento dei dati tra endpoint richiede la creazione di più flussi, oltre a quelli che eseguono il trasferimento dei dati. Alcuni esempi sono i flussi creati per la risoluzione DNS e i flussi creati per i probe di integrità del servizio di bilanciamento del carico. Si noti anche che le appliance virtuali di rete (appliance virtuali), come gateway, proxy e firewall, visualizzeranno i flussi creati per le connessioni terminate nel dispositivo e originate dall'appliance. 

![Numero di flussi per la conversazione TCP tramite un'appliance di invio](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>Limiti e raccomandazioni per il flusso

Attualmente, lo stack di rete di Azure supporta 250K totali dei flussi di rete con prestazioni ottimali per le macchine virtuali con più di 8 core CPU e 100.000 flussi totali con prestazioni ottimali per le macchine virtuali con meno di 8 core CPU. Oltre questo limite, le prestazioni di rete diminuiscono normalmente per i flussi aggiuntivi fino a un limite rigido di 1 milione di flussi totali, 500.000 in ingresso e 500.000 in uscita, dopo il quale vengono eliminati i flussi aggiuntivi.

||VM con < 8 core CPU|VM con più di 8 core CPU|
|---|---|---|
|<b>Prestazioni ottimali</b>|100K flussi |Flussi 250K|
|<b>Prestazioni ridotte</b>|Oltre 100.000 flussi|Sopra i flussi 250K|
|<b>Limite di flusso</b>|1M flussi|1M flussi|

Le metriche sono disponibili in [monitoraggio di Azure](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) per tenere traccia del numero di flussi di rete e della velocità di creazione del flusso nelle istanze della macchina virtuale o vmss.

![azure-monitor-flow-metrics.png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

La definizione della connessione e i tassi di terminazione possono anche influire sulle prestazioni di rete durante la creazione e la terminazione della connessione CPU con routine di elaborazione pacchetti. È consigliabile eseguire il benchmark dei carichi di lavoro in base ai modelli di traffico previsti e scalare i carichi di lavoro in modo appropriato per soddisfare le esigenze di prestazioni. 

## <a name="next-steps"></a>Passaggi successivi

- [Ottimizzare la velocità effettiva di rete per un sistema operativo di macchina virtuale](virtual-network-optimize-network-bandwidth.md)
- [Testare la velocità effettiva di rete](virtual-network-bandwidth-testing.md) per una macchina virtuale.
