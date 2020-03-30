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
ms.openlocfilehash: 16909e0a5d59b0ae019d91aad00e0168b0c5f433
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775069"
---
1. Nella pagina **Crea peering,** nella scheda **Configurazione,** compilare i campi come illustrato di seguito.

    > [!div class="mx-imgBorder"]
    > ![Configurazione peering - Diretta](../media/setup-direct-conf-tab.png)

    * Per **Tipo di peering**, selezionare *Diretto*.
    * Per **rete Microsoft**, scegliere *AS8075*. Non selezionare ASN 8069. È riservato per applicazioni speciali e utilizzato solo da [Microsoft peering](mailto:peering@microsoft.com).
    * Selezionare **SKU** come *Basic Free*. Non selezionare *Premium Free* poiché è riservato ad applicazioni speciali.
    * Scegliere la posizione Metro in cui si vuole convertire il peering in risorsa di Azure.Choose the **Metro** location for where you want to convert peering to Azure resource. Se si dispone di connessioni di peering con Microsoft nella posizione **Metro** selezionata che non vengono convertite in risorsa di Azure, tali connessioni verranno elencate nella sezione **Connessioni peering** come illustrato di seguito. È ora possibile convertire queste connessioni di peering in risorse di Azure.You can now convert these peering connections to Azure resource.

        > [!div class="mx-imgBorder"]
        > ![Configurazione peering - Diretta - Connessioni legacy](../media/setup-directlegacy-conf-tab.png)

1. Se è necessario aggiornare la larghezza di banda, fare clic sul pulsante di modifica di una riga, come evidenziato di seguito, per modificare le impostazioni di connessione.

    > [!div class="mx-imgBorder"]
    > ![Configurazione peering - Modifica diretta](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Se si desidera aggiungere ulteriori connessioni di peering con Microsoft nella posizione **Metro** selezionata, è possibile farlo facendo clic sul pulsante **Crea nuovo.** Per altre info, vedi [Creare o modificare un peering diretto usando il portale.](../howto-direct-portal.md)
    >

1. Fare clic su **Revisione e creazione**. Osservare che il portale esegue la convalida di base delle informazioni immesse. Questo viene visualizzato in una barra multifunzione nella parte superiore, come *Esecuzione della convalida finale...*.

    > [!div class="mx-imgBorder"]
    > ![Scheda Convalida peering](../media/setup-direct-review-tab-validation.png)

1. Dopo che si passa a *Convalida superata*, verificare le informazioni e inviare la richiesta facendo clic su **Crea**. Se è necessario modificare la richiesta, fare clic su **Precedente** e ripetere i passaggi precedenti.

    > [!div class="mx-imgBorder"]
    > ![Invio peering](../media/setup-direct-review-tab-submit.png)

1. Dopo aver inviato la richiesta, attendere che completi la distribuzione. Se la distribuzione non riesce, [contattare Microsoft peering](mailto:peering@microsoft.com). Una distribuzione corretta verrà visualizzata come di seguito.

    > [!div class="mx-imgBorder"]
    > ![Successo peering](../media/setup-direct-success.png)
