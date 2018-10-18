---
title: Eseguire il mapping di reti virtuali tra due aree di Azure in Azure Site Recovery | Microsoft Docs
description: Azure Site Recovery coordina la replica, il failover e il ripristino di macchine virtuali e server fisici. Informazioni sul failover in Azure o in un centro dati secondario.
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
editor: ''
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/16/2018
ms.author: mayg
ms.openlocfilehash: 95e6a388d0638d2fd477d33aaf7c39cf120e29aa
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353433"
---
# <a name="map-virtual-networks-in-different-azure-regions"></a>Eseguire il mapping delle reti virtuali in aree di Azure differenti


Questo articolo descrive come eseguire il mapping tra due istanze della rete virtuale di Azure di due aree di Azure diverse. Il mapping di rete assicura che, quando nell'area di Azure di destinazione viene creata una macchina virtuale replicata, questa sia creata anche nella rete virtuale mappata alla rete virtuale della macchina virtuale di origine.  

## <a name="prerequisites"></a>Prerequisiti
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

### <a name="ip-assignment-behavior-during-failover"></a>Modalità di assegnazione dell'indirizzo IP durante il failover
#### <a name="1-same-address-space"></a>1. Stesso spazio di indirizzi

Se la subnet di origine e la subnet di destinazione hanno lo stesso spazio di indirizzi, per l'interfaccia di rete della macchina virtuale di origine verrà impostato un indirizzo IP identico a quello di destinazione. Se lo stesso indirizzo IP non è disponibile, verrà impostato come indirizzo IP di destinazione il successivo indirizzo IP disponibile.

#### <a name="2-different-address-spaces"></a>2. Spazi di indirizzi diversi

Se la subnet di origine e la subnet di destinazione hanno spazi di indirizzi diversi, come indirizzo IP di destinazione verrà impostato il successivo indirizzo IP disponibile nella subnet di destinazione.


### <a name="ip-assignment-behavior-during-test-failover"></a>Modalità di assegnazione dell'indirizzo IP durante il failover di test
#### <a name="1-if-the-target-network-chosen-is-the-production-vnet"></a>1. Se la rete di destinazione selezionata è la rete virtuale di produzione
- L'indirizzo IP di ripristino (IP di destinazione) è un indirizzo IP statico, ma **non coincide con l'indirizzo IP** riservato per il failover.
- L'indirizzo IP assegnato è il successivo indirizzo IP disponibile dalla fine dell'intervallo di indirizzi della subnet.
- Si supponga, ad esempio, che come indirizzo IP statico della VM di origine sia configurato 10.0.0.19 e che sia stato tentato il failover di test con la rete di produzione configurata: ***dr-PROD-nw***, con intervallo di subnet 10.0.0.0/24. </br>
Alla VM sottoposta a failover verrà assegnato il successivo indirizzo IP disponibile dalla fine dell'intervallo di indirizzi della subnet, ovvero 10.0.0.254. </br>

**Nota:** il termine **rete virtuale di produzione** si riferisce alla "rete di destinazione" mappata durante la configurazione del ripristino di emergenza.
#### <a name="2-if-the-target-network-chosen-is-not-the-production-vnet-but-has-the-same-subnet-range-as-production-network"></a>2. Se la rete di destinazione selezionata non è la rete virtuale di produzione, ma ha lo stesso intervallo di subnet della rete di produzione

- L'indirizzo IP di ripristino (IP di destinazione) è un indirizzo IP statico, ma **coincide con l'indirizzo IP** (ovvero l'indirizzo IP statico configurato) riservato per il failover, a condizione che lo stesso indirizzo IP sia disponibile.
- Se l'indirizzo IP statico configurato è già assegnato a un'altra VM o a un altro dispositivo, l'indirizzo IP di ripristino sarà il successivo indirizzo IP disponibile dalla fine dell'intervallo di indirizzi della subnet.
- Si supponga, ad esempio, che come indirizzo IP statico della VM di origine sia configurato 10.0.0.19 e che sia stato tentato il failover di test con una rete di test: ***dr-NON-PROD-nw***, con lo stesso intervallo di subnet della rete di produzione, 10.0.0.0/24. </br>
  Alla VM sottoposta a failover verrà assegnato l'indirizzo IP statico seguente </br>
    - Indirizzo IP statico configurato: 10.0.0.19, se disponibile.
    - Indirizzo IP disponibile successivo: 10.0.0.254, se 10.0.0.19 è già in uso.


Per modificare l'indirizzo IP di destinazione in ogni interfaccia di rete, passare alle impostazioni **Calcolo e rete** della macchina virtuale.</br>
Come procedura consigliata è sempre preferibile scegliere una rete di test per eseguire il failover di test.
## <a name="next-steps"></a>Passaggi successivi

* Vedere il [materiale sussidiario del servizio di rete per la replica delle macchine virtuali di Azure](site-recovery-azure-to-azure-networking-guidance.md).
