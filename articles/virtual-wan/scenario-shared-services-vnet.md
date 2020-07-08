---
title: 'Scenario: routing ai servizi condivisi reti virtuali'
titleSuffix: Azure Virtual WAN
description: 'Scenari per il routing: configurare route per accedere a un servizio condiviso VNet con un carico di lavoro a cui si vuole accedere ogni VNet e ramo.'
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 64bb4c85399f811c0ab7ff84b297b64734efc491
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567901"
---
# <a name="scenario-route-to-shared-services-vnets"></a>Scenario: routing ai servizi condivisi reti virtuali

Quando si usa il routing dell'hub virtuale WAN virtuale, esistono molti scenari disponibili. In questo scenario, l'obiettivo è quello di configurare le route per accedere a un VNet di **servizio condiviso** con il carico di lavoro che si vuole possa accedere a ogni VNet e ramo (VPN/er/P2S). Per informazioni sul routing degli hub virtuali, vedere [About Virtual Hub routing](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="workflow"></a>Flusso di lavoro dello scenario

Per configurare questo scenario, prendere in considerazione i passaggi seguenti:

1. Identificare il VNet dei **servizi condivisi** .
2. Creare una tabella di route personalizzata. Nell'esempio si fa riferimento a questa tabella di route come **RT_SHARED**. Per i passaggi necessari per creare una tabella di route, vedere [How to Configure Virtual Hub routing](how-to-virtual-hub-routing.md). Usare i valori seguenti come linee guida:

   * **Association Rules**
     * Per **reti virtuali ad *eccezione* dei servizi condivisi VNet**, selezionare il reti virtuali da isolare. Questo implica che tutti questi reti virtuali (ad eccezione dei servizi condivisi VNet) saranno in grado di raggiungere la destinazione in base alle route di RT_SHARED tabella di route.

   * **Propagazione**
      * Per i **rami**, è possibile propagare le route a questa tabella di route, oltre a qualsiasi altra tabella di route già selezionata. A causa di questo passaggio, RT_SHARED tabella di route apprenderà le route da tutte le connessioni ramo (VPN/ER/VPN utente).
      * Per **reti virtuali**selezionare il **VNet dei servizi condivisi**. A causa di questo passaggio, RT_SHARED tabella di route apprenderà le route dalla connessione VNet dei servizi condivisi.

     Questo comporterà la modifica della configurazione di routing, come illustrato nella figura seguente.

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="Servizi condivisi VNet":::

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).
* Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).
