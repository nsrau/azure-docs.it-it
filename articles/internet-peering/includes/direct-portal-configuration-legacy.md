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
ms.openlocfilehash: 632490498b8dd13414657edb9518cd543ac07af6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81678847"
---
1. Nella scheda **configurazione** della pagina **Crea un peering** compilare le caselle come illustrato qui.

    > [!div class="mx-imgBorder"]
    > ![Creare una scheda di configurazione della pagina di peering](../media/setup-direct-conf-tab.png)

    * Per **tipo di peering**selezionare **diretta**.
    * Per **Microsoft Network**selezionare **AS8075**. Non selezionare ASN 8069. È riservata per applicazioni speciali e viene usata solo dal [peering Microsoft](mailto:peering@microsoft.com).
    * Selezionare **SKU** come **Basic Free**. Non selezionare Premium Free perché è riservato per applicazioni speciali.
    * Selezionare la **località** in cui si vuole convertire il peering in una risorsa di Azure. Se si dispone di connessioni peering con Microsoft nella località **metro** selezionata che non vengono convertite in una risorsa di Azure, queste connessioni verranno elencate nella sezione **connessioni peering** , come illustrato. È ora possibile convertire le connessioni peering in una risorsa di Azure.

        > [!div class="mx-imgBorder"]
        > ![Elenco connessioni peering](../media/setup-directlegacy-conf-tab.png)

1. Se è necessario aggiornare la larghezza di banda, selezionare il pulsante modifica per una riga per modificare le impostazioni di connessione.

    > [!div class="mx-imgBorder"]
    > ![Pulsante modifica](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Se si desidera aggiungere ulteriori connessioni peering con Microsoft nella località **metro** selezionata, selezionare **Crea nuovo**. Per altre informazioni, vedere [creare o modificare un peering diretto usando il portale](../howto-direct-portal.md).
    >

1. Selezionare **Rivedi e crea**. Si noti che il portale esegue la convalida di base delle informazioni immesse. Una barra multifunzione nella parte superiore Visualizza il messaggio *che esegue la convalida finale...*.

    > [!div class="mx-imgBorder"]
    > ![Scheda convalida peering](../media/setup-direct-review-tab-validation.png)

1. Dopo che il messaggio è stato modificato in *convalida superata*, verificare le informazioni. Inviare la richiesta selezionando **Crea**. Per modificare la richiesta, selezionare **precedente** e ripetere i passaggi.

    > [!div class="mx-imgBorder"]
    > ![Invio peering](../media/setup-direct-review-tab-submit.png)

1. Dopo aver inviato la richiesta, attendere il completamento della distribuzione. Se la distribuzione ha esito negativo, contattare il team [Peer Microsoft](mailto:peering@microsoft.com). Una distribuzione corretta viene visualizzata come illustrato di seguito.

    > [!div class="mx-imgBorder"]
    > ![Il peering è riuscito](../media/setup-direct-success.png)
