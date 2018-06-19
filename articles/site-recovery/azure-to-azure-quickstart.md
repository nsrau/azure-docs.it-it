---
title: Replicare una macchina virtuale di Azure in un'altra area di Azure
description: Questa guida introduttiva illustra i passaggi necessari per replicare una macchina virtuale di Azure presente in un'area di Azure in un'area diversa.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: quickstart
ms.date: 05/31/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: d52691dbec932a3515c90e757863be01ade086cc
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715956"
---
# <a name="replicate-an-azure-vm-to-another-azure-region"></a>Replicare una macchina virtuale di Azure in un'altra area di Azure

Il servizio [Azure Site Recovery](site-recovery-overview.md) contribuisce a realizzare la strategia di continuità aziendale e ripristino di emergenza (BCDR) mantenendo operative le app aziendali durante le interruzioni pianificate e non pianificate. Site Recovery gestisce e coordina il ripristino di emergenza di computer locali e macchine virtuali di Azure, incluse le operazioni di replica, failover e failback.

Questa guida introduttiva illustra come replicare una macchina virtuale di Azure in un'area di Azure diversa. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.



## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="enable-replication-for-the-azure-vm"></a>Abilitare la replica per la macchina virtuale di Azure

1. Nel portale di Azure fare clic su **Macchine virtuali** e selezionare la macchina virtuale che si vuole replicare.

2. In **Impostazioni** fare clic su **Ripristino di emergenza**.
3. In **Configure disaster recovery** (Configura ripristino di emergenza)  >  **Area di destinazione** selezionare l'area di destinazione in cui si vuole eseguire la replica.
4. Per questa guida introduttiva, accettare le altre impostazioni predefinite.
5. Fare clic su **Abilitazione della replica**. Verrà avviato un processo per abilitare la replica per la macchina virtuale.

    ![abilitare la replica](media/azure-to-azure-quickstart/enable-replication1.png)



## <a name="verify-settings"></a>Verificare le impostazioni

Al termine del processo di replica, è possibile controllare lo stato di replica, modificare le impostazioni di replica e testare la distribuzione.

1. Nel menu della macchina virtuale fare clic su **Ripristino di emergenza**.
2. È possibile verificare l'integrità della replica, i punti di ripristino creati e le aree di origine e di destinazione sulla mappa.

   ![Stato della replica](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>Pulire le risorse

La replica della macchina virtuale nell'area primaria viene arrestata nel momento in cui si disabilita la replica corrispondente:

- Le impostazioni della replica di origine vengono automaticamente pulite.
- Viene arrestata anche la fatturazione di Site Recovery per la macchina virtuale.

Per arrestare la replica, seguire questa procedura:

1. Selezionare la macchina virtuale.
2. In **Ripristino di emergenza** fare clic su **Altro**.
3. Fare clic su **Disable Replication** (Disabilitazione della replica).

   ![Disabilitare la replica](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata replicata una singola macchina virtuale in un'area secondaria.

> [!div class="nextstepaction"]
> [Configurare il ripristino di emergenza per macchine virtuali di Azure](azure-to-azure-tutorial-enable-replication.md)
