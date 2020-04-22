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
ms.openlocfilehash: e5804aa1b005e670d8b430b1c0a3bd62efd0bb06
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687065"
---
1. Selezionare una connessione di peering da abilitare per il servizio peering di Azure.Select a peering connection you want to enable for Azure Peering Service. Quindi selezionare **...**  >  **Modifica connessione**.
    > [!div class="mx-imgBorder"]
    > ![Connessione peering Modifica connessione](../media/setup-direct-modify-editconnection.png)
1. In **Usa per il servizio di peering**selezionare **Abilitato** e quindi **Salva**.
    > [!div class="mx-imgBorder"]
    > ![Peering connection Enable Peering Service](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. Nella schermata **Panoramica** vengono visualizzati i dettagli della distribuzione. Al termine della distribuzione, selezionare **Vai alla risorsa**.
    > [!div class="mx-imgBorder"]
    > ![La distribuzione è completa](../media/setup-direct-modify-overview-deployment-complete.png)

1. Nel riquadro **Prefissi registrati** selezionare **Aggiungi prefisso registrato**.
    > [!div class="mx-imgBorder"]
    > ![Aggiungi prefisso registrato](../media/setup-direct-modify-add-registered-prefix.png)
1. Registrare un prefisso selezionando un **Nome** e un **Prefisso** e selezionando **Salva**.
    > [!div class="mx-imgBorder"]
    >  ![Registrare un prefissoRegister a prefix](../media/setup-direct-modify-register-a-prefix.png) 

1. Dopo aver creato un prefisso, viene visualizzato nell'elenco Dei **prefissi registrati**. Selezionare il **Nome** del prefisso per visualizzare ulteriori dettagli.
    > [!div class="mx-imgBorder"]
    > ![Prefissi e connessioni registrati](../media/setup-direct-modify-registered-prefixes.png)
1. Nella pagina del prefisso registrato vengono visualizzati i dettagli completi, che includono la **chiave Prefix** per ogni prefisso. Questa chiave deve essere fornita al cliente allocato questo prefisso dal provider ISP. Il cliente può quindi registrare il prefisso all'interno della sottoscrizione utilizzando questa chiave.
    > [!div class="mx-imgBorder"]
    > ![Prefisso con chiave prefissoPrefix with prefix key](../media/setup-direct-modify-registered-prefix-detail.png)