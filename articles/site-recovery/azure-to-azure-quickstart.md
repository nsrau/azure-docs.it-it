---
title: Configurare il ripristino di emergenza per le macchine virtuali di Azure in un'area secondaria con Azure Site Recovery
description: Configurare rapidamente il ripristino di emergenza per macchine virtuali di Azure in un'area di Azure diversa usando il servizio Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: quickstart
ms.date: 11/12/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 91674d6335ae95993bcdd59250658d562302b1dc
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954138"
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

1. Nel menu del portale di Azure selezionare **Macchine virtuali** oppure cercare e selezionare *Macchine virtuali* in qualsiasi pagina. Selezionare la macchina virtuale da replicare.
2. In **Operazioni** selezionare **Ripristino di emergenza**.
3. In **Configure disaster recovery** (Configura ripristino di emergenza)  >  **Area di destinazione** selezionare l'area di destinazione in cui si vuole eseguire la replica.
4. Per questa guida introduttiva, accettare le altre impostazioni predefinite.
5. Selezionare **Rivedi e avvia replica**. Selezionare quindi **Avvia replica** per avviare un processo per abilitare la replica della macchina virtuale.

    ![abilitare la replica](media/azure-to-azure-quickstart/enable-replication1.png)

## <a name="verify-settings"></a>Verificare le impostazioni

Al termine del processo di replica, è possibile controllare lo stato di replica, modificare le impostazioni di replica e testare la distribuzione.

1. Nel menu del portale di Azure selezionare **Macchine virtuali** oppure cercare e selezionare *Macchine virtuali* in qualsiasi pagina. Selezionare la macchina virtuale da verificare.
2. In **Operazioni** selezionare **Ripristino di emergenza**.

   È possibile verificare l'integrità della replica, i punti di ripristino creati, nonché le aree di origine e di destinazione sulla mappa.

   ![Stato della replica](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>Pulire le risorse

La replica della macchina virtuale nell'area primaria viene arrestata nel momento in cui si disabilita la replica corrispondente:

- Le impostazioni della replica di origine vengono automaticamente pulite. L'estensione Site Recovery installata nella macchina virtuale come parte della replica non viene rimossa e deve essere rimossa manualmente. 
- La fatturazione di Site Recovery per la macchina virtuale viene arrestata.

Per arrestare la replica, seguire questa procedura

1. Nel menu del portale di Azure selezionare **Macchine virtuali** oppure cercare e selezionare *Macchine virtuali* in qualsiasi pagina. Selezionare la macchina virtuale da modificare.
2. In **Ripristino di emergenza** selezionare **Disabilita replica**.

   ![Disabilitare la replica](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata replicata una singola macchina virtuale in un'area secondaria. Ora, provare a replicare più macchine virtuali di Azure usando un piano di ripristino.

> [!div class="nextstepaction"]
> [Configurare il ripristino di emergenza per macchine virtuali di Azure](azure-to-azure-tutorial-enable-replication.md)
