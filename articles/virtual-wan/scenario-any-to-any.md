---
title: 'Scenario: any-to-any'
titleSuffix: Azure Virtual WAN
description: Scenari di routing-any-to-any
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 09dddad24794491b53a11f7b0e4347f43f11598b
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92440485"
---
# <a name="scenario-any-to-any"></a>Scenario: any-to-any

Quando si usa il routing dell'hub virtuale WAN virtuale, esistono molti scenari disponibili. In uno scenario any-to-any qualsiasi spoke può raggiungere un altro spoke. Quando esistono più hub, il routing da Hub a hub (noto anche come Interhub) è abilitato per impostazione predefinita nella rete WAN virtuale standard. Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Progettazione

Per determinare il numero di tabelle di route necessarie in uno scenario WAN virtuale, è possibile creare una matrice di connettività, in cui ogni cella indica se un'origine (riga) può comunicare con una destinazione (colonna).

| Da |   A |  *Reti virtuali* | *Rami* |
| -------------- | -------- | ---------- | ---|
| Reti virtuali     | &#8594;| Connessione diretta | Connessione diretta |
| Rami   | &#8594;| Connessione diretta  | Connessione diretta |

Ognuna delle celle della tabella precedente descrive se una connessione WAN virtuale (il lato "da" del flusso, le intestazioni di riga) comunica con un prefisso di destinazione (il lato "a" del flusso, le intestazioni di colonna in corsivo). In questo scenario non sono presenti firewall o appliance virtuali di rete, quindi la comunicazione scorre direttamente sulla rete WAN virtuale, quindi la parola "Direct" nella tabella.

Poiché tutte le connessioni da reti virtuali e Branch (VPN, ExpressRoute e VPN utente) presentano gli stessi requisiti di connettività, è necessaria una singola tabella di route. Di conseguenza, tutte le connessioni verranno associate e propagate alla stessa tabella di route, ovvero la tabella di route predefinita:

* Reti virtuali:
  * Tabella di route associata: **predefinita**
  * Propagazione alle tabelle di route: **predefinita**
* Rami
  * Tabella di route associata: **predefinita**
  * Propagazione alle tabelle di route: **predefinita**

Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Architettura

Nella **Figura 1**, tutti i reti virtuali e i rami (VPN, EXPRESSROUTE, P2S) possono essere raggiungibili tra loro. In un hub virtuale le connessioni funzionano come segue:

* Una connessione VPN connette un sito VPN a un gateway VPN.
* Una connessione di rete virtuale connette una rete virtuale a un hub virtuale. Il router dell'hub virtuale fornisce la funzionalità di transito tra reti virtuali.
* Una connessione ExpressRoute connette un circuito ExpressRoute a un gateway ExpressRoute.

Queste connessioni (per impostazione predefinita al momento della creazione) sono associate alla tabella di route predefinita, a meno che non si imposti la configurazione di routing della connessione a **Nessuna**o a una tabella di route personalizzata. Queste connessioni propagano anche le route, per impostazione predefinita alla tabella di route predefinita. Questo è ciò che consente uno scenario any-to-any in cui qualsiasi spoke (VNet, VPN, ER, P2S) può raggiungere l'uno con l'altro.

**Figura 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="Figura 1":::

## <a name="workflow"></a><a name="workflow"></a>Flusso di lavoro

Questo scenario è abilitato per impostazione predefinita per la rete WAN virtuale standard. Se l'impostazione per Branch-to-Branch è disabilitata nella configurazione WAN, ciò non consentirà la connettività tra spoke del ramo. VPN/ExpressRoute/VPN utente sono considerati spoke del ramo nella rete WAN virtuale

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).
* Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).
