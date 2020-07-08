---
title: File di inclusione
titleSuffix: Azure
description: File di inclusione
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 5/22/2020
ms.author: derekol
ms.openlocfilehash: e2804c8f9b1af89ac0ea86ec14136df66d900060
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846159"
---
1. Selezionare **Crea una risorsa** > **Visualizza tutto**.

    > [!div class="mx-imgBorder"]
    > ![Cercare un peering](../media/setup-seeall.png)

1. Cercare **Peering** nella casella di ricerca e premere **Invio** sulla tastiera. Nei risultati, selezionare una risorsa di **Peering**.

    > [!div class="mx-imgBorder"]
    > ![Avviare il peering](../media/setup-launch.png)

1. Dopo l'avvio del **Peering**, rivedere la pagina per comprendere i dettagli. Al termine, selezionare **Crea**.

    > [!div class="mx-imgBorder"]
    > ![Creare un peering](../media/setup-create.png)

1. Nella scheda **Generale** della pagina **Creare un peering**, compilare le caselle come illustrato nell'immagine seguente.

    > [!div class="mx-imgBorder"]
    > ![Scheda Generale della pagina Peering](../media/setup-basics-tab.png)

    * Selezionare la **Sottoscrizione** di Azure.
    * Per **Gruppo di risorse**, è possibile sceglierne uno esistente nell'elenco a discesa o crearne uno nuovo selezionando **Crea nuovo**. Per questo esempio, creare un nuovo gruppo di risorse.
    * Il **nome** corrisponde a quello della risorsa e può essere qualsiasi nome desiderato.
    * Se si sceglie un gruppo di risorse esistente, l'**area** viene selezionata automaticamente. Se si sceglie di creare un nuovo gruppo di risorse, è necessario scegliere anche l'area di Azure in cui deve trovarsi la risorsa.

        > [!NOTE]
        > L'area in cui si trova un gruppo di risorse è indipendente dalla località in cui si vuole creare il peering con Microsoft. Tuttavia, è consigliabile organizzare le risorse di peering all'interno di gruppi di risorse che si trovano nelle aree di Azure più vicine. Ad esempio, per peering in Ashburn è possibile creare un gruppo di risorse nell'area Stati Uniti orientali o Stati Uniti orientali 2.

    * Selezionare l'ASN nella casella **ASN del peer**.

        > [!IMPORTANT]
        > * È possibile scegliere un ASN solo con stato di convalida approvato prima di inviare una richiesta di peering. Se la richiesta dell'ASN del peer è appena stata inviata, attendere 12 ore che venga approvata l'associazione dell'ASN. Se la convalida dell'ASN selezionato è in sospeso, verrà visualizzato un messaggio di errore. 
        > * Se l'ASN che è necessario scegliere non è visualizzato, verificare di aver selezionato la sottoscrizione corretta. Se sì, controllare se è già stato creato un ASN del peer facendo riferimento a [Associare l'ASN del peer alla sottoscrizione di Azure](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![Scheda Generale della pagina Peering compilata](../media/setup-direct-basics-filled-tab.png)

    * Selezionare **Avanti: Configurazione >** per continuare.
