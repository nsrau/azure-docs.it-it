---
title: Abilitare la replica per un disco aggiunto a una VM di Azure replicate da Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come abilitare la replica per un disco aggiunto a una macchina virtuale di Azure che è abilitato per il ripristino di emergenza con Azure Site Recovery
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: asgang
ms.openlocfilehash: 69122ffe9cefa3e1b9c6c8fbadfa80492ebebbde
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928063"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Abilitare la replica per un disco aggiunto a una VM di Azure


Questo articolo descrive come abilitare la replica per i dischi dati che vengono aggiunti a una macchina virtuale di Azure che è già abilitato per il ripristino di emergenza in un'altra area di Azure, usando [Azure Site Recovery](site-recovery-overview.md).

Abilitazione della replica per un disco che è aggiungere a una macchina virtuale è supportata per macchine virtuali di Azure con dischi gestiti.

Quando si aggiunge un nuovo disco a una macchina virtuale di Azure che esegue la replica in un'altra area di Azure, si verifica quanto segue:

-   Integrità della replica per la macchina virtuale viene visualizzato un avviso e una nota nel portale informa che uno o più dischi sono disponibili per la protezione.
-   Se si abilita la protezione per i dischi aggiunti, l'avviso scompare dopo la replica iniziale del disco.
-   Se si sceglie di non abilitare la replica per il disco, è possibile selezionare per ignorare l'avviso.

![Nuovo disco aggiunto](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Prima di iniziare

Questo articolo presuppone che aver già configurato il ripristino di emergenza per la macchina virtuale a cui si aggiunge il disco. Se hai fatto, seguire le [esercitazione di ripristino di emergenza da Azure ad Azure](azure-to-azure-tutorial-enable-replication.md). 

## <a name="enable-replication-for-an-added-disk"></a>Abilitare la replica per un disco aggiunto 

Per abilitare la replica per un disco aggiunto, eseguire le operazioni seguenti:

1. Nell'insieme di credenziali > **elementi replicati**, scegliere la macchina virtuale a cui è stato aggiunto il disco.
2. Fare clic su **dischi**e quindi selezionare il disco dati per il quale si desidera abilitare la replica (questi dischi hanno un **non protette** stato).
3.  Nelle **dettagli disco**, fare clic su **abilita la replica**.

    ![Abilitare la replica disco aggiunto](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

Viene eseguito il processo di abilitazione della replica e la replica iniziale non viene completata, l'avviso di integrità della replica per il problema del disco viene rimosso.



# <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](site-recovery-test-failover-to-azure.md) sull'esecuzione di un failover di test.
