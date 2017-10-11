---
title: Mappare reti per la replica di VM Hyper-V VM in un sito secondario con Azure Site Recovery | Microsoft Docs
description: Descrive come mappare le reti durante la replica di VM Hyper-V in un sito VMM secondario con Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 461b7c1c-ef61-4005-aeec-2324e727a3d0
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 606e2d3d0e31b9d80200105e812f9d7bbbcf939c
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="step-7-map-networks-for-hyper-v-vm-replication-to-a-secondary-site"></a>Passaggio 7: Mappare reti per la replica di VM Hyper-V in un sito secondario


Dopo avere configurato le [impostazioni di origine e di destinazione](vmm-to-vmm-walkthrough-source-target.md) per la replica di macchine virtuali Hyper-V in un sito System Center Virtual Machine Manager (VMM) secondario, usare le istruzioni disponibili in questo articolo per configurare il mapping di rete per la replica di macchine virtuali Hyper-V in un sito secondario tramite [Azure Site Recovery](site-recovery-overview.md).

È possibile inviare commenti nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Prima di iniziare

- Leggere le informazioni sul [mapping di rete](vmm-to-vmm-walkthrough-network.md#network-mapping-overview) prima di iniziare.
- Verificare che le macchine virtuali sui server VMM siano connesse a una rete VM.

## <a name="configure-network-mapping"></a>Configurare il mapping di rete

1. In **Mapping di rete** > **Mapping di rete** fare clic su **+Mapping di rete**.
2. Nella scheda **Aggiungi mapping di rete** selezionare i server VMM di origine e di destinazione. Vengono recuperate le reti VM associate ai server VMM.
3. In **Rete di origine**selezionare la rete che si vuole usare nell'elenco di reti VM associate al server VMM primario.
4. In **Rete di destinazione** selezionare la rete che si vuole usare nel server VMM secondario. Fare quindi clic su **OK**.

    ![Mapping di rete](./media/vmm-to-vmm-walkthrough-network-mapping/network-mapping2.png)

Quando ha inizio il mapping di rete vengono eseguite le operazioni seguenti:

* Tutte le macchine virtuali di replica esistenti che corrispondono alla rete VM di origine verranno connesse alle reti VM di destinazione.
* Dopo la replica, le nuove macchine virtuali connesse alla rete VM di origine verranno connesse alla rete mappata di destinazione.
* Se si modifica un mapping esistente con una nuova rete, le macchine virtuali di replica verranno connesse usando le nuove impostazioni.
* Se la rete di destinazione dispone di più subnet e una di esse ha lo stesso nome di una subnet in cui si trova la macchina virtuale di origine, la macchina virtuale di replica sarà connessa a tale subnet di destinazione dopo il failover. Se non è presente una subnet di destinazione con un nome corrispondente, la macchina virtuale sarà connessa alla prima subnet della rete.



## <a name="next-steps"></a>Passaggi successivi

Andare al [Passaggio 8: Configurare un criterio di replica](vmm-to-vmm-walkthrough-replication.md).