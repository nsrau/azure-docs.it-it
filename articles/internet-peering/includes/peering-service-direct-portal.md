---
title: File di inclusione
titleSuffix: Azure
description: File di inclusione
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 60752cf1b3c05ab7817083e70310ba7b40227dec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129860"
---
1. Fare clic su una connessione di peering che si desidera abilitare per Peering Service e quindi fare clic su **...**  >  **Pulsante Modifica connessione.**
    > [!div class="mx-imgBorder"]
    > ![Modifica connessione peering](../media/setup-direct-modify-editconnection.png)
1. Nella sezione Utilizzo per il servizio di ***peering***, fare clic su **Abilitato** e **Salva**.
    > [!div class="mx-imgBorder"]
    > ![Peering Connection Enable Peering Service](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. Nella schermata Panoramica verranno visualizzati i dettagli della distribuzione. Una volta completata la distribuzione, fare clic su **Vai alla risorsa**.
    > [!div class="mx-imgBorder"]
    > ![La distribuzione è completa](../media/setup-direct-modify-overview-deployment-complete.png)

1. Verrà quindi visualizzato in **Prefissi registrati**impostazioni . Fare clic su **Aggiungi prefisso registrato**.
    > [!div class="mx-imgBorder"]
    > ![Prefissi e connessioni registrati](../media/setup-direct-modify-add-registered-prefix.png)
1. Registrare un prefisso selezionando un **Nome** e un **Prefisso** e fare clic su **Salva**
    > [!div class="mx-imgBorder"]
    >  ![Registrare un prefissoRegister a Prefix](../media/setup-direct-modify-register-a-prefix.png) 

1. Una volta creato un prefisso, lo vedrai nell'elenco dei prefissi registrati. Fare clic sul **nome** del prefisso per visualizzare ulteriori dettagli.
    > [!div class="mx-imgBorder"]
    > ![Prefissi e connessioni registrati](../media/setup-direct-modify-registered-prefixes.png)
1. Nella pagina del prefisso registrato verranno visualizzati i dettagli completi per includere la **chiave Prefix** per ogni prefisso. Questa chiave dovrà essere fornita al cliente allocato questo prefisso dal provider ISP. Il cliente può quindi registrare il prefisso all'interno della sottoscrizione con questa chiave.
    > [!div class="mx-imgBorder"]
    > ![Prefisso con chiave prefissoPrefix with prefix key](../media/setup-direct-modify-registered-prefix-detail.png)