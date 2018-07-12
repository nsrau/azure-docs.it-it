---
title: Preparare la destinazione (da fisico ad Azure) | Documentazione Microsoft
description: Questo articolo descrive come preparare l'ambiente di Azure per avviare la replica di server fisici che eseguono Windows o Linux in Azure.
services: site-recovery
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: bsiva
ms.openlocfilehash: 370d245e39b848acade18d0e73f60a3246737629
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2018
ms.locfileid: "37915521"
---
# <a name="prepare-target-vmware-to-azure"></a>Preparare la destinazione (da VMware ad Azure)

Questo articolo descrive come preparare l'ambiente di Azure per avviare la replica di server fisici (x64) che eseguono Windows o Linux in Azure.

## <a name="prerequisites"></a>prerequisiti

L'articolo presuppone quanto segue:
- È stato creato un insieme di credenziali dei servizi di ripristino per proteggere i server fisici. È possibile creare un insieme di credenziali di Servizi di ripristino nel [portale di Azure](http://portal.azure.com "portale di Azure").
- Per replicare i server fisici in Azure, è necessario aver [configurato l'ambiente locale](physical-azure-disaster-recovery.md).

## <a name="prepare-target"></a>Preparare la destinazione

Dopo aver completato il **Passaggio 1: Selezionare l'obiettivo di protezione** e il **Passaggio 2: Preparare l'origine**, si passa al **Passaggio 3: Destinazione**.

![Preparare la destinazione](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Sottoscrizione:** dal menu a discesa selezionare la sottoscrizione nella quale replicare i server fisici.
2. **Modello di distribuzione**: selezionare il modello di distribuzione (classica o di Resource Manager)

In base al modello di distribuzione scelto, viene eseguita una convalida per verificare di avere almeno un account di archiviazione compatibile e una rete virtuale nella sottoscrizione di destinazione in cui vengono eseguiti replica e failover dei server fisici.

Dopo aver completato la convalida, fare clic su OK per andare al passaggio successivo.

Se non si ha una rete virtuale o di un account di archiviazione di Resource Manager compatibile, è possibile crearne uno facendo clic sul pulsante **+ Account di archiviazione** o **+ Rete** nella parte superiore della pagina.

## <a name="next-steps"></a>Passaggi successivi
[Configurare le impostazioni di replica](vmware-azure-set-up-replication.md).
