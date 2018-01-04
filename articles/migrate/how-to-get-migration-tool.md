---
title: Eseguire la migrazione di computer dopo la valutazione con Azure Migrate | Microsoft Docs
description: Descrive come ottenere indicazioni per la migrazione di computer dopo aver eseguito una valutazione con il servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: e6e32e9bd2384987a1d0315bfbef913c46fc5dbb
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2017
---
# <a name="migrate-machines-after-assessment"></a>Eseguire la migrazione dei computer dopo la valutazione


[Azure Migrate](migrate-overview.md) valuta i computer locali per verificare se siano adatti per la migrazione in Azure e fornisce stime riguardo a dimensioni e costi per l'esecuzione del computer in Azure. Attualmente Azure Migrate si limita a valutare i computer per la migrazione. La migrazione stessa viene eseguita usando altri servizi di Azure.

Questo articolo descrive come ottenere suggerimenti su uno strumento di migrazione dopo aver eseguito una valutazione della migrazione.

## <a name="migration-tool-suggestion"></a>Suggerimento dello strumento di migrazione

Per ottenere suggerimenti relativi agli strumenti di migrazione, è necessario eseguire un'individuazione completa nell'ambiente locale. L'individuazione completa viene eseguita tramite l'installazione di agenti nei computer locali.  

1. Creare un progetto Azure Migrate, individuare i computer locali e creare una valutazione della migrazione. [Altre informazioni](tutorial-assessment-vmware.md).
2. Scaricare e installare gli agenti di Azure Migrate in ogni computer locale per cui si vuole visualizzare un metodo di migrazione consigliato. [Seguire questa procedura](how-to-create-group-machine-dependencies.md#prepare-machines-for-dependency-mapping) per installare gli agenti.
2. Identificare i computer locali adatti per la migrazione in modalità lift-and-shift. Si tratta delle macchine virtuali che non richiedono alcuna modifica alle app che le eseguono e di cui è possibile eseguire la migrazione così come sono.
3. Per la migrazione in modalità lift-and-shift, è consigliabile usare Azure Site Recovery. [Altre informazioni](../site-recovery/tutorial-migrate-on-premises-to-azure.md). In alternativa, è possibile usare strumenti di terze parti che supportano la migrazione in Azure.
4. Se sono presenti computer locali che non sono adatti per una migrazione in modalità lift-and-shift, ovvero se si vuole eseguire la migrazione di un'app specifica invece che di un'intera macchina virtuale, è possibile usare altri strumenti di migrazione. Ad esempio, è consigliabile usare il [servizio Migrazione del database di Azure](https://azure.microsoft.com/campaigns/database-migration/) se si vuole eseguire la migrazione di database locali come SQL Server, MySQL, o Oracle in Azure.


## <a name="review-suggested-migration-methods"></a>Esaminare i metodi di migrazione suggeriti

1. Prima di poter ottenere un metodo di migrazione suggerito, è necessario creare un progetto Azure Migrate, individuare i computer locali ed eseguire una valutazione della migrazione. [Altre informazioni](tutorial-assessment-vmware.md).
2. Dopo aver creato la valutazione, visualizzarla nel progetto > **Panoramica** > **Dashboard**. Fare clic su **Idoneità per Azure**

    ![Idoneità per Azure](./media/tutorial-assessment-vmware/assessment-report.png)  

3. In **Strumento suggerito** esaminare i suggerimenti per gli strumenti che è possibile usare per la migrazione.

    ![Strumento suggerito](./media/tutorial-assessment-vmware/assessment-suitability.png) 




## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
