---
title: includere il file
titleSuffix: Azure
description: File di inclusione
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 00c24212706555667ad4680c086ece24f15ca59a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678544"
---
1. Selezionare **Crea una risorsa** > **Vedi tutto**.

    > [!div class="mx-imgBorder"]
    > ![Peering di ricerca](../media/setup-seeall.png)

1. Cercare il **peering** nella casella di ricerca e premere **invio** sulla tastiera. Nei risultati selezionare una risorsa di **peering** .

    > [!div class="mx-imgBorder"]
    > ![Avvia peering](../media/setup-launch.png)

1. Dopo l'avvio del **peering** , rivedere la pagina per comprendere i dettagli. Quando si è pronti, selezionare **Crea**.

    > [!div class="mx-imgBorder"]
    > ![Crea peering](../media/setup-create.png)

1. Nella pagina **Crea un peering** , nella scheda **nozioni di base** , compilare le caselle come illustrato qui.

    > [!div class="mx-imgBorder"]
    > ![Scheda nozioni di base sul peering](../media/setup-basics-tab.png)

    * Selezionare la **sottoscrizione**di Azure.
    * Per **gruppo di risorse**, è possibile scegliere un gruppo di risorse esistente dall'elenco a discesa o creare un nuovo gruppo selezionando **Crea nuovo**. Per questo esempio verrà creato un nuovo gruppo di risorse.
    * Il **nome** corrisponde al nome della risorsa e può essere qualsiasi elemento selezionato.
    * Se si sceglie un gruppo di risorse esistente, l' **area** viene selezionata. Se si sceglie di creare un nuovo gruppo di risorse, è necessario anche scegliere l'area di Azure in cui si vuole che la risorsa risieda.

        > [!NOTE]
        > L'area in cui risiede un gruppo di risorse è indipendente dalla località in cui si vuole creare il peering con Microsoft. Tuttavia, è consigliabile organizzare le risorse di peering all'interno di gruppi di risorse che si trovano nelle aree di Azure più vicine. Per i peering in Ashburn, ad esempio, è possibile creare un gruppo di risorse in Stati Uniti orientali o Uniti orientali.

    * Selezionare ASN nella casella **ASN peer** .

        > [!IMPORTANT]
        > * È possibile scegliere un ASN con ValidationState come approvato prima di inviare una richiesta di peering. Se è appena stata inviata la richiesta Peerasn sugli, attendere 12 ore o in modo che l'associazione ASN venga approvata. Se l'ASN selezionato è in attesa di convalida, verrà visualizzato un messaggio di errore. 
        > * Se non viene visualizzato l'ASN che è necessario scegliere, verificare di aver selezionato la sottoscrizione corretta. In caso affermativo, controllare se è già stato creato Peerasn sugli usando l' [associazione dell'ASN peer alla sottoscrizione di Azure](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![Nozioni fondamentali sul peering compilate](../media/setup-direct-basics-filled-tab.png)

    * Fare clic su **Next: Configuration >** per continuare.
