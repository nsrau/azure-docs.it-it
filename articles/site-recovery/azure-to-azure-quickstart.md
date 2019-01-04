---
title: Configurare il ripristino di emergenza per una macchina virtuale IaaS di Azure in un'area di Azure secondaria
description: Questa guida introduttiva fornisce i passaggi necessari per il ripristino di emergenza di una macchina virtuale IaaS di Azure tra le aree di Azure con il servizio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: quickstart
ms.date: 12/27/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 881ec1a12f42c56f3ef3b4c3d858102ac36d2eb0
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793666"
---
# <a name="set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Configurare il ripristino di emergenza in un'area di Azure secondaria per una macchina virtuale di Azure 

Il servizio [Azure Site Recovery](site-recovery-overview.md) contribuisce a realizzare la strategia di continuità aziendale e ripristino di emergenza (BCDR) mantenendo operative le app aziendali durante le interruzioni pianificate e non pianificate. Site Recovery gestisce e coordina il ripristino di emergenza di computer locali e macchine virtuali di Azure, incluse le operazioni di replica, failover e failback.

Questa guida introduttiva illustra come replicare una macchina virtuale di Azure in un'area di Azure diversa.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!NOTE]
> Scopo di questo articolo è illustrare a un nuovo utente l'esperienza Azure Site Recovery con le opzioni predefinite e una minima personalizzazione. Per maggiori informazioni sulle diverse impostazioni personalizzabili, vedere [l'esercitazione relativa all'abilitazione della replica per le macchine virtuali di Azure](azure-to-azure-tutorial-enable-replication.md).

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="enable-replication-for-the-azure-vm"></a>Abilitare la replica per la macchina virtuale di Azure

1. Nel portale di Azure fare clic su **Macchine virtuali** e selezionare la macchina virtuale che si vuole replicare.
2. In **Operazioni** fare clic su **Ripristino di emergenza**.
3. In **Configure disaster recovery** (Configura ripristino di emergenza)  >  **Area di destinazione** selezionare l'area di destinazione in cui si vuole eseguire la replica.
4. Per questa guida introduttiva, accettare le altre impostazioni predefinite.
5. Fare clic su **Abilitazione della replica**. Verrà avviato un processo per abilitare la replica per la macchina virtuale.

    ![abilitare la replica](media/azure-to-azure-quickstart/enable-replication1.png)

## <a name="verify-settings"></a>Verificare le impostazioni

Al termine del processo di replica, è possibile controllare lo stato di replica, modificare le impostazioni di replica e testare la distribuzione.

1. Nel menu della macchina virtuale fare clic su **Ripristino di emergenza**.
2. È possibile verificare l'integrità della replica, i punti di ripristino creati, nonché le aree di origine e di destinazione sulla mappa.

   ![Stato della replica](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>Pulire le risorse

La replica della macchina virtuale nell'area primaria viene arrestata nel momento in cui si disabilita la replica corrispondente:

- Le impostazioni della replica di origine vengono automaticamente pulite. Si noti che l'estensione Site Recovery installata come parte della replica non viene rimossa e deve essere rimossa manualmente. 
- Viene arrestata anche la fatturazione di Site Recovery per la macchina virtuale.

Per arrestare la replica, seguire questa procedura

1. Selezionare la macchina virtuale.
2. In **Ripristino di emergenza** fare clic su **Disabilita replica**.

   ![Disabilitare la replica](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata replicata una singola macchina virtuale in un'area secondaria. È ora possibile esplorare altre opzioni e provare a replicare un set di macchine virtuali di Azure usando un piano di ripristino.

> [!div class="nextstepaction"]
> [Configurare il ripristino di emergenza per macchine virtuali di Azure](azure-to-azure-tutorial-enable-replication.md)
