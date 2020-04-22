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
ms.openlocfilehash: b8869e93a7156b24d61ac555c95b9ca7f850ae34
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678524"
---
1. Nella scheda **Configurazione** della pagina **Crea peering** compilare le caselle come illustrato di seguito.

    > [!div class="mx-imgBorder"]
    > ![Creare un tipo di peering di peering per pagine di peering](../media/setup-exchange-conf-tab.png)

    * Per **Tipo di peering**, selezionare **Exchange**.
    * Selezionare **SKU** come **Basic Free**.
    * Selezionare la posizione Metro in cui si vuole convertire il peering in una risorsa di Azure.Select the **Metro** location where you want to convert peering to an Azure resource. Se si dispone di connessioni di peering con Microsoft nella posizione **Metro** selezionata che non vengono convertite in una risorsa di Azure, queste connessioni verranno elencate nella sezione **Connessioni peering** come illustrato. È ora possibile convertire queste connessioni di peering in una risorsa di Azure.You can now convert these peering connections to an Azure resource.

        > [!div class="mx-imgBorder"]
        > ![Elenco delle connessioni di peering](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > Non è possibile modificare le impostazioni per le connessioni peering legacy. Se si desidera aggiungere ulteriori connessioni di peering con Microsoft nella posizione **Metro** selezionata, selezionare **Crea nuovo**. Per ulteriori informazioni, vedere [Creare o modificare un peering](../howto-exchange-portal.md)di Exchange tramite il portale .
        >

1. Selezionare **Rivedi e crea**. Si noti che il portale esegue la convalida di base delle informazioni immesse. Una barra multifunzione nella parte superiore visualizza il messaggio *Esecuzione della convalida finale...*.

    > [!div class="mx-imgBorder"]
    > ![Scheda Convalida peering](../media/setup-direct-review-tab-validation.png)

1. Dopo che il messaggio diventa *Convalida superata,* verificare le informazioni. Inviare la richiesta selezionando **Crea**. Se è necessario modificare la richiesta, selezionare **Indietro** e ripetere i passaggi.

    > [!div class="mx-imgBorder"]
    > ![Invio peering](../media/setup-exchange-review-tab-submit.png)

1. Dopo aver inviato la richiesta, attendere il completamento della distribuzione. Se la distribuzione non riesce, [contattare Microsoft peering](mailto:peering@microsoft.com). Viene visualizzata una distribuzione corretta come illustrato.

    > [!div class="mx-imgBorder"]
    > ![Successo di peering](../media/setup-direct-success.png)
