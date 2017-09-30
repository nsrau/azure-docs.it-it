---
title: "Panoramica delle porte a disponibilità elevata in Azure | Microsoft Docs"
description: "Informazioni sul bilanciamento del carico con le porte a disponibilità elevata in un servizio di bilanciamento del carico interno"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2e41c70b982b97c6aab7b6c0322c193c61370a26
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="high-availability-ports-overview"></a>Panoramica delle porta a disponibilità elevata

Lo SKU Standard di Azure Load Balancer introduce le porte a disponibilità elevata, una funzionalità per la distribuzione del traffico da tutte le porte e per tutti i protocolli supportati. Durante la configurazione di un servizio di bilanciamento del carico interno, gli utenti possono configurare una regola per le porte a disponibilità elevata con cui impostare le porte front-end e back-end su **0** e il protocollo su **all**, consentendo in tal modo a tutto il traffico di passare dal servizio di bilanciamento del carico interno.

L'algoritmo di bilanciamento del carico rimane lo stesso e la destinazione viene selezionata in base alle cinque tuple <Indirizzo IP di origine, Porta di origine, Indirizzo IP di destinazione, Porta di destinazione, Protocollo>. Questa configurazione tuttavia consente una sola regola di bilanciamento del carico per elaborare tutto il traffico disponibile e riduce la complessità della configurazione, ma anche i limiti imposti dal numero massimo di regole di bilanciamento del carico che è possibile aggiungere.

Uno degli scenari critici consentiti dalle porte a disponibilità elevata è la distribuzione a disponibilità elevata delle appliance virtuali di rete nelle reti virtuali di Azure. Un altro scenario comune consentito dalle porte a disponibilità elevata è il bilanciamento del carico per un intervallo di porte. 

## <a name="why-use-high-ha-ports"></a>Vantaggi dell'uso delle porte a disponibilità elevata

I clienti di Azure fanno grande affidamento sulle appliance virtuali di rete per proteggere i carichi di lavoro da diversi tipi di minacce per la sicurezza. Le appliance virtuali di rete devono anche essere affidabili e a disponibilità elevata.  

Le porte a disponibilità elevata riducono la complessità dello scenario a disponibilità elevata delle appliance virtuali di rete eliminando la necessità di soluzioni più complesse, come zookeeper, e aumentano l'affidabilità grazie a opzioni di scalabilità orizzontale e failover più rapido. È ora possibile realizzare la disponibilità elevata delle appliance virtuali di rete aggiungendole nel pool back-end del servizio di bilanciamento del carico interno di Azure e quindi configurando la regola del servizio di bilanciamento del carico per le porte a disponibilità elevata.

L'esempio seguente presenta una distribuzione della rete virtuale hub e spoke, in cui gli spoke forzano il tunneling del traffico alla rete virtuale dell'hub e tramite l'appliance virtuale di rete prima che lasci lo spazio attendibile. Poiché le appliance virtuali di rete si trovano dietro un servizio di bilanciamento del carico interno con la configurazione delle porte a disponibilità elevata, possono elaborare tutto il traffico e inoltrarlo di conseguenza. 

![Esempio di porte a disponibilità elevata](./media/load-balancer-ha-ports-overview/nvaha.png)

Figura 1: Rete virtuale hub e spoke con appliance virtuali di rete distribuite in modalità a disponibilità elevata

## <a name="caveats"></a>Avvertenze

Di seguito sono elencate le configurazioni supportate o le eccezioni per le porte a disponibilità elevata:

- Una singola configurazione IP front-end può avere una sola regola del servizio di bilanciamento del carico DSR con le porte a disponibilità elevata (tutte le porte) oppure può avere una sola regola del servizio di bilanciamento del carico non DSR con le porte a disponibilità elevata (tutte le porte). Non può averle entrambe.
- Una singola configurazione IP dell'interfaccia di rete può avere una sola regola del servizio di bilanciamento del carico non DSR con le porte a disponibilità elevata. Non possono essere configurate altre regole per questa configurazione IP.
- Una singola configurazione IP dell'interfaccia di rete può avere una o più regole del servizio di bilanciamento del carico DSR con le porte a disponibilità elevata, purché tutte le rispettive configurazioni IP front-end siano univoche.
- Due o più regole del servizio di bilanciamento del carico che puntano allo stesso pool back-end possono coesistere se tutte le regole del servizio di bilanciamento del carico sono relative alle porte a disponibilità elevata (solo DSR) o se tutte le regole sono relative alle porte non a disponibilità elevata (DSR e non DSR). Due regole di bilanciamento del carico di questo tipo non possono coesistere se è presente una combinazione di regole per le porte a disponibilità elevata e per le porte non a disponibilità elevata.
- La porta a disponibilità elevata in un tenant abilitato per IPv6 non è disponibile.


## <a name="next-steps"></a>Passaggi successivi

[Configurare le porte a disponibilità elevata in un servizio di bilanciamento del carico interno](load-balancer-configure-ha-ports.md)


