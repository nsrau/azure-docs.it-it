---
title: Configurare il ripristino di emergenza per le macchine virtuali di Azure in un'area secondaria con Azure Site Recovery
description: Configurare rapidamente il ripristino di emergenza per macchine virtuali di Azure in un'area di Azure diversa usando il servizio Azure Site Recovery.
ms.topic: quickstart
ms.date: 03/27/2020
ms.custom: mvc
ms.openlocfilehash: e26c2a1f24a88dc979f4ec68de65afc618740c00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371907"
---
# <a name="quickstart-set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Guida introduttiva: Configurare il ripristino di emergenza in un'area di Azure secondaria per una macchina virtuale di Azure

Il servizio [Azure Site Recovery](site-recovery-overview.md) contribuisce a realizzare la strategia di continuità aziendale e ripristino di emergenza (BCDR) mantenendo online le applicazioni aziendali durante le interruzioni pianificate e non pianificate. Site Recovery gestisce e coordina il ripristino di emergenza di computer locali e macchine virtuali di Azure, incluse le operazioni di replica, failover e failback.

Questo argomento di avvio rapido descrive come configurare il ripristino di emergenza per una macchina virtuale di Azure replicandola in un'area di Azure secondaria. In generale, per abilitare la replica vengono usate le impostazioni predefinite.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessaria una sottoscrizione di Azure e una macchina virtuale.

- Se non si ha un account Azure con una sottoscrizione attiva, è possibile [creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- È consigliabile usare una macchina virtuale con almeno 1 GB di RAM. [Altre informazioni](/azure/virtual-machines/windows/quick-create-portal) su come creare una macchina virtuale.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="enable-replication-for-the-azure-vm"></a>Abilitare la replica per la macchina virtuale di Azure

La procedura seguente consente di abilitare la replica di una macchina virtuale in una posizione secondaria.

1. Nel portale di Azure selezionare una macchina virtuale da replicare nel menu **Home** > **Macchine virtuali**.
1. In **Operazioni** selezionare **Ripristino di emergenza**.
1. In **Informazioni di base** > **Area di destinazione** selezionare l'area di destinazione.
1. Per visualizzare le impostazioni di replica, selezionare **Rivedi e avvia replica**. Se è necessario modificare le impostazioni predefinite, selezionare **Impostazioni avanzate**.
1. Per avviare il processo che abilita la replica della macchina virtuale, selezionare **Avvia replica**.

   :::image type="content" source="media/azure-to-azure-quickstart/enable-replication1.png" alt-text="Abilitare la replica":::.

## <a name="verify-settings"></a>Verificare le impostazioni

Al termine del processo di replica, è possibile controllare lo stato di replica, modificare le impostazioni di replica e testare la distribuzione.

1. Nel menu del portale di Azure selezionare **Macchine virtuali** e quindi la macchina virtuale replicata.
1. In **Operazioni** selezionare **Ripristino di emergenza**.
1. Per visualizzare i dettagli della replica selezionare **Informazioni di base** in **Panoramica**. Per altre informazioni, vedere la mappa **Integrità e stato**, **Conformità failover** e **Visualizzazione dell'infrastruttura**.

   :::image type="content" source="media/azure-to-azure-quickstart/replication-status.png" alt-text="Stato della replica.":::

## <a name="clean-up-resources"></a>Pulire le risorse

Per arrestare la replica della macchina virtuale nell'area primaria, è necessario disabilitare la replica:

- Le impostazioni della replica di origine vengono automaticamente pulite.
- L'estensione Site Recovery installata nella macchina virtuale durante la replica non viene rimossa.
- La fatturazione di Site Recovery per la macchina virtuale viene arrestata.

Per disabilitare la replica, attenersi alla procedura seguente:

1. Nel menu del portale di Azure selezionare **Macchine virtuali** e quindi la macchina virtuale replicata.
1. In **Operazioni** selezionare **Ripristino di emergenza**.
1. In **Panoramica** selezionare **Disabilita replica**.
1. Per disinstallare l'estensione Site Recovery, passare a **Impostazioni** > **Estensioni** della macchina virtuale.

   :::image type="content" source="media/azure-to-azure-quickstart/disable2-replication.png" alt-text="Disabilitare la replica.":::

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata replicata una singola macchina virtuale in un'area secondaria. Successivamente, configurare la replica per più macchine virtuali di Azure.

> [!div class="nextstepaction"]
> [Configurare il ripristino di emergenza per le macchine virtuali di Azure](azure-to-azure-tutorial-enable-replication.md)
