---
title: Preparare la destinazione (da VMware ad Azure) | Documentazione Microsoft
description: Questo articolo descrive come preparare l&quot;ambiente Azure per avviare la replica di macchine virtuali VMware in Azure.
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhemraj
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 5/31/2017
ms.author: bsiva
ms.translationtype: Human Translation
ms.sourcegitcommit: b7f26df96ddedb579cf5d9d20fee6b2599e762e0
ms.openlocfilehash: 555ea8eed17e42019513029f7b3ac2167f002578
ms.contentlocale: it-it
ms.lasthandoff: 02/13/2017

---

# <a name="prepare-target-vmware-to-azure"></a>Preparare la destinazione (da VMware ad Azure)
> [!div class="op_single_selector"]
> * [Macchine virtuali VMware](./site-recovery-prepare-target-vmware-to-azure.md)
> * [Server fisici](./site-recovery-prepare-target-physical-to-azure.md)

Questo articolo descrive come preparare l'ambiente Azure per avviare la replica di macchine virtuali VMware in Azure.

## <a name="prerequisites"></a>Prerequisiti

In questo articolo si presuppone quanto segue:
- È stato creato un insieme di credenziali di Servizi di ripristino per proteggere le macchine virtuali VMware. È possibile creare un insieme di credenziali di Servizi di ripristino nel [portale di Azure](http://portal.azure.com "portale di Azure").
- Per replicare le macchine virtuali VMware in Azure, è necessario avere [configurato l'ambiente locale](./site-recovery-set-up-vmware-to-azure.md).

## <a name="prepare-target"></a>Preparare la destinazione

Dopo aver completato il **Passaggio 1: Selezionare l'obiettivo di protezione** e il **Passaggio 2: Preparare l'origine**, si passa al **Passaggio 3: Destinazione**.

![Preparare la destinazione](./media/site-recovery-prepare-target-vmware-to-azure/prepare-target-vmware-to-azure.png)

1. **Sottoscrizione**: nel menu a discesa selezionare la sottoscrizione nella quale replicare le macchine virtuali.
2. **Modello di distribuzione**: selezionare il modello di distribuzione (classica o di Resource Manager)

In base al modello di distribuzione scelto, viene eseguita una convalida per verificare di avere almeno un account di archiviazione e una rete virtuale compatibili nella sottoscrizione di destinazione in cui vengono eseguiti replica e failover della macchina virtuale.

Dopo aver completato la convalida, fare clic su OK per andare al passaggio successivo.

Se non si dispone di un account di archiviazione di Resource Manager o di una rete virtuale compatibile o se si vuole aggiungerne altri, fare clic sui pulsanti **+ Account di archiviazione** o **+ Rete** nella parte superiore del pannello.

## <a name="next-steps"></a>Passaggi successivi
[Configurare le impostazioni di replica](./site-recovery-setup-replication-settings-vmware.md).

