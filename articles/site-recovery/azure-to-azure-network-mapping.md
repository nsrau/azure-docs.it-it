---
title: Eseguire il mapping di reti virtuali tra due aree di Azure in Azure Site Recovery | Microsoft Docs
description: Azure Site Recovery coordina la replica, il failover e il ripristino di macchine virtuali e server fisici. Informazioni sul failover in Azure o in un centro dati secondario.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: fccc7379794b4b75ff53e517eddd95ff0f7db0e9
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223783"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>Configurare il mapping di rete e gli indirizzi IP per reti virtuali

Questo articolo descrive come eseguire il mapping tra due istanze delle reti virtuali di Azure (VNet) di due aree di Azure diverse e come configurare gli indirizzi IP tra le reti. Il mapping della rete assicura che una machina virtuale (VM) replicata sia creata nell’area di destinazione Azure e sia creata nella VNet che è mappata sulla VNet della VM sorgente.

## <a name="prerequisites"></a>Prerequisiti

Prima eseguire il mapping di reti, è necessario disporre di [reti virtuali di Azure](../virtual-network/virtual-networks-overview.md) nelle aree di origine e di destinazione di Azure. 

## <a name="set-up-network-mapping"></a>Configurare il mapping di rete

Eseguire il mapping delle reti nel modo seguente:

1. Nelle **infrastruttura di Site Recovery**, fare clic su **+ Mapping di rete**.

    ![ Eseguire il mapping di una rete](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. In **Aggiungi mapping di rete** selezionare le posizioni di origine e di destinazione. In questo esempio, l'origine della macchina virtuale è in esecuzione nell'area dell’Asia orientale e replicata nell'area dell’Asia sudorientale.

    ![Selezionare origine e destinazione ](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. Creare ora un mapping di rete nella directory opposta. In questo esempio, l'origine sarà ora Asia sudorientale e la destinazione sarà Asia orientale.

    ![Dal riquadro Aggiungi mapping di rete, selezionare i percorsi di origine e di destinazione per la rete di destinazione](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>Eseguire il mapping di reti con replica abilitata

Se non è stato preparato il mapping della rete prima di configurare il ripristino di emergenza per le macchine virtuali Azure, è possibile specificare una rete di destinazione quando [si configura e si attiva la replica](azure-to-azure-how-to-enable-replication.md). Quando si esegue questa operazione, si verifica quanto segue:

- A seconda della destinazione selezionata, Site Recovery crea automaticamente i mapping di rete dall'origine all'area di destinazione e dalla destinazione all'area di origine.
- Per impostazione predefinita, Site Recovery crea nell'area di destinazione una rete identica alla rete di origine. Site Recovery aggiunge **-asr** come suffisso al nome della rete di origine. È possibile personalizzare la rete di destinazione.
- Se il mapping di rete è già stato eseguito, non è possibile modificare la rete virtuale di destinazione durante l'abilitazione della replica. Per modificare la rete virtuale di destinazione, è necessario modificare il mapping di rete esistente.
- Se si modifica un mapping di rete dall'area A all'area B, assicurarsi di modificare anche il mapping di rete dall'area B all'area A. ]

## <a name="specify-a-subnet"></a>Specificare una subnet

La subnet della macchina virtuale di destinazione viene selezionata in base al nome della subnet della macchina virtuale di origine.

- Se nella rete di destinazione è presente una subnet con nome corrispondente a quello della subnet della macchina virtuale di origine, tale subnet verrà impostata per la macchina virtuale di destinazione.
- Se nella rete di destinazione non è presente alcuna subnet con lo stesso nome, verrà scelta come subnet di destinazione la prima subnet in ordine alfabetico.
- La subnet può essere modificata nelle impostazioni **Calcolo e rete** per la macchina virtuale.

    ![Finestra Calcolo e rete delle proprietà di calcolo](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>Configurare gli indirizzi IP per le macchine virtuali di destinazione

L'indirizzo IP per ogni scheda NIC in una macchina virtuale di destinazione è configurato come indicato di seguito:

- **DHCP**: Se la scheda NIC della macchina virtuale di origine usa DHCP, la scheda NIC della macchina virtuale di destinazione viene impostata anche per utilizzare DHCP.
- **Indirizzo IP statico**: Se la scheda NIC della macchina virtuale di origine usa indirizzi IP statici, la scheda NIC dell’interfaccia di rete di destinazione utilizzerà anche un indirizzo IP statico.


## <a name="ip-address-assignment-during-failover"></a>Assegnazione indirizzo IP durante il failover

**Subnet di origine e di destinazione** | **Dettagli**
--- | ---
Stesso spazio di indirizzi | L'indirizzo IP della scheda NIC della macchia virtuale di origine è impostato come indirizzo IP della scheda NIC della macchia virtuale di destinazione.<br/><br/> Se lo stesso non è disponibile, verrà impostato come indirizzo IP di destinazione il successivo disponibile.
Spazio di indirizzi diverso<br/><br/> Il successivo indirizzo IP disponibile nella subnet di destinazione viene impostato come indirizzo NIC della macchina virtuale di destinazione.



## <a name="ip-address-assignment-during-test-failover"></a>Assegnazione indirizzo IP durante il failover di test

**Rete di destinazione** | **Dettagli**
--- | ---
Rete di destinazione è la rete virtuale di failover | - L'indirizzo IP di destinazione è statico, ma non lo stesso indirizzo IP di quello riservato al failover.<br/><br/>  - L'indirizzo assegnato è l’indirizzo successivo disponibile dalla fine dell'intervallo di subnet.<br/><br/> Ad esempio:  Se l'indirizzo IP di origine è 10.0.0.19 e la rete di failover usa l’intervallo 10.0.0.0/24, allora il successivo indirizzo IP assegnato alla macchina virtuale di destinazione è 10.0.0.254.
La rete di destinazione è la rete virtuale di failover | - L'indirizzo IP di destinazione sarà statico con lo stesso indirizzo IP riservato al failover.<br/><br/>  - Se lo stesso indirizzo IP è già assegnato, allora l'indirizzo IP è il successivo disponibile in ogni intervallo di subnet.<br/><br/> Ad esempio:  Se l'indirizzo IP statico di origine è 10.0.0.19 e il failover è in una rete che non sia la rete di failover, con l’intervallo 10.0.0.0/24, allora l'indirizzo IP statico di destinazione sarà 10.0.0.0.19 se disponibile e in caso contrario sarà 10.0.0.254.

- Il failover della rete virtuale è la rete di destinazione che si seleziona quando si configura il ripristino di emergenza.
- È consigliabile usare sempre una rete non di produzione per il failover di test.
- È possibile modificare l'indirizzo IP di destinazione nelle impostazioni **Calcolo e rete** della macchina virtuale.


## <a name="next-steps"></a>Passaggi successivi

- Consultare il [materiale sussidiario sulla rete](site-recovery-azure-to-azure-networking-guidance.md) per il ripristino di emergenza di macchine virtuali di Azure.
- [Ulteriori informazioni](site-recovery-retain-ip-azure-vm-failover.md) sulla conservazione degli indirizzi IP dopo il failover.

Se la rete di destinazione scelta è il failover vnet" e il secondo punto per dire "Se la rete di destinazione scelta è diversa dal failover vnet ma ha lo stesso intervallo di subnet del failover vnet"