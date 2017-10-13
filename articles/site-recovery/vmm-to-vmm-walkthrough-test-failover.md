---
title: Eseguire un failover di test per la replica della macchina virtuale Hyper-V in un sito secondario con Azure Site Recovery | Microsoft Docs
description: Descrive come eseguire un failover di test per la replica della macchina virtuale Hyper-V in un sito System Center VMM secondario con Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a3fd11ce-65a1-4308-8435-45cf954353ef
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 23d235d326273e7ec59feee6588a39f685401e52
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="step-10-run-a-test-failover-for-hyper-v-replication-to-a-secondary-site"></a>Passaggio 10: Eseguire un failover di test per la replica Hyper-V in un sito secondario


Dopo aver abilitato la replica per le macchine virtuali Hyper-V con [Azure Site Recovery](site-recovery-overview.md), seguire quanto riportato in questo articolo per eseguire un failover di test. Un failover di test verifica che la replica funzioni, senza alcuna conseguenza sull'ambiente di produzione. 


È possibile inviare commenti nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Prima di iniziare

- Quando si attiva un failover di test è possibile specificare la rete a cui si connetteranno le macchine virtuali di replica di test. [Altre informazioni](site-recovery-test-failover-vmm-to-vmm.md#network-options-in-site-recovery) sulle opzioni di rete.
- È consigliabile non scegliere una rete selezionata durante il mapping di rete.
- Le istruzioni riportate in questo articolo descrivono come eseguire il failover di una singola macchina virtuale. Consultare l'articolo relativo alla [creazione di piani di ripristino](site-recovery-create-recovery-plans.md) se si desidera eseguire il failover di più macchine virtuali contemporaneamente.
- Consultare l'articolo relativo alle [limitazioni per i failover di test](site-recovery-test-failover-vmm-to-vmm.md#things-to-note).
- L'indirizzo IP assegnato a una macchina virtuale durante un failover di test corrisponde all'indirizzo IP che la macchina virtuale riceverebbe durante un failover pianificato o non pianificato, a condizione che l'indirizzo IP sia disponibile nella rete di failover di test. Se l'indirizzo IP non è disponibile nella rete di failover di test, la macchina virtuale riceve un altro indirizzo IP disponibile in tale rete.
- Se si desidera eseguire il failover di test nella rete di produzione per la convalida completa della macchina di connettività di rete end-to-end, si noti che:
    - La macchina virtuale primaria deve essere arrestata quando si sta eseguendo il failover di test. In caso contrario si avranno due macchine virtuali con la stessa identità in esecuzione contemporaneamente sulla stessa rete. 
    - Se si apportano modifiche alle macchine virtuali di test, tali modifiche andranno perdute durante la pulizia del failover di test. Le modifiche non vengono replicate nella macchina virtuale primaria.
    - Testare una rete di produzione comporta tempi di inattività per i carichi di lavoro della produzione. Chiedere agli utenti di non usare l'app quando l'analisi di ripristino di emergenza è in corso.  


## <a name="run-a-test-failover-for-a-vm"></a>Eseguire un failover di test per una macchina virtuale

1. Per eseguire il failover di una macchina virtuale, in **Elementi replicati** fare clic sulla macchina virtuale e quindi su **Failover di test**.
2. In **Failover di test** specificare come le macchine virtuali di test dovranno essere connesse alle reti dopo il failover di test. 
3. Fare clic su **OK** per iniziare il failover. Tenere traccia dell'avanzamento sulla scheda **Processi** .
5. Dopo il completamento del failover verificare che le macchine virtuali di test vengano avviate correttamente.
6. Al termine fare clic su **Cleanup test failover** (Pulizia failover di test) nel piano di ripristino.
7. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test. Questa operazione elimina le macchine virtuali e le reti create durante il failover di test.


## <a name="next-steps"></a>Passaggi successivi

Dopo aver testato la distribuzione, leggere altre informazioni sugli altri tipi di [failover](site-recovery-failover.md).
