---
title: Eseguire un'analisi di ripristino di emergenza per il sito secondario locale con Azure Site Recovery | Microsoft Docs
description: Informazioni sull'esecuzione di un'analisi di ripristino di emergenza sul sito secondario locale con Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 445878e2-6682-49ba-914d-4c6824ab08a6
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 8737c2a22bd729cfc15d5448e1ec0becef643fd5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="run-a-disaster-recovery-drill-for-hyper-v-vms-to-your-secondary-on-premises-site"></a>Configurare un'analisi di ripristino di emergenza per macchine virtuali Hyper-V nel sito locale secondario

Il servizio [Azure Site Recovery](site-recovery-overview.md) favorisce l'attuazione della strategia di ripristino di emergenza gestendo e coordinando le operazioni di replica, failover e failback di computer locali e macchine virtuali di Azure.

In questa esercitazione viene illustrato come configurare un'analisi di ripristino di emergenza per macchine virtuali Hyper-V nel sito locale secondario. Gli host Hyper-V devono essere gestiti in cloud privati System Center Virtual Machine Manager (VMM). Un'analisi consente di convalidare la strategia di replica senza tempi di inattività o perdite di dati e non ha alcun impatto sull'ambiente di produzione. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Preparare i prerequisiti per il failover di test
> * Eseguire un failover di test per un singolo computer


## <a name="prerequisites"></a>Prerequisiti

- È possibile eseguire un failover di test con un paio di opzioni di rete nel sito secondario. È possibile eseguire il failover senza una rete o con una rete esistente o consentire la creazione automatica di una rete di test con Site Recovery. 
**Per usare una rete di produzione esistente per il failover di test:**:
    - La macchina virtuale primaria deve essere arrestata quando si sta eseguendo il failover di test. In caso contrario si avranno due macchine virtuali con la stessa identità in esecuzione contemporaneamente sulla stessa rete. 
    - Se si apportano modifiche alle macchine virtuali di test, tali modifiche andranno perdute durante la pulizia del failover di test. Le modifiche non vengono replicate nella macchina virtuale primaria.
    - Testare una rete di produzione comporta tempi di inattività per i carichi di lavoro della produzione. Chiedere agli utenti di non usare le app correlate quando l'analisi di ripristino di emergenza è in corso. 
- La rete di test di replica non deve necessariamente corrispondere al tipo di rete logica VMM usato per il failover di test. Tuttavia, alcune combinazioni non funzionano. Ad esempio, se la replica usa DHCP e l'isolamento basato su VLAN, non è possibile usare la virtualizzazione rete Windows per la rete del failover di test, perché richiede un pool di indirizzi IP. 
- È consigliabile non usare la rete che è stata selezionata per il mapping di rete per il failover di test.


## <a name="run-a-test-failover-for-a-vm"></a>Eseguire un failover di test per una macchina virtuale

1. In **Elementi replicati** fare clic sulla macchina virtuale e quindi su **Failover di test**.
2. In **Failover di test** specificare come le macchine virtuali di test dovranno essere connesse alle reti dopo il failover di test. Si consiglia, ai fini di questa esercitazione, di consentire a Site Recovery di creare automaticamente una rete di test. [Altre informazioni](site-recovery-test-failover-vmm-to-vmm.md#network-options-in-site-recovery)
3. Fare clic su **OK** per iniziare il failover. Tenere traccia dell'avanzamento sulla scheda **Processi** .
4. Dopo il completamento del failover verificare che le macchine virtuali di test vengano avviate correttamente.
5. Al termine, fare clic su **Pulisci failover di test**. Questo comando consente di eliminare le macchine virtuali di test e le eventuali reti create durante il failover di test.
6. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test. 


## <a name="next-steps"></a>Passaggi successivi

[Eseguire un failover di produzione](tutorial-vmm-to-vmm-failover-failback.md)






