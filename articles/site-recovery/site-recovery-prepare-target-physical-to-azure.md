---
title: Preparare la destinazione (da fisico ad Azure) | Documentazione Microsoft
description: Questo articolo descrive come preparare l&quot;ambiente di Azure per avviare la replica di server fisici che eseguono Windows o Linux in Azure.
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
ms.date: 2/11/2017
ms.author: bsiva
translationtype: Human Translation
ms.sourcegitcommit: b7f26df96ddedb579cf5d9d20fee6b2599e762e0
ms.openlocfilehash: 9dd4e9007da6cf276f976e2f3f25305286830544

---

# <a name="prepare-target-vmware-to-azure"></a>Preparare la destinazione (da VMware ad Azure)
> [!div class="op_single_selector"]
> * [Macchine virtuali VMware](./site-recovery-prepare-target-vmware-to-azure.md)
> * [Server fisici](./site-recovery-prepare-target-physical-to-azure.md)

Questo articolo descrive come preparare l'ambiente di Azure per avviare la replica di server fisici (x64) che eseguono Windows o Linux in Azure.

## <a name="prerequisites"></a>Prerequisiti

In questo articolo si presuppone quanto segue:
- È stato creato un insieme di credenziali dei servizi di ripristino per proteggere i server fisici. È possibile creare un insieme di credenziali di Servizi di ripristino nel [portale di Azure](http://portal.azure.com "portale di Azure").
- Per replicare i server fisici in Azure, è necessario aver [configurato l'ambiente locale](./site-recovery-set-up-physical-to-azure.md).

## <a name="prepare-target"></a>Preparare la destinazione

Dopo aver completato il **Passaggio 1: Selezionare l'obiettivo di protezione** e il **Passaggio 2: Preparare l'origine**, si passa al **Passaggio 3: Destinazione**.

![Preparare la destinazione](./media/site-recovery-prepare-target-physical-to-azure/prepare-target-physical-to-azure.png)

1. **Sottoscrizione**: dal menu a discesa selezionare la sottoscrizione nella quale replicare i server fisici.
2. **Modello di distribuzione**: selezionare il modello di distribuzione (classica o di Resource Manager)

In base al modello di distribuzione scelto, viene eseguita una convalida per verificare di avere almeno un account di archiviazione compatibile e una rete virtuale nella sottoscrizione di destinazione in cui vengono eseguiti replica e failover dei server fisici.

Dopo aver completato la convalida, fare clic su OK per andare al passaggio successivo.

Se non si dispone di un account di archiviazione di Resource Manager o di una rete virtuale compatibile o se si vuole aggiungerne altri, fare clic sui pulsanti **+ Account di archiviazione** o **+ Rete** nella parte superiore del pannello.

## <a name="next-steps"></a>Passaggi successivi
[Configurare le impostazioni di replica](./site-recovery-setup-replication-settings-vmware.md).



<!--HONumber=Feb17_HO2-->


