---
title: Preparare la destinazione di replica della macchina virtuale VMware in Azure Site Recovery
description: Questo articolo descrive come preparare l'ambiente Azure di destinazione per la replica di macchine virtuali VMware in Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: c721234f2e1d806d51d31f3466e441bf8360f6b8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693174"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Preparare l'ambiente di destinazione per il ripristino di emergenza di macchine virtuali VMware o server fisici in Azure

Questo articolo descrive come preparare l'ambiente Azure di destinazione per avviare la replica di macchine virtuali o server fisici VMware in Azure.

## <a name="prerequisites"></a>Prerequisiti

L'articolo presuppone quanto segue:
- È stato creato un insieme di credenziali di servizi di ripristino su [portale di Azure](https://portal.azure.com "Portale di Azure") per proteggere le macchine di origine
- Per replicare le [macchine virtuali VMware](vmware-azure-set-up-source.md) di origine o i [server fisici](physical-azure-set-up-source.md) in Azure, è necessario avere configurato l'ambiente locale.

## <a name="prepare-target"></a>Preparare la destinazione

Dopo aver completato il **Passaggio 1: Selezionare l'obiettivo di protezione** e il **Passaggio 2: Preparare l'origine**, si passa al **Passaggio 3: Destinazione**

![Preparare la destinazione](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Sottoscrizione**: nel menu a discesa selezionare la sottoscrizione nella quale replicare le macchine virtuali o i server fisici.
2. **Modello di distribuzione**: selezionare il modello di distribuzione (classica o di Resource Manager)

In base al modello di distribuzione scelto, viene eseguita una convalida per assicurarsi di disporre di almeno una rete virtuale nella sottoscrizione di destinazione per la replica e il failover del server fisico o della macchina virtuale in.

Dopo aver completato la convalida, fare clic su OK per andare al passaggio successivo.

Se non si dispone di una rete virtuale, è possibile crearne una facendo clic sul pulsante **+ rete** nella parte superiore della pagina.

## <a name="next-steps"></a>Passaggi successivi
[Configurare le impostazioni di replica](vmware-azure-set-up-replication.md).
