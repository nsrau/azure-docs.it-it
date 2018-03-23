---
title: Preparare l'ambiente di destinazione per la replica VMware in Azure | Microsoft Docs
description: Questo articolo descrive come preparare l'ambiente Azure di destinazione per la replica di macchine virtuali VMware in Azure.
services: site-recovery
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: bsiva
ms.openlocfilehash: 5f14bbed7ae59737f62fb736591775cb7ba495c5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="prepare-the-target-environment-for-vmware-replication-to-azure"></a>Preparare l'ambiente di destinazione per la replica VMware in Azure

Questo articolo descrive come preparare l'ambiente Azure di destinazione per avviare la replica di macchine virtuali VMware in Azure.

## <a name="prerequisites"></a>prerequisiti

L'articolo presuppone quanto segue:
- È stato creato un insieme di credenziali di Servizi di ripristino per proteggere le macchine virtuali VMware. È possibile creare un insieme di credenziali di Servizi di ripristino nel [portale di Azure](http://portal.azure.com "portale di Azure").
- Per replicare le macchine virtuali VMware in Azure, è necessario avere [configurato l'ambiente locale](vmware-azure-set-up-source.md).

## <a name="prepare-target"></a>Preparare la destinazione

Dopo aver completato il **Passaggio 1: Selezionare l'obiettivo di protezione** e il **Passaggio 2: Preparare l'origine**, si passa al **Passaggio 3: Destinazione**.

![Preparare la destinazione](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Sottoscrizione**: nel menu a discesa selezionare la sottoscrizione nella quale replicare le macchine virtuali.
2. **Modello di distribuzione**: selezionare il modello di distribuzione (classica o di Resource Manager)

In base al modello di distribuzione scelto, viene eseguita una convalida per verificare di avere almeno un account di archiviazione e una rete virtuale compatibili nella sottoscrizione di destinazione in cui vengono eseguiti replica e failover della macchina virtuale.

Dopo aver completato la convalida, fare clic su OK per andare al passaggio successivo.

Se non si dispone di una rete virtuale o di un account di archiviazione di Resource Manager compatibile, è possibile crearne uno facendo clic sul pulsante **+ Account di archiviazione** o **+ Rete** nella parte superiore della pagina.

## <a name="next-steps"></a>Passaggi successivi
[Configurare le impostazioni di replica](vmware-azure-set-up-replication.md).
