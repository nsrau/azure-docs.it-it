---
title: includere file
description: includere file
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 134f1dc7cb6e53c181b2f518055e5cb758fccf31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91812718"
---
1. Nella rete WAN virtuale selezionare Hub e quindi **+ Nuovo hub**.

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.jpg" alt-text="Nuovo hub":::

1. Nella pagina Crea hub virtuale completare i campi seguenti.

   * **Area**: selezionare l'area in cui si vuole distribuire l'hub virtuale.
   * **Nome**: immettere il nome con cui denominare l'hub virtuale.
   * **Spazio di indirizzi privato dell'hub**: intervallo di indirizzi dell'hub nella notazione CIDR.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.jpg" alt-text="Nuovo hub":::

1. Nella scheda Da punto a sito completare i campi seguenti:

   * **Unità di scala gateway**, che rappresenta la capacità aggregata del gateway VPN utente.
   * **Configurazione da punto a sito**, creata nel passaggio precedente.
   * **Pool indirizzi client**, per gli utenti remoti.
   * **IP server DNS personalizzato**.

   :::image type="content" source="media/virtual-wan-p2s-hub/hub-with-p2s.png" alt-text="Nuovo hub":::

1. Selezionare **Rivedi e crea**.
1. Nella pagina **Convalida superata** selezionare **Crea**.