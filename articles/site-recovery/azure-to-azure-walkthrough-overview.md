---
title: Eseguire la replica di macchine virtuali di Azure tra aree di Azure | Microsoft Docs
description: Offre un riepilogo sulla procedura necessaria per eseguire la replica delle macchine virtuali di Azure tra aree di Azure mediante il servizio Azure Site Recovery nel portale di Azure
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: 6dd36239-4363-4538-bf80-a18e71b8ec67
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.openlocfilehash: 9258613161a61e36b1d0c5796d5763c916d66859
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="replicate-azure-vms-between-regions-with-azure-site-recovery"></a>Eseguire la replica delle VM di Azure tra le aree con Azure Site Recovery

>Questo articolo offre una panoramica della procedura necessaria per eseguire la replica di macchine virtuali di Azure in un'area di Azure a macchine virtuali in un'area diversa. 

>[!NOTE]
>
> La replica delle macchine virtuali di Azure è attualmente in anteprima.

Inserire commenti e domande nella parte inferiore di questo articolo oppure nel [forum su Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="step-1-review-architecture"></a>Passaggio 1: Esaminare l'architettura

Prima di iniziare la distribuzione, esaminare l'architettura dello scenario e i componenti che è necessario distribuire.

Andare a [Passaggio 1: Esaminare l'architettura](azure-to-azure-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>Passaggio 2: Esaminare i prerequisiti

Verificare di aver soddisfatto i prerequisiti di Azure, tra cui una sottoscrizione, reti virtuali, account di archiviazione e i requisiti delle macchine virtuali.

Andare a [Passaggio 2: Esaminare i prerequisiti e le limitazioni](azure-to-azure-walkthrough-prerequisites.md)


## <a name="step-3-plan-networking"></a>Passaggio 3: Pianificare la rete

Verificare che la connettività in uscita sia impostata nelle macchine virtuali di Azure da replicare e che le connessioni locali siano impostate.

Andare a [Passaggio 4: Pianificare la rete](azure-to-azure-walkthrough-network.md)



## <a name="step-4-create-a-vault"></a>Passaggio 4: Creare un insieme di credenziali 

È necessario configurare un insieme di credenziali di Servizi di ripristino per orchestrare e gestire la replica e anche specificare l'area di origine.

Andare a [Passaggio 4: Creare un insieme di credenziali](azure-to-azure-walkthrough-vault.md)


## <a name="step-5-enable-replication"></a>Passaggio 5: Abilitare la replica


Per abilitare la replica, configurare le impostazioni del percorso di destinazione, impostare i criteri di replica e selezionare le macchine virtuali di Azure da replicare. Dopo l'abilitazione, viene eseguita la replica iniziale delle VM.

Andare a [Passaggio 5: Abilitare la replica](azure-to-azure-walkthrough-enable-replication.md)


## <a name="step-6-run-a-test-failover"></a>Passaggio 6: Eseguire un failover di test

Dopo il completamento della replica iniziale e con la replica differenziale in esecuzione, è possibile eseguire un failover di test per verificare che tutto funzioni come previsto.

Andare a [Passaggio 6: Eseguire un failover di test](azure-to-azure-walkthrough-test-failover.md)


