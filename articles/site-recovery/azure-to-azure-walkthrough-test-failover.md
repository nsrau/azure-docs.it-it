---
title: Eseguire un failover di test per la replica nella macchina virtuale di Azure con Azure Site Recovery | Microsoft Docs
description: Vengono riepilogati i passaggi necessari per eseguire un failover di test per le VM di Azure di cui viene eseguita la replica in Azure tramite il servizio Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: e15c1b0c-5d75-4fdf-acb0-e61def9e9339
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.openlocfilehash: 8babb0d016729f318442af93596d206c38d91206
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="step-6-run-a-test-failover-for-azure-vm-replication"></a>Passaggio 6: Eseguire un failover di test per la replica della macchina virtuale di Azure

Dopo avere abilitato la replica per la macchina virtuale di Azure (VM), seguire la procedura descritta in questo articolo per eseguire il failover di test da un'area di Azure a un'altra tramite il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.

- Terminata la lettura dell'articolo, è consigliabile verificare con un failover di test che almeno una macchina virtuale di Azure può eseguire il failover per l'area secondaria di Azure. 
- È possibile inserire commenti alla fine di questo articolo oppure porre domande nel [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

>[!NOTE]
>
> La replica della macchina virtuale di Azure è attualmente in anteprima.


## <a name="before-you-start"></a>Prima di iniziare

- Prima di eseguire un failover di test, è consigliabile verificare le proprietà delle VM e apportare eventuali modifiche necessarie. È possibile accedere alle proprietà delle VM in **Elementi replicati**. Il pannello **Informazioni di base** visualizza informazioni sulle impostazioni e sullo stato dei computer.
- È consigliabile usare una rete VM di Azure separata per il failover di test e non la rete (predefinita o personalizzato) che è stata configurata per il failover di produzione.
- Il failover di test viene eseguito per le macchine virtuali di Azure (e il relativo spazio di archiviazione) nell'area secondaria di Azure. Non vengono replicate eventuali app o risorse dipendenti. Se le app in esecuzione in macchine virtuali su cui è stato eseguito il failover sono dipendenti da altre risorse, ad esempio Active Directory o DNS, è necessario replicare anche queste, se non sono già disponibili nell'area secondaria. [Altre informazioni](site-recovery-test-failover-to-azure.md#prepare-active-directory-and-dns)
- Se si desidera accedere alle macchine virtuali replicate dopo il failover da un sito locale, è necessario [preparare la connessione](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) a queste macchine virtuali.

## <a name="run-a-test-failover"></a>Eseguire un failover di test

1. In **Impostazioni** > **Elementi replicati** fare clic sull'icona della VM **+Failover di test**. 

2. In **Failover di test** selezionare un punto di ripristino in cui eseguire il failover:

    - **Ultima elaborazione**: viene eseguito il failover della macchina virtuale nel punto di ripristino più recente che è stato elaborato dal servizio Site Recovery. Viene visualizzato il timestamp. Con questa opzione, non viene impiegato alcun tempo di elaborazione dati, pertanto viene fornito un RTO (Recovery Time Objective) basso.
    - **Coerente con l'app più recente**: questa opzione esegue il failover su tutte le macchine virtuali al punto di ripristino coerente con l'app. Viene visualizzato il timestamp. 
    - **Personalizzazione**: selezionare qualsiasi punto di ripristino.
 
3. Selezionare la rete virtuale di Azure di destinazione a cui si connetteranno le VM di Azure nell'area secondaria dopo il failover.
4. Per avviare il failover, fare clic su **OK**. Per verificare lo stato dell'operazione, fare clic sulla VM per visualizzare le rispettive proprietà. In alternativa, è possibile fare clic sul processo **Failover di test** nel nome dell'insieme di credenziali, quindi su **Impostazioni** > **Processi** > **Site Recovery jobs** (Processi di Site Recovery).
5. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure in **Macchine virtuali**. Verificare che la macchina virtuale sia delle dimensioni appropriate, che sia connessa alla rete giusta e che sia in esecuzione.
6. Per eliminare le VM create durante il failover di test, fare clic su **Cleanup test failover** (Pulizia failover di test) nell'elemento replicato o nel piano di ripristino. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test. 

[Altre informazioni](site-recovery-test-failover-to-azure.md) sui failover di test.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver testato il failover, questa procedura dettagliata viene completata. A questo punto, informazioni sull'esecuzione di failover nell'ambiente di produzione:

- [Altre informazioni](site-recovery-failover.md) sui diversi tipi di failover e su come eseguirli.
- Altre informazioni sul failover su più macchine virtuali [tramite un piano di ripristino](site-recovery-create-recovery-plans.md).
- Altre informazioni sull'[uso dei piani di ripristino](site-recovery-create-recovery-plans.md).
- Altre informazioni sulla [riprotezione delle VM di Azure](site-recovery-how-to-reprotect.md) dopo il failover.

