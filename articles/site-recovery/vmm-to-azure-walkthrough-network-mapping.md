---
title: Configurare il mapping di rete per la replica in Azure di macchine virtuali Hyper-V in cloud VMM con Azure Site Recovery | Microsoft Docs
description: Descrive come configurare il mapping di rete quando si replicano in Azure macchine virtuali Hyper-V presenti in cloud VMM con Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: ed6f73d8baea5af0d2aa5f0ae885f305911ccc82
ms.contentlocale: it-it
ms.lasthandoff: 07/26/2017

---
# <a name="step-9-configure-network-mapping-for-hyper-v-replication-with-vmm-to-azure"></a>Passaggio 9: Configurare il mapping di rete per la replica Hyper-V (con VMM) in Azure

Dopo aver configurato le [impostazioni di replica di origine e destinazione](vmm-to-azure-walkthrough-source-target.md), usare le informazioni in questo articolo per configurare il mapping di rete in modo da eseguire il mapping tra reti di macchine virtuali VMM locali e reti di Azure.

Inserire commenti e domande nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Prima di iniziare

- Informazioni sul [mapping di rete](vmm-to-azure-walkthrough-network.md#network-mapping-for-replication-to-azure).
- [Preparare VMM per il mapping di rete](vmm-to-azure-walkthrough-network.md#prepare-vmm-for-network-mapping). 
- Verificare che le macchine virtuali nel server VMM siano connesse a una rete VM e di aver creato almeno una rete virtuale di Azure. È possibile mappare più reti VM a una singola rete di Azure.

## <a name="configure-mapping"></a>Configurare il mapping

Per configurare il mapping, procedere come segue:

1. In **Infrastruttura di Site Recovery** > **Mapping di rete** > **Mapping di rete** fare clic sull'icona **+Mapping di rete**.

    ![Mapping di rete](./media/vmm-to-azure-walkthrough-network-mapping/network-mapping1.png)
2. In **Aggiungi mapping di rete** selezionare il server VMM di origine e **Azure** come destinazione.
3. Verificare la sottoscrizione e il modello di distribuzione dopo il failover.
4. In **Rete di origine**selezionare la rete VM locale di origine di cui si vuole eseguire il mapping dall'elenco associato al server VMM.
5. In **Rete di destinazione**selezionare la rete di Azure in cui inserire le macchine virtuali di Azure di replica dopo la creazione. Fare quindi clic su **OK**.

    ![Mapping di rete](./media/vmm-to-azure-walkthrough-network-mapping/network-mapping2.png)

Quando ha inizio il mapping di rete vengono eseguite le operazioni seguenti:

* Le macchine virtuali esistenti nella rete VM di origine vengono connesse alla rete di destinazione quando ha inizio il mapping. Le nuove macchine virtuali connesse alla rete VM di origine vengono connesse alla rete di Azure di cui è stato eseguito il mapping quando viene eseguita la replica.
* Se si modifica un mapping di rete esistente, le macchine virtuali di replica si connettono usando le nuove impostazioni.
* Se la rete di destinazione dispone di più subnet e una di esse ha lo stesso nome di una subnet in cui si trova la macchina virtuale di origine, la macchina virtuale di replica si connette a questa subnet di destinazione dopo il failover.
* Se non è presente una subnet di destinazione con un nome corrispondente, la macchina virtuale si connette alla prima subnet della rete.



## <a name="next-steps"></a>Passaggi successivi

Andare al [Passaggio 10: Creare criteri di replica](vmm-to-azure-walkthrough-replication.md)

