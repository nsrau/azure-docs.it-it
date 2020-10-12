---
title: includere file
titleSuffix: Azure
description: includere file
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: b8869e93a7156b24d61ac555c95b9ca7f850ae34
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81678524"
---
1. Nella scheda **configurazione** della pagina **Crea un peering** compilare le caselle come illustrato qui.

    > [!div class="mx-imgBorder"]
    > ![Creare un tipo di peering di scambio di pagine peering](../media/setup-exchange-conf-tab.png)

    * Per **tipo di peering**selezionare **Exchange**.
    * Selezionare **SKU** come **Basic Free**.
    * Selezionare la **località** in cui si vuole convertire il peering in una risorsa di Azure. Se si dispone di connessioni peering con Microsoft nella località **metro** selezionata che non vengono convertite in una risorsa di Azure, queste connessioni verranno elencate nella sezione **connessioni peering** , come illustrato. È ora possibile convertire le connessioni peering in una risorsa di Azure.

        > [!div class="mx-imgBorder"]
        > ![Elenco connessioni peering](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > Non è possibile modificare le impostazioni per le connessioni di peering legacy. Se si desidera aggiungere ulteriori connessioni peering con Microsoft nella località **metro** selezionata, selezionare **Crea nuovo**. Per altre informazioni, vedere [creare o modificare un peering di Exchange tramite il portale](../howto-exchange-portal.md).
        >

1. Selezionare **Rivedi e crea**. Si noti che il portale esegue la convalida di base delle informazioni immesse. Una barra multifunzione nella parte superiore Visualizza il messaggio *che esegue la convalida finale...*.

    > [!div class="mx-imgBorder"]
    > ![Scheda convalida peering](../media/setup-direct-review-tab-validation.png)

1. Dopo che il messaggio è stato modificato in *convalida superata*, verificare le informazioni. Inviare la richiesta selezionando **Crea**. Se è necessario modificare la richiesta, selezionare **precedente** e ripetere i passaggi.

    > [!div class="mx-imgBorder"]
    > ![Invio peering](../media/setup-exchange-review-tab-submit.png)

1. Dopo aver inviato la richiesta, attendere il completamento della distribuzione. Se la distribuzione ha esito negativo, contattare il team [Peer Microsoft](mailto:peering@microsoft.com). Viene visualizzata una distribuzione corretta, come illustrato.

    > [!div class="mx-imgBorder"]
    > ![Il peering è riuscito](../media/setup-direct-success.png)
