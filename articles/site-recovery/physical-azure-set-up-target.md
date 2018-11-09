---
title: Configurare l'ambiente di destinazione per il ripristino di emergenza di server fisici locali in Azure | Microsoft Docs
description: Questo articolo descrive come configurare l'ambiente di destinazione di Azure per il ripristino di emergenza di server fisici con Azure Site Recovery.
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: bsiva
ms.openlocfilehash: b89d04a6e2fd11a61de8b56690664f6204c208ad
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209293"
---
# <a name="prepare-target-vmware-to-azure"></a>Preparare la destinazione (da VMware ad Azure)

Questo articolo descrive come preparare l'ambiente di Azure per avviare la replica di server fisici (x64) che eseguono Windows o Linux in Azure.

## <a name="prerequisites"></a>Prerequisiti

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
