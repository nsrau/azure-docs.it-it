---
title: Preparare l'ambiente di destinazione per la replica VMware in Azure | Microsoft Docs
description: Questo articolo descrive come preparare l'ambiente Azure di destinazione per la replica di macchine virtuali VMware in Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: cbf9c1a49481de49fb8d9c6599acec7bc3600df3
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52837512"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Preparare l'ambiente di destinazione per il ripristino di emergenza di macchine virtuali VMware o server fisici in Azure

Questo articolo descrive come preparare l'ambiente Azure di destinazione per avviare la replica di macchine virtuali o server fisici VMware in Azure.

## <a name="prerequisites"></a>Prerequisiti

L'articolo presuppone quanto segue:
- È stato creato un insieme di credenziali di Servizi di ripristino sul [portale di Azure](http://portal.azure.com "portale di Azure") per proteggere le macchine di origine
- Per replicare le [macchine virtuali VMware](vmware-azure-set-up-source.md) di origine o i [server fisici](physical-azure-set-up-source.md) in Azure, è necessario avere configurato l'ambiente locale.

## <a name="prepare-target"></a>Preparare la destinazione

Dopo aver completato il **Passaggio 1: Obiettivo di protezione** e il **Passaggio 2: Prepara origine**, si passa al **Passaggio 3: Destinazione**

![Preparare la destinazione](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Sottoscrizione:** nel menu a discesa selezionare la sottoscrizione nella quale replicare le macchine virtuali o i server fisici.
2. **Modello di distribuzione**: selezionare il modello di distribuzione (classica o Resource Manager)

In base al modello di distribuzione scelto, viene eseguita una convalida per verificare di avere almeno un account di archiviazione e una rete virtuale compatibili nella sottoscrizione di destinazione in cui vengono eseguiti replica e failover della macchina virtuale o del server fisico.

Dopo aver completato la convalida, fare clic su OK per andare al passaggio successivo.

Se non si ha una rete virtuale o di un account di archiviazione di Resource Manager compatibile, è possibile crearne uno facendo clic sul pulsante **+ Account di archiviazione** o **+ Rete** nella parte superiore della pagina.

## <a name="next-steps"></a>Passaggi successivi
[Configurare le impostazioni di replica](vmware-azure-set-up-replication.md).
