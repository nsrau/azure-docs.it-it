---
title: Eseguire il mapping di reti virtuali tra due aree di Azure in Azure Site Recovery | Microsoft Docs
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
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 8f347827c640729112e2e8f4c11288b6bcb176ea
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="map-virtual-networks-in-different-azure-regions"></a>Eseguire il mapping delle reti virtuali in aree di Azure differenti


Questo articolo descrive come eseguire il mapping tra due istanze della rete virtuale di Azure di due aree di Azure diverse. Il mapping di rete assicura che, quando nell'area di Azure di destinazione viene creata una macchina virtuale replicata, questa sia creata anche nella rete virtuale mappata alla rete virtuale della macchina virtuale di origine.  

## <a name="prerequisites"></a>prerequisiti
Prima di eseguire il mapping di reti, assicurarsi di aver creato una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) sia nell'area di origine che nell'area di destinazione di Azure.

## <a name="map-virtual-networks"></a>Mapping di reti virtuali

Per mappare una rete virtuale di Azure situata in un'area di Azure (rete di origine) a un'altra rete virtuale situata in un'altra area (rete di destinazione) per le macchine virtuali di Azure, passare a **Infrastruttura di Site Recovery** > **Mapping di rete**. Eseguire il mapping di una rete.

![Finestra Mapping di rete - Crea mapping di rete](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


Nell'esempio seguente, la macchina virtuale è in esecuzione nell'area Asia orientale. La macchina virtuale viene replicata nell'area Asia sud-orientale.

Per creare un mapping di rete dall'area Asia orientale nell'area Asia sud-orientale, selezionare il percorso della rete di origine e il percorso della rete di destinazione. Quindi selezionare **OK**.

![Dalla finestra Aggiungi mapping di rete, selezionare i percorsi di origine e di destinazione per la rete di origine](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


Ripetere il processo precedente per creare un mapping di rete dall'area Asia sud-orientale all'area Asia orientale.

![Dal riquadro Aggiungi mapping di rete, selezionare i percorsi di origine e di destinazione per la rete di destinazione](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-a-network-when-you-enable-replication"></a>Eseguire il mapping di una rete con replica abilitata

Quando si replica una macchina virtuale da un'area di Azure in un'altra area per la prima volta, se non esiste alcun mapping di rete è possibile impostare la rete di destinazione quando si configura la replica. Azure Site Recovery crea mapping di rete dall'area di origine all'area di destinazione e dall'area di destinazione all'area di origine in base a queste impostazioni.   

![Dal riquadro Configura impostazioni, scegliere il percorso di destinazione](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

Per impostazione predefinita, Site Recovery crea nell'area di destinazione una rete identica alla rete di origine. Site Recovery crea una rete mediante l'aggiunta di **-asr** come suffisso al nome della rete di origine. Per scegliere una rete già creata, selezionare **Personalizza**.

![Dal riquadro Personalizza impostazioni di destinazione, impostare il nome del gruppo di risorse di destinazione e quello della rete virtuale di destinazione](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)

Se il mapping di rete è già stato eseguito, non è possibile modificare la rete virtuale di destinazione durante l'abilitazione della replica. In questo caso, per modificare la rete virtuale di destinazione, modificare il mapping di rete esistente.  

![Dal riquadro Personalizza impostazioni di destinazione, impostare il nome del gruppo di risorse di destinazione](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![Dal riquadro Modificare il mapping di rete, modificare il nome di una rete virtuale di destinazione esistente](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> Se si modifica un mapping di rete dall'area A all'area B, assicurarsi di modificare anche il mapping di rete dall'area B all'area A.
>
>


## <a name="subnet-selection"></a>Selezione della subnet
La subnet della macchina virtuale di destinazione viene selezionata in base al nome della subnet della macchina virtuale di origine. Se nella rete di destinazione è presente una subnet con nome corrispondente a quello della macchina virtuale di origine, tale subnet verrà impostata per la macchina virtuale di destinazione. Se nella rete di destinazione non è presente alcuna subnet con lo stesso nome, verrà scelta come subnet di destinazione la prima subnet in ordine alfabetico.

Per modificare la subnet, passare alle impostazioni **Calcolo e rete** per la macchina virtuale.

![Finestra Calcolo e rete delle proprietà di calcolo](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>Indirizzo IP

L'indirizzo IP per ogni interfaccia di rete della macchina virtuale di destinazione è impostato come descritto nelle sezioni seguenti.

### <a name="dhcp"></a>DHCP
Se l'interfaccia di rete della macchina virtuale di origine usa DHCP, anche l'interfaccia di rete della macchina virtuale di destinazione viene impostata come DHCP.

### <a name="static-ip-address"></a>Indirizzo IP statico
Se l'interfaccia di rete della macchina virtuale di origine usa un indirizzo IP statico, anche l'interfaccia di rete della macchina virtuale di destinazione viene impostata come indirizzo IP statico. Le sezioni seguenti descrivono le modalità d'impostazione di un indirizzo IP statico.

#### <a name="same-address-space"></a>Stesso spazio di indirizzi

Se la subnet di origine e la subnet di destinazione hanno lo stesso spazio di indirizzi, per l'interfaccia di rete della macchina virtuale di origine verrà impostato un indirizzo IP identico a quello di destinazione. Se lo stesso indirizzo IP non è disponibile, verrà impostato come indirizzo IP di destinazione il successivo indirizzo IP disponibile.

#### <a name="different-address-spaces"></a>Spazi di indirizzi diversi

Se la subnet di origine e la subnet di destinazione hanno spazi di indirizzi diversi, come indirizzo IP di destinazione verrà impostato il successivo indirizzo IP disponibile nella subnet di destinazione.

Per modificare l'indirizzo IP di destinazione in ogni interfaccia di rete, passare alle impostazioni **Calcolo e rete** della macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

* Vedere il [materiale sussidiario del servizio di rete per la replica delle macchine virtuali di Azure](site-recovery-azure-to-azure-networking-guidance.md).
