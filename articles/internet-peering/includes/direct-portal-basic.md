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
ms.openlocfilehash: 79cf4b2edd1a25df427cf15f9ee7f2f331ebd2df
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774640"
---
1. Fare clic su **Crea una risorsa** > **Visualizza tutto**.

    > [!div class="mx-imgBorder"]
    > ![peering di ricerca](../media/setup-seeall.png)

1. Cercare il *peering* nella casella di ricerca e premere *invio* sulla tastiera. Nei risultati fare clic su risorsa **peering** .

    > [!div class="mx-imgBorder"]
    > ![avviare il peering](../media/setup-launch.png)

1. Una volta avviato il **peering** , rivedere la pagina per comprendere i dettagli. Quando si è pronti, fare clic su **Crea**.

    > [!div class="mx-imgBorder"]
    > ![creare il peering](../media/setup-create.png)

1. Nella scheda **nozioni di base** della pagina **creare un peering** compilare i campi come illustrato di seguito.

    > [!div class="mx-imgBorder"]
    > Nozioni di base sul peering ![](../media/setup-basics-tab.png)

    * Scegliere la **sottoscrizione**di Azure.
    * Per **gruppo di risorse**, è possibile scegliere un gruppo di risorse esistente dall'elenco a discesa o creare un nuovo gruppo facendo clic su **Crea nuovo**. Per questo esempio verrà creato un nuovo gruppo di risorse.
    * Il **nome** corrisponde al nome della risorsa e può essere qualsiasi elemento selezionato.
    * L' **area** viene selezionata automaticamente se si sceglie un gruppo di risorse esistente nel passaggio precedente. Se si sceglie di creare un nuovo gruppo di risorse, è necessario anche scegliere l'area di Azure in cui si vuole che la risorsa risieda. Stati Uniti orientali

        > [!NOTE]
        > L'area in cui risiede il gruppo di risorse è indipendente dalla località in cui si vuole creare il peering con Microsoft. È tuttavia consigliabile organizzare le risorse di peering all'interno di gruppi di risorse che si trovano nelle aree di Azure più vicine. Ad esempio: per i peering in Ashburn, è possibile creare un gruppo di risorse in *Stati Uniti orientali* o *Uniti orientali*

    * Scegliere il numero ASN nel campo **ASN peer** .

        > [!IMPORTANT]
        > * Prima di inviare una richiesta di peering, è possibile scegliere solo un ASN con ValidationState come "approvato". Se è stata appena inviata la richiesta Peerasn sugli, attendere 12 ore o in modo che l'associazione ASN venga "approvata". Se l'ASN selezionato è in attesa di convalida, verrà visualizzato un messaggio di errore. 
        > * Se non viene visualizzato il numero di ASN che è necessario scegliere, verificare se è stata selezionata la sottoscrizione corretta. In caso affermativo, controllare se è già stato creato Peerasn sugli usando l' [associazione di peer ASN alla sottoscrizione di Azure](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![nozioni di base sul peering riempite](../media/setup-direct-basics-filled-tab.png)

    * Fare clic su **Avanti: Configuration >** per continuare.
