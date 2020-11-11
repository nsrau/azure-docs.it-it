---
title: 'Scenario: routing personalizzato del firewall di Azure per la rete WAN virtuale'
titleSuffix: Azure Virtual WAN
description: Scenari per il routing del routing del traffico tra reti virtuali direttamente, ma usare il firewall di Azure per VNet->Internet/Branch e Branch per flussi di traffico VNet
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: d083607782f96744ecbd7d23976f77ee53fec49d
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515570"
---
# <a name="scenario-azure-firewall---custom"></a>Scenario: firewall di Azure-personalizzato

Quando si usa il routing dell'hub virtuale WAN virtuale, esistono molti scenari disponibili. In questo scenario, l'obiettivo è instradare il traffico tra reti virtuali direttamente, ma usare il firewall di Azure per i flussi di traffico da VNet a Internet/ramo e da ramo a VNet.

## <a name="design"></a><a name="design"></a>Progettazione

Per determinare il numero di tabelle di route necessarie, è possibile creare una matrice di connettività, in cui ogni cella indica se un'origine (riga) può comunicare con una destinazione (colonna). La matrice di connettività in questo scenario è semplice, ma è coerente con altri scenari, ma è comunque possibile esaminarla.

**Matrice di connettività**

| Da           | Con:      | *Reti virtuali*      | *Rami*    | *Internet*   |
|---             |---       |---           |---            |---           |
| **Reti virtuali**      |   &#8594;|    Connessione diretta    |     AzFW      |     AzFW     |
| **Rami**   |   &#8594;|    AzFW      |    Connessione diretta     |    Connessione diretta    |

Nella tabella precedente, una "diretta" rappresenta la connettività diretta tra due connessioni senza il traffico che attraversa il firewall di Azure nella rete WAN virtuale e "AzFW" indica che il flusso passerà attraverso il firewall di Azure. Poiché nella matrice sono presenti due modelli di connettività distinti, saranno necessarie due tabelle di route che verranno configurate nel modo seguente:

* Reti virtuali:
  * Tabella di route associata: **RT_VNet**
  * Propagazione alle tabelle di route: **RT_VNet**
* Rami
  * Tabella di route associata: **predefinita**
  * Propagazione alle tabelle di route: **predefinita**

> [!NOTE]
> È possibile creare un'istanza WAN virtuale separata con un singolo hub virtuale sicuro in ogni area, quindi è possibile connettere ogni WAN virtuale tra loro tramite VPN da sito a sito.

Per informazioni sul routing degli hub virtuali, vedere [About Virtual Hub routing](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>Flusso di lavoro

In questo scenario si vuole instradare il traffico attraverso il firewall di Azure per il traffico da VNet a Internet, da VNet a ramo o da ramo a VNet, ma si vuole passare direttamente al traffico da VNet a VNet. Se è stato usato gestione firewall di Azure, le impostazioni della route vengono inserite automaticamente nella **tabella di route predefinita**. Il traffico privato si applica a VNet e a rami, il traffico Internet si applica a 0.0.0.0/0.

VPN, ExpressRoute e le connessioni VPN utente sono denominate collettivamente Branch e associate alla stessa tabella di route (predefinita). Tutte le connessioni VPN, ExpressRoute e VPN utente propagano le route allo stesso set di tabelle di route. Per configurare questo scenario, prendere in considerazione i passaggi seguenti:

1. Creare una tabella di route personalizzata **RT_VNet**.
1. Creare una route per l'attivazione da VNet a Internet e da VNet a ramo: 0.0.0.0/0 con l'hop successivo che punta al firewall di Azure. Nella sezione propagazione si assicurerà che siano selezionate reti virtuali che garantiscano percorsi più specifici, consentendo in questo modo il flusso di traffico diretto da VNet a VNet.

   * In **associazione:** selezionare reti virtuali che implica che reti virtuali raggiungerà la destinazione in base alle route della tabella di route.
   * In **propagazione:** selezionare reti virtuali che comporterà la propagazione di reti virtuali a questa tabella di route. in altre parole, le route più specifiche vengono propagate a questa tabella di route, garantendo in tal modo il flusso diretto del traffico tra VNet e VNet.

1. Aggiungere una route statica aggregata per reti virtuali nella **tabella di route predefinita** per attivare il flusso da ramo a VNet tramite il firewall di Azure.

   * Tenere presente che i rami vengono associati e propagati alla tabella di route predefinita.
   * I rami non vengono propagati a RT_VNet tabella di route. In questo modo si garantisce il flusso del traffico da VNet a ramo tramite il firewall di Azure.

Questo comporterà la modifica della configurazione di routing, come illustrato nella **Figura 1**.

**Figura 1**

:::image type="content" source="./media/routing-scenarios/between-vnets-firewall/routing.png" alt-text="Figura 1":::

> [!NOTE]
> Gli hub WAN virtuali e le reti virtuali connesse devono trovarsi nella stessa area di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).
* Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).
* Per altre informazioni su come configurare il routing dell'hub virtuale, vedere [How to Configure Virtual Hub routing](how-to-virtual-hub-routing.md).
