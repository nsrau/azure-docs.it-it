---
title: Mapping di rete tra due aree di Azure in Azure Site Recovery | Microsoft Docs
description: Azure Site Recovery coordina la replica, il failover e il ripristino di macchine virtuali e server fisici. Informazioni sul failover in Azure o in un centro dati secondario.
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/15/2017
ms.author: manayar
ms.openlocfilehash: bf3d557c77e3cb6ade6f1bb3773c807f9c8b43f6
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2017
---
# <a name="network-mapping-between-two-azure-regions"></a>Mapping di rete tra due aree di Azure


Questo articolo descrive come mappare tra loro le reti virtuali di Azure di due aree di Azure. Mapping di rete garantisce che, quando una macchina virtuale replicata viene creata nell'area di Azure di destinazione, viene creato nella rete virtuale che viene eseguito il mapping alla rete virtuale della macchina virtuale di origine.  

## <a name="prerequisites"></a>Prerequisiti
Prima eseguire il mapping di reti, assicurarsi di aver creato [reti virtuali di Azure](../virtual-network/virtual-networks-overview.md) sia di origine e destinazione aree di Azure.

## <a name="map-networks"></a>Mappare le reti

Per mappare una rete virtuale di Azure in un'area di Azure a un'altra rete virtuale in un'altra area, passare a Site Recovery Infrastructure (Infrastruttura di Site Recovery) -> Mapping di rete (per macchine virtuali di Azure) e creare un mapping di rete.

![Mapping di rete](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


Nell'esempio seguente, la macchina virtuale è in esecuzione nell'area Asia orientale e viene replicata in Asia sudorientale.

Selezionare l'origine e destinazione di rete e quindi fare clic su OK per creare un mapping di rete dell'Asia orientale Asia sudorientale.

![Mapping di rete](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


Ripetere il processo precedente per creare un mapping di rete Asia sudorientale Asia orientale.

![Mapping di rete](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="mapping-network-when-enabling-replication"></a>Mapping di rete durante l'abilitazione della replica

Se non è stato eseguito il mapping di rete quando si replicano una macchina virtuale per la prima volta da un'area di Azure a un altro, è possibile scegliere una rete di destinazione come parte dello stesso processo. Site Recovery crea mapping di rete dall'area di origine all'area di destinazione e dall'area di destinazione all'area di origine in base a questa selezione.   

![Mapping di rete](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

Per impostazione predefinita, Site Recovery crea una rete nell'area di destinazione che corrisponde alla rete di origine e aggiunge "-asr" come suffisso al nome della rete di origine. È possibile scegliere una rete già creata facendo clic su personalizza.

![Mapping di rete](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)


Se il mapping di rete è già stato eseguito, non è possibile modificare la rete virtuale di destinazione durante l'abilitazione della replica. Per cambiarla, modificare il mapping di rete esistente.  

![Mapping di rete](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![Mapping di rete](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> Se si modifica un mapping di rete dall'area 1 all'area 2, assicurarsi di modificare il mapping di rete anche dall'area 2 all'area 1.
>
>


## <a name="subnet-selection"></a>Selezione della subnet
La subnet della macchina virtuale di destinazione è selezionata in base al nome della subnet della macchina virtuale di origine. Se una subnet con lo stesso nome della macchina virtuale di origine è disponibile nella rete di destinazione, tale subnet viene scelto per la macchina virtuale di destinazione. Se è presente alcuna subnet con lo stesso nome di rete di destinazione, quindi in ordine alfabetico prima subnet viene scelto come la subnet di destinazione. È possibile modificare questa subnet passando alle impostazioni Calcolo e rete della macchina virtuale.

![Modificare la subnet](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>Indirizzo IP

Indirizzo IP per ogni interfaccia di rete della macchina virtuale di destinazione viene scelto come indicato di seguito:

### <a name="dhcp"></a>DHCP
Se l'interfaccia di rete della macchina virtuale di origine usa DHCP, anche l'interfaccia di rete della macchina virtuale di destinazione viene impostata come DHCP.

### <a name="static-ip"></a>IP statico
Se l'interfaccia di rete della macchina virtuale di origine usa un indirizzo IP statico, anche l'interfaccia di rete della macchina virtuale di destinazione viene impostata per usare un indirizzo IP statico. L'indirizzo IP statico viene scelto come indicato di seguito:

#### <a name="same-address-space"></a>Stesso spazio di indirizzi

Se la subnet di origine e la subnet di destinazione hanno lo stesso spazio di indirizzo, l'indirizzo IP dell'interfaccia di rete della macchina virtuale di origine è impostato come indirizzo IP di destinazione. Se lo stesso indirizzo IP non è disponibile, quindi l'indirizzo IP disponibile successivo viene impostato come indirizzo IP di destinazione.

#### <a name="different-address-space"></a>Spazio di indirizzi diverso

Se la subnet di origine e la subnet di destinazione dispone di spazi di indirizzi diverso, quindi il successivo indirizzo IP disponibile nella subnet di destinazione viene impostato come indirizzo IP di destinazione.

È possibile modificare l'indirizzo IP di destinazione in ogni interfaccia di rete, passare alle impostazioni di calcolo e rete della macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [indicazioni sulla rete per la replica di VM di Azure](site-recovery-azure-to-azure-networking-guidance.md).
