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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774640"
---
1. Fare clic su **Crea una risorsa** > **Visualizza tutto**.

    > [!div class="mx-imgBorder"]
    > ![Peering di ricerca](../media/setup-seeall.png)

1. Cerca *Peering* nella casella di ricerca e premi *Invio* sulla tastiera. Dai risultati, fai clic su **Risorsa peering.**

    > [!div class="mx-imgBorder"]
    > ![Avviare peering](../media/setup-launch.png)

1. Una volta **avviato peering,** esamina la pagina per conoscere i dettagli. Al termine, fare clic su **Crea**.

    > [!div class="mx-imgBorder"]
    > ![Creare peering](../media/setup-create.png)

1. Nella pagina **Crea peering,** nella scheda **Nozioni di base,** compilare i campi come illustrato di seguito.

    > [!div class="mx-imgBorder"]
    > ![Nozioni di base sul peering](../media/setup-basics-tab.png)

    * Scegliere la **sottoscrizione di**Azure .
    * Per **Gruppo**di risorse , è possibile scegliere un gruppo di risorse esistente dall'elenco a discesa oppure creare un nuovo gruppo facendo clic su **Crea nuovo**. Verrà creato un nuovo gruppo di risorse per questo esempio.
    * **Il nome** corrisponde al nome della risorsa e può essere qualsiasi elemento scelto.
    * **L'area** è selezionata automaticamente se si sceglie un gruppo di risorse esistente nel passaggio precedente. Se si è scelto di creare un nuovo gruppo di risorse, è necessario scegliere anche l'area di Azure in cui si vuole che la risorsa risieda. Stati Uniti orientali

        > [!NOTE]
        > L'area in cui risiede il gruppo di risorse è indipendente dal percorso in cui si vuole creare il peering con Microsoft.Region where resource group resides is indipendente of the location where you want to create peering with Microsoft. Tuttavia, è consigliabile organizzare le risorse di peering all'interno di gruppi di risorse che risiedono nelle aree di Azure più vicine. Ad esempio: per i peering in Ashburn, è possibile creare un gruppo di risorse negli *Stati Uniti orientali* o negli Stati *Uniti orientali2*

    * Scegliere l'ASN nel campo **ASN peer.**

        > [!IMPORTANT]
        > * È possibile scegliere un numero ASN solo con ValidationState come "Approvato" prima di inviare una richiesta di peering. Se hai appena inviato la tua richiesta PeerAsn, attendi 12 ore o giù di lì affinché l'associazione ASN sia "approvata". Se l'ASN selezionato è in attesa di convalida, verrà visualizzato un messaggio di errore. 
        > * Se l'ASN non è visualizzato, verificare di aver selezionato la sottoscrizione corretta. In tal caso, verificare se PeerAsn è già stato creato usando [Associate Peer ASN to Azure Subscription](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![Nozioni di base sul peering riempite](../media/setup-direct-basics-filled-tab.png)

    * Fare clic su **Avanti : Configurazione >** per continuare.
