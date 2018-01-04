---
title: "Velocità effettiva delle macchine virtuali di Azure | Microsoft Docs"
description: "Informazioni sulla velocità effettiva di rete delle macchine virtuali di Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: 7de85aa76dd449b97a5572f665d98378872eee88
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="virtual-machine-network-throughput"></a>Velocità effettiva di rete delle macchine virtuali

Azure offre un'ampia gamma di dimensioni e tipi di macchine virtuali, ognuna con una diversa combinazione di prestazioni. Una funzionalità è rete velocità effettiva (o larghezza di banda), misurata in megabit al secondo (Mbps). Poiché le macchine virtuali sono ospitate su hardware condiviso, la capacità di rete deve essere condivisa equamente tra le macchine virtuali che condividono lo stesso hardware. Alle macchine virtuali di dimensioni maggiori viene allocata una larghezza di banda relativamente maggiore rispetto alle macchine virtuali più piccole.
 
La larghezza di banda di rete allocata a ogni macchina virtuale è misurata sul traffico in uscita dalla macchina virtuale. Tutto il traffico di rete che esce dalla macchina virtuale viene conteggiato per il limite allocato, indipendentemente dalla destinazione. Se, ad esempio, una macchina virtuale ha un limite di 1.000 Mbps, tale limite si applica sia che il traffico in uscita sia destinato a un'altra macchina virtuale nella stessa rete virtuale sia all'esterno di Azure.
 
Il traffico in ingresso non viene misurato o limitato direttamente. Tuttavia, esistono altri fattori, ad esempio i limiti di CPU e di archiviazione, che possono influire sulle capacità di una macchina virtuale di elaborare i dati in ingresso.

Rete accelerata è una funzionalità progettata per migliorare le prestazioni di rete, tra cui la latenza, velocità effettiva e l'utilizzo della CPU. Mentre la rete accelerata può migliorare la velocità effettiva di una macchina virtuale, è possibile farlo solo fino alla larghezza di banda allocata alla macchina virtuale. Per ulteriori informazioni sulle reti Accelerated, vedere Accelerated di rete per [Windows](create-vm-accelerated-networking-powershell.md) o [Linux](create-vm-accelerated-networking-cli.md) macchine virtuali.
 
Le macchine virtuali di Azure devono avere almeno un'interfaccia collegata, ma possono averne anche molte. La larghezza di banda allocata a una macchina virtuale è la somma di tutto il traffico in uscita in tutte le interfacce di rete associate alla macchina virtuale. In altre parole, la larghezza di banda allocata è per macchina virtuale, indipendentemente dal numero di interfacce di rete collegate alla macchina virtuale. Per informazioni su quante interfacce di rete sono supportate dalle diverse dimensioni di macchina virtuale di Azure, vedere le dimensioni delle VM [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure. 

## <a name="expected-network-throughput"></a>Velocità effettiva della rete prevista

La velocità effettiva in uscita prevista e il numero di interfacce di rete supportati da ogni dimensione di VM sono descritti in dettaglio nelle informazioni sulle dimensioni delle VM [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure. Selezionare un tipo, ad esempio utilizzo generico, quindi selezionare una serie di dimensioni nella pagina risultante, ad esempio la serie Dv2. Ciascuna serie dispone di una tabella con specifiche di rete nell'ultima colonna, denominata **Schede di interfaccia di rete max/prestazioni rete previste (Mbps)**. 

Il limite di velocità effettiva si applica alla macchina virtuale. La velocità effettiva è influenzata dai fattori seguenti:
- **Numero di interfacce di rete**: il limite di larghezza di banda è cumulativo per tutto il traffico in uscita dalla macchina virtuale.
- **Accelerazione di rete**: anche se la funzione può essere utile per ottenere il limite pubblicato, non modifica il limite.
- **Destinazione del traffico**: tutte le destinazioni contano per il limite in uscita.
- **Protocollo**: tutto il traffico in uscita su tutti i protocolli conta per il limite.

## <a name="next-steps"></a>Passaggi successivi

- [Ottimizzare la velocità effettiva di rete per un sistema operativo di macchina virtuale](virtual-network-optimize-network-bandwidth.md)
- [Testare la velocità effettiva di rete](virtual-network-bandwidth-testing.md) per una macchina virtuale.