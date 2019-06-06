---
title: Configurare il ripristino di emergenza per una macchina virtuale IaaS di Azure in un'area di Azure secondaria
description: Questa guida introduttiva fornisce i passaggi necessari per il ripristino di emergenza di una macchina virtuale IaaS di Azure tra le aree di Azure con il servizio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: quickstart
ms.date: 05/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 46376091649a2c2b1498fd13e8775fb161de32c1
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399536"
---
# <a name="set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Configurare il ripristino di emergenza in un'area di Azure secondaria per una macchina virtuale di Azure        

Il servizio [Azure Site Recovery](site-recovery-overview.md) contribuisce a realizzare la strategia di continuità aziendale e ripristino di emergenza (BCDR) mantenendo operative le app aziendali durante le interruzioni pianificate e non pianificate. Site Recovery gestisce e coordina il ripristino di emergenza di computer locali e macchine virtuali di Azure, incluse le operazioni di replica, failover e failback.

Questo argomento di avvio rapido descrive come configurare il ripristino di emergenza per una macchina virtuale di Azure replicandola in un'area di Azure diversa.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!NOTE]
> Questo articolo è una rapida procedura dettagliata per i nuovi utenti. Usa il percorso più semplice, con opzioni predefinite e personalizzazione minima.  Per una procedura dettagliata più completa, vedere questa [esercitazione](azure-to-azure-tutorial-enable-replication.md).

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="enable-replication-for-the-azure-vm"></a>Abilitare la replica per la macchina virtuale di Azure

1. Nel portale di Azure fare clic su **Macchine virtuali** e selezionare la macchina virtuale che si vuole replicare.
2. In **Operazioni** fare clic su **Ripristino di emergenza**.
3. In **Configure disaster recovery** (Configura ripristino di emergenza)  >  **Area di destinazione** selezionare l'area di destinazione in cui si vuole eseguire la replica.
4. Per questa guida introduttiva, accettare le altre impostazioni predefinite.
5. Fare clic su **Abilitazione della replica**. Verrà avviato un processo per abilitare la replica per la macchina virtuale.

    ![abilitare la replica](media/azure-to-azure-quickstart/enable-replication1.png)

## <a name="verify-settings"></a>Verificare le impostazioni

Al termine del processo di replica, è possibile controllare lo stato di replica, modificare le impostazioni di replica e testare la distribuzione.

1. In **Operazioni** fare clic su **Ripristino di emergenza**.
2. È possibile verificare l'integrità della replica, i punti di ripristino creati, nonché le aree di origine e di destinazione sulla mappa.

   ![Stato della replica](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>Pulire le risorse

La replica della macchina virtuale nell'area primaria viene arrestata nel momento in cui si disabilita la replica corrispondente:

- Le impostazioni della replica di origine vengono automaticamente pulite. L'estensione Site Recovery installata nella macchina virtuale come parte della replica non viene rimossa e deve essere rimossa manualmente. 
- La fatturazione di Site Recovery per la macchina virtuale viene arrestata.

Per arrestare la replica, seguire questa procedura

1. Selezionare la macchina virtuale.
2. In **Ripristino di emergenza** fare clic su **Disabilita replica**.

   ![Disabilitare la replica](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata replicata una singola macchina virtuale in un'area secondaria. Ora, provare a replicare più macchine virtuali di Azure usando un piano di ripristino.

> [!div class="nextstepaction"]
> [Configurare il ripristino di emergenza per macchine virtuali di Azure](azure-to-azure-tutorial-enable-replication.md)
