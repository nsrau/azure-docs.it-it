---
title: includere file
titleSuffix: Azure
description: includere file
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: e5804aa1b005e670d8b430b1c0a3bd62efd0bb06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81687065"
---
1. Selezionare una connessione di peering che si vuole abilitare per il servizio peering di Azure. Quindi selezionare **...**  >  **Modifica connessione**.
    > [!div class="mx-imgBorder"]
    > ![Connessione di modifica connessione peering](../media/setup-direct-modify-editconnection.png)
1. In **USA per il servizio di peering**selezionare **abilitato** e quindi fare clic su **Salva**.
    > [!div class="mx-imgBorder"]
    > ![Connessione peering Abilita servizio di peering](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. Nella schermata **Panoramica** vengono visualizzati i dettagli della distribuzione. Al termine della distribuzione, selezionare **Vai alla risorsa**.
    > [!div class="mx-imgBorder"]
    > ![La distribuzione è stata completata](../media/setup-direct-modify-overview-deployment-complete.png)

1. Nel riquadro **prefissi registrati** selezionare **Aggiungi prefisso registrato**.
    > [!div class="mx-imgBorder"]
    > ![Aggiungi prefisso registrato](../media/setup-direct-modify-add-registered-prefix.png)
1. Registrare un prefisso selezionando un **nome** e un **prefisso** e selezionando **Save (Salva**).
    > [!div class="mx-imgBorder"]
    >  ![Registrare un prefisso](../media/setup-direct-modify-register-a-prefix.png) 

1. Una volta creato un prefisso, questo viene visualizzato nell'elenco dei **prefissi registrati**. Selezionare il **nome** del prefisso per visualizzare altri dettagli.
    > [!div class="mx-imgBorder"]
    > ![Prefissi e connessioni registrati](../media/setup-direct-modify-registered-prefixes.png)
1. Nella pagina prefisso registrato vengono visualizzati i dettagli completi che includono la **chiave di prefisso** per ogni prefisso. Questa chiave deve essere fornita al cliente allocato questo prefisso dal provider ISP del provider. Il cliente può quindi registrare il prefisso nella propria sottoscrizione usando questa chiave.
    > [!div class="mx-imgBorder"]
    > ![Prefisso con chiave prefisso](../media/setup-direct-modify-registered-prefix-detail.png)