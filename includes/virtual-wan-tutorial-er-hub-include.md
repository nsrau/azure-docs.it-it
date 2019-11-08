---
title: File di inclusione
description: File di inclusione
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6f910dbe91ed8e1cb65eefa6dfc48c72a689bf25
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491431"
---
1. Individuare la rete WAN virtuale creata. Nella pagina della rete WAN virtuale selezionare **Hub** nella sezione **Connettività**.
2. Nella pagina Hub selezionare **+Nuovo hub** per aprire la pagina **Crea hub virtuale**.
3. Nella scheda **Generale** della pagina **Crea hub virtuale** completare i campi seguenti:

   ![Nozioni di base](./media/virtual-wan-tutorial-er-hub-include/hub1.png "Nozioni di base")

    **Dettagli del progetto**

   * Regione (precedentemente denominata Posizione)
   * NOME
   * Spazio di indirizzi privato dell'hub. Lo spazio di indirizzi minimo per la creazione di un hub è /24; qualsiasi intervallo compreso tra /25 e /32 genererà pertanto un errore durante la creazione.
4. Selezionare la scheda **ExpressRoute**.

5. Nella scheda **ExpressRoute** completare i campi seguenti:

   ![ExpressRoute](./media/virtual-wan-tutorial-er-hub-include/hub2.png "ExpressRoute")

   * Scegliere **Sì** per creare un gateway **ExpressRoute**.
   * Nell'elenco a discesa selezionare il valore **Unità di scala gateway**.
6. Selezionare **Rivedi e crea** per convalidare la selezione.
7. Fare clic su **Crea** per creare l'hub. Dopo 30 minuti, fare clic su **Aggiorna** per visualizzare l'hub nella pagina **Hub**. Selezionare **Vai alla risorsa** per passare alla risorsa.