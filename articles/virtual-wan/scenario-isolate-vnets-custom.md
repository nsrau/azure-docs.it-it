---
title: 'Scenario: isolamento personalizzato per reti virtuali'
titleSuffix: Azure Virtual WAN
description: "Scenari per il routing: impedire che i reti virtuali selezionati siano in grado di raggiungere l'altro"
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 12bc99d24472780f87a6b2a83befdbbf12944860
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267721"
---
# <a name="scenario-custom-isolation-for-vnets"></a>Scenario: isolamento personalizzato per reti virtuali

Quando si usa il routing dell'hub virtuale WAN virtuale, esistono molti scenari disponibili. In uno scenario di isolamento personalizzato per reti virtuali, l'obiettivo è impedire che un set specifico di reti virtuali riesca a raggiungere un altro set specifico di reti virtuali. Tuttavia, reti virtuali sono necessari per raggiungere tutti i rami (VPN/ER/VPN utente). Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Progettazione

Per determinare il numero di tabelle di route necessarie, è possibile creare una matrice di connettività. Per questo scenario l'aspetto sarà simile al seguente, in cui ogni cella indica se un'origine (riga) può comunicare con una destinazione (colonna):

| From | Con:| *Reti virtuali blu* | *Reti virtuali rosso* | *Rami*|
|---|---|---|---|---|
| **Reti virtuali blu** |   &#8594;|      X        |               |       X      |
| **Reti virtuali rosso**  |   &#8594;|              |       X       |       X      |
| **Rami**   |   &#8594;|     X        |       X       |       X      |

Ogni cella della tabella precedente descrive se una connessione WAN virtuale (il lato "da" del flusso, le intestazioni di riga nella tabella) apprende un prefisso di destinazione (il lato "a" del flusso, le intestazioni di colonna in corsivo nella tabella) per un flusso di traffico specifico, dove una "X" significa che la connettività viene fornita dalla rete WAN virtuale.

Il numero di modelli di riga diversi sarà il numero di tabelle di route che saranno necessarie in questo scenario. In questo caso, tre tabelle Route route che chiameremo **RT_BLUE** e **RT_RED** per le reti virtuali e **predefinite** per i rami. Tenere presente che i rami devono sempre essere associati alla tabella di routing predefinita.

È necessario che i rami apprendano i prefissi di reti virtuali sia di colore rosso che blu, in modo che tutti reti virtuali debbano propagarsi a default (in aggiunta a **RT_BLUE** o **RT_RED**). Reti virtuali blu e rosso dovranno apprendere i prefissi dei rami, in modo che i rami vengano propagati alle tabelle di route **RT_BLUE** e **RT_RED** . Di conseguenza, si tratta della progettazione finale:

* Reti virtuali blu:
  * Tabella di route associata: **RT_BLUE**
  * Propagazione alle tabelle di route: **RT_BLUE** e **default**
* Reti virtuali rosse:
  * Tabella di route associata: **RT_RED**
  * Propagazione alle tabelle di route: **RT_RED** e **default**
* Rami
  * Tabella di route associata: **predefinita**
  * Propagazione alle tabelle di route: **RT_BLUE**, **RT_RED** e **default**

> [!NOTE]
> Poiché è necessario associare tutti i rami alla tabella di route predefinita, nonché propagarsi allo stesso set di tabelle di routing, tutti i rami avranno lo stesso profilo di connettività. In altre parole, non è possibile applicare il concetto rosso/blu per reti virtuali ai rami.

> [!NOTE]
> Se la rete WAN virtuale viene distribuita in più aree, sarà necessario creare il **RT_BLUE** e **RT_RED** le tabelle di route in ogni hub e le route da ogni connessione VNet devono essere propagate alle tabelle di route in ogni hub virtuale usando le etichette di propagazione.

Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="architecture"></a>Flusso di lavoro

Nella **Figura 1**sono presenti connessioni VNet blu e rosse.

* Il reti virtuali connesso a blu può raggiungere l'altro, oltre a raggiungere tutte le connessioni di Branch (VPN/ER/P2S).
* Il reti virtuali rosso può raggiungere l'altro, oltre a raggiungere tutte le connessioni di Branch (VPN/ER/P2S).

Quando si configura il routing, tenere presente i passaggi seguenti.

1. Creare due tabelle di route personalizzate nell'portale di Azure, **RT_BLUE** e **RT_RED**.
2. Per **RT_BLUE**della tabella di route, per le impostazioni seguenti:
   * **Associazione**: selezionare tutti reti virtuali blu.
   * **Propagazione**: per i Branch selezionare l'opzione per i rami, le connessioni che implicano il ramo (VPN/er/P2S) propagheranno le route a questa tabella di route.
3. Ripetere gli stessi passaggi per **RT_RED** tabella di route per reti virtuali e rami rossi (VPN/er/P2S).

Questo comporterà la modifica della configurazione di routing, come illustrato nella figura seguente.

**Figura 1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="Figura 1":::

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).
* Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).
