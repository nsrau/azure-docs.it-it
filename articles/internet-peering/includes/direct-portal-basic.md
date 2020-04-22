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
ms.openlocfilehash: 00c24212706555667ad4680c086ece24f15ca59a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678544"
---
1. Selezionare **Crea una risorsa** > **Visualizza tutto**.

    > [!div class="mx-imgBorder"]
    > ![Peering di ricerca](../media/setup-seeall.png)

1. Cercare **Peering** nella casella di ricerca e selezionare **Invio** sulla tastiera. Nei risultati selezionare una risorsa **peering.**

    > [!div class="mx-imgBorder"]
    > ![Avviare peering](../media/setup-launch.png)

1. Dopo l'avvio **del peering,** esamina la pagina per comprendere i dettagli. Quando si è pronti, selezionare **Crea**.

    > [!div class="mx-imgBorder"]
    > ![Creare peering](../media/setup-create.png)

1. Nella scheda **Nozioni di base** della pagina **Crea peering** compilare le caselle come illustrato di seguito.

    > [!div class="mx-imgBorder"]
    > ![Scheda Nozioni di base sui peer](../media/setup-basics-tab.png)

    * Selezionare la **sottoscrizione di**Azure .
    * Per **Gruppo**di risorse , è possibile scegliere un gruppo di risorse esistente dall'elenco a discesa oppure creare un nuovo gruppo selezionando **Crea nuovo**. Creeremo un nuovo gruppo di risorse per questo esempio.
    * **Il nome** corrisponde al nome della risorsa e può essere qualsiasi elemento desiderato.
    * **L'opzione Area** è selezionata automaticamente se si sceglie un gruppo di risorse esistente. Se si è scelto di creare un nuovo gruppo di risorse, è inoltre necessario scegliere l'area di Azure in cui si vuole che la risorsa risieda.

        > [!NOTE]
        > L'area in cui risiede un gruppo di risorse è indipendente dalla posizione in cui si vuole creare il peering con Microsoft.The region where a resource group resides is indipendente of the location where you want to create peering with Microsoft. È tuttavia consigliabile organizzare le risorse di peering all'interno di gruppi di risorse che risiedono nelle aree di Azure più vicine. For example, for peerings in Ashburn, you can create a resource group in East US or East US2.

    * Selezionare l'ASN nella casella **ASN peer.**

        > [!IMPORTANT]
        > * È possibile scegliere un numero ASN solo con ValidationState come approvato prima di inviare una richiesta di peering. Se hai appena inviato la tua richiesta PeerAsn, attendi circa 12 ore per l'approvazione dell'associazione ASN. Se l'ASN selezionato è in attesa di convalida, verrà visualizzato un messaggio di errore. 
        > * Se l'ASN non è visualizzato, verificare di aver selezionato l'abbonamento corretto. In tal caso, verificare se PeerAsn è già stato creato utilizzando [Associa asn peer alla sottoscrizione](../howto-subscription-association-portal.md)di Azure.

        > [!div class="mx-imgBorder"]
        > ![Nozioni di base sui peering inserite](../media/setup-direct-basics-filled-tab.png)

    * Selezionare **Avanti : Configurazione >** per continuare.
