---
title: Preparare l'ambiente di destinazione per la replica VMware in Azure | Microsoft Docs
description: Questo articolo descrive come preparare l'ambiente Azure di destinazione per la replica di macchine virtuali VMware in Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: e75d4b1701944e206fcf6ded2dcb6d1e1fbc77cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60723804"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Preparare l'ambiente di destinazione per il ripristino di emergenza di macchine virtuali VMware o server fisici in Azure

Questo articolo descrive come preparare l'ambiente Azure di destinazione per avviare la replica di macchine virtuali o server fisici VMware in Azure.

## <a name="prerequisites"></a>Prerequisiti

L'articolo presuppone quanto segue:
- È stato creato un insieme di credenziali di Servizi di ripristino sul [portale di Azure](https://portal.azure.com "portale di Azure") per proteggere le macchine di origine
- Per replicare le [macchine virtuali VMware](vmware-azure-set-up-source.md) di origine o i [server fisici](physical-azure-set-up-source.md) in Azure, è necessario avere configurato l'ambiente locale.

## <a name="prepare-target"></a>Preparare la destinazione

Dopo aver completato il **Passaggio 1: Obiettivo di protezione** e il **Passaggio 2: Prepara origine**, si passa al **Passaggio 3: Destinazione**

![Preparare la destinazione](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Sottoscrizione:** nel menu a discesa selezionare la sottoscrizione nella quale replicare le macchine virtuali o i server fisici.
2. **Modello di distribuzione**: selezionare il modello di distribuzione (classica o Resource Manager)

Basate sul modello di distribuzione scelto, viene eseguita una convalida per assicurarsi di avere almeno una rete virtuale nella sottoscrizione di destinazione per eseguire la replica e failover la macchina virtuale o server fisico in.

Dopo aver completato la convalida, fare clic su OK per andare al passaggio successivo.

Se non si dispone di una rete virtuale, è possibile crearne uno facendo il **+ rete** nella parte superiore della pagina.

## <a name="next-steps"></a>Passaggi successivi
[Configurare le impostazioni di replica](vmware-azure-set-up-replication.md).
