---
title: File di inclusione
titleSuffix: Azure
description: File di inclusione
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 7d7b9f847cdcc4ab4b1ff065425eebe07fb4d888
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775212"
---
1. Nella pagina **Crea peering,** nella scheda **Configurazione,** compilare i campi come illustrato di seguito.

    > [!div class="mx-imgBorder"]
    > ![Configurazione peering - Exchange](../media/setup-exchange-conf-tab.png)

    * Per **Tipo di peering**, selezionare *Exchange*.
    * Selezionare **SKU** come *Basic Free*.
    * Scegliere la posizione Metro in cui si vuole convertire il peering in risorsa di Azure.Choose the **Metro** location for where you want to convert peering to Azure resource. Se si dispone di connessioni di peering con Microsoft nella posizione **Metro** selezionata che non vengono convertite in risorsa di Azure, tali connessioni verranno elencate nella sezione **Connessioni peering** come illustrato di seguito. È ora possibile convertire queste connessioni di peering in risorse di Azure.You can now convert these peering connections to Azure resource.

        > [!div class="mx-imgBorder"]
        > ![Configurazione peering - Exchange - Connessioni legacy](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > Non è possibile modificare le impostazioni per le connessioni peering legacy. Se si desidera aggiungere ulteriori connessioni di peering con Microsoft nella posizione **Metro** selezionata, è possibile farlo facendo clic sul pulsante **Crea nuovo.** Per altre informazioni, vedere [Creare o modificare un peering di Exchange tramite il portale.](../howto-exchange-portal.md)
        >

1. Fare clic su **Revisione e creazione**. Osservare che il portale esegue la convalida di base delle informazioni immesse. Questo viene visualizzato in una barra multifunzione nella parte superiore, come *Esecuzione della convalida finale...*.

    > [!div class="mx-imgBorder"]
    > ![Scheda Convalida peering](../media/setup-direct-review-tab-validation.png)

1. Dopo che si passa a *Convalida superata*, verificare le informazioni e inviare la richiesta facendo clic su **Crea**. Se è necessario modificare la richiesta, fare clic su **Precedente** e ripetere i passaggi precedenti.

    > [!div class="mx-imgBorder"]
    > ![Invio peering](../media/setup-exchange-review-tab-submit.png)

1. Dopo aver inviato la richiesta, attendere che completi la distribuzione. Se la distribuzione non riesce, [contattare Microsoft peering](mailto:peering@microsoft.com). Una distribuzione corretta verrà visualizzata come di seguito.

    > [!div class="mx-imgBorder"]
    > ![Successo peering](../media/setup-direct-success.png)
