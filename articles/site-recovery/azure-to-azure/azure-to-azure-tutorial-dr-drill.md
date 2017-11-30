---
title: Eseguire un'analisi del ripristino di emergenza per macchine virtuali di Azure in un'area di Azure secondaria con Azure Site Recovery (anteprima)
description: Informazioni su come eseguire un'analisi del ripristino di emergenza per macchine virtuali di Azure in un'area di Azure secondaria usando il servizio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: d763865bb0b86b1bdf15258a3caf36561dd3385c
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2017
---
# <a name="run-a-disaster-recovery-drill-for-azure-vms-to-a-secondary-azure-region-preview"></a>Eseguire un'analisi del ripristino di emergenza per macchine virtuali di Azure in un'area di Azure secondaria (anteprima)

Il servizio [Azure Site Recovery](../site-recovery-overview.md) contribuisce a realizzare la strategia di continuità aziendale e ripristino di emergenza (BCDR) mantenendo disponibili e operative le app aziendali durante interruzioni pianificate e non pianificate. Site Recovery gestisce e coordina il ripristino di emergenza di computer locali e macchine virtuali di Azure, incluse le operazioni di replica, failover e failback.

Questa esercitazione illustra come eseguire un'analisi del ripristino di emergenza per una macchina virtuale di Azure, da un'area di Azure a un'altra, con un failover di test. Un'analisi consente di convalidare la strategia di replica senza tempi di inattività o perdite di dati e non ha alcun impatto sull'ambiente di produzione. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Verificare i prerequisiti
> * Eseguire un failover di test per una singola macchina virtuale

## <a name="prerequisites"></a>Prerequisiti

- Prima di eseguire un failover di test, è consigliabile verificare le proprietà della macchina virtuale per verificare che corrispondano a quelle previste.  È possibile accedere alle proprietà delle macchine virtuali in **Elementi replicati**. Il pannello **Informazioni di base** visualizza informazioni sulle impostazioni e sullo stato dei computer.
- È consigliabile usare una rete di una macchina virtuale di Azure separata per il failover di test e non la rete predefinita che è stata configurata al momento di abilitare la replica.


## <a name="run-a-test-failover"></a>Eseguire un failover di test

1. In **Impostazioni** > **Elementi replicati** fare clic sull'icona della VM **+Failover di test**.

2. In **Failover di test** selezionare un punto di ripristino in cui eseguire il failover:

   - **Ultima elaborazione**: viene eseguito il failover della macchina virtuale nel punto di ripristino più recente che è stato elaborato dal servizio Site Recovery. Viene visualizzato il timestamp. Con questa opzione, non viene impiegato alcun tempo di elaborazione dati e viene quindi fornito un valore RTO (Recovery Time Objective) basso.
   - **Coerente con l'app più recente**: questa opzione esegue il failover su tutte le macchine virtuali al punto di ripristino coerente con l'app. Viene visualizzato il timestamp.
   - **Personalizzazione**: selezionare qualsiasi punto di ripristino.

3. Selezionare la rete virtuale di Azure di destinazione a cui si connetteranno le VM di Azure nell'area secondaria dopo il failover.

4. Per avviare il failover, fare clic su **OK**. Per verificare lo stato dell'operazione, fare clic sulla VM per visualizzare le rispettive proprietà. In alternativa, è possibile fare clic sul processo **Failover di test** nel nome dell'insieme di credenziali, quindi su **Impostazioni** > **Processi** > **Site Recovery jobs** (Processi di Site Recovery).
5. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure in **Macchine virtuali**. Verificare che la macchina virtuale sia in esecuzione, che sia stata dimensionata in modo adeguato e che sia connessa alla rete appropriata.
6. Per eliminare le VM create durante il failover di test, fare clic su **Cleanup test failover** (Pulizia failover di test) nell'elemento replicato o nel piano di ripristino. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire un failover di produzione](azure-to-azure-tutorial-failover-failback.md)
