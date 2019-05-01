---
title: Velocità effettiva delle macchine virtuali di Azure | Microsoft Docs
description: Informazioni sulla velocità effettiva di rete delle macchine virtuali di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: kumud
ms.openlocfilehash: 182b3b7dad828e67d006391e00986406729c959d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689246"
---
# <a name="virtual-machine-network-bandwidth"></a>Larghezza di banda della rete di macchine virtuali

Azure offre un'ampia gamma di dimensioni e tipi di macchine virtuali, ognuna con una diversa combinazione di prestazioni. Tra queste prestazioni c'è la velocità effettiva (o larghezza di banda) della rete, misurata in megabit al secondo (Mbps). Poiché le macchine virtuali sono ospitate su hardware condiviso, la capacità di rete deve essere condivisa equamente tra le macchine virtuali che condividono lo stesso hardware. Alle macchine virtuali di dimensioni maggiori viene allocata una larghezza di banda relativamente maggiore rispetto alle macchine virtuali più piccole.
 
La larghezza di banda di rete allocata a ogni macchina virtuale è misurata sul traffico in uscita dalla macchina virtuale. Tutto il traffico di rete che esce dalla macchina virtuale viene conteggiato per il limite allocato, indipendentemente dalla destinazione. Se, ad esempio, una macchina virtuale ha un limite di 1.000 Mbps, tale limite si applica sia che il traffico in uscita sia destinato a un'altra macchina virtuale nella stessa rete virtuale sia all'esterno di Azure.
 
Il traffico in ingresso non viene misurato o limitato direttamente. Tuttavia, esistono altri fattori, ad esempio i limiti di CPU e di archiviazione, che possono influire sulle capacità di una macchina virtuale di elaborare i dati in ingresso.

La rete accelerata è una funzione progettata per migliorare le prestazioni di rete, tra cui la latenza, la velocità effettiva e l'utilizzo della CPU. Mentre la rete accelerata può migliorare la velocità effettiva di una macchina virtuale, è possibile farlo solo fino alla larghezza di banda allocata alla macchina virtuale. Sono disponibili altre informazioni sulle rete accelerata per macchine virtuali [Windows](create-vm-accelerated-networking-powershell.md) o [Linux](create-vm-accelerated-networking-cli.md).
 
Le macchine virtuali di Azure devono avere almeno un'interfaccia collegata, ma possono averne anche molte. La larghezza di banda allocata a una macchina virtuale è la somma di tutto il traffico in uscita in tutte le interfacce di rete associate alla macchina virtuale. In altre parole, la larghezza di banda allocata è per macchina virtuale, indipendentemente dal numero di interfacce di rete collegate alla macchina virtuale. Per informazioni su quante interfacce di rete sono supportate dalle diverse dimensioni di macchina virtuale di Azure, vedere le dimensioni delle VM [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure. 

## <a name="expected-network-throughput"></a>Velocità effettiva della rete prevista

La velocità effettiva in uscita prevista e il numero di interfacce di rete supportati da ogni dimensione di VM sono descritti in dettaglio nelle informazioni sulle dimensioni delle VM [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure. Selezionare un tipo, ad esempio utilizzo generico, quindi selezionare una serie di dimensioni nella pagina risultante, ad esempio la serie Dv2. Ciascuna serie dispone di una tabella con specifiche di rete nell'ultima colonna, denominata **Schede di interfaccia di rete max/prestazioni rete previste (Mbps)**. 

Il limite di velocità effettiva si applica alla macchina virtuale. La velocità effettiva è influenzata dai fattori seguenti:
- **Numero di interfacce di rete**: Il limite di larghezza di banda è cumulativo di tutto il traffico in uscita dalla macchina virtuale.
- **Rete accelerata**: Anche se la funzionalità può essere utile per ottenere il limite pubblicato, il limite non viene modificato.
- **Destinazione del traffico**: Tutte le destinazioni contano per il limite in uscita.
- **Protocollo**: Tutto il traffico in uscita su tutti i protocolli conta per il limite.

## <a name="next-steps"></a>Passaggi successivi

- [Ottimizzare la velocità effettiva di rete per un sistema operativo di macchina virtuale](virtual-network-optimize-network-bandwidth.md)
- [Testare la velocità effettiva di rete](virtual-network-bandwidth-testing.md) per una macchina virtuale.
