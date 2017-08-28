---
title: Panoramica di BGP con i gateway VPN di Azure | Microsoft Docs
description: Questo articolo fornisce una panoramica di BGP con i gateway VPN di Azure.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: f8c3985c-c128-4f34-835c-0e88742bf36e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/12/2017
ms.author: yushwang
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 60d8dd45ecbd4a075721c25acadb21d4e2fd5448
ms.contentlocale: it-it
ms.lasthandoff: 08/16/2017

---
# <a name="overview-of-bgp-with-azure-vpn-gateways"></a>Panoramica di BGP con i gateway VPN di Azure
Questo articolo fornisce una panoramica di BGP (Border Gateway Protocol) nei gateway VPN di Azure.

BGP è il protocollo di routing standard comunemente usato in Internet per lo scambio di informazioni di routing e raggiungibilità tra due o più reti. Quando viene usato nel contesto di reti virtuali di Azure, BGP consente ai gateway VPN di Azure e ai dispositivi VPN locali, detti peer BGP o router adiacenti, lo scambio di "route" che indicano a entrambi i gateway la disponibilità e la raggiungibilità di tali prefissi per il passaggio attraverso i gateway o i router coinvolti. BGP può anche abilitare il routing di transito tra più reti propagando a tutti gli altri peer BGP le route che un gateway BGP apprende da un peer BGP. 

## <a name="why-use-bgp"></a>Perché usare BGP
BGP è una funzionalità facoltativa che può essere usata con i gateway VPN di Azure basati su route. Prima di abilitare tale funzionalità, assicurarsi che i dispositivi VPN locali supportino BGP. È possibile continuare a usare i gateway VPN di Azure e i dispositivi VPN locali senza BGP. In altre parole occorre scegliere tra l'uso delle route statiche, senza BGP, *oppure* l'uso del routing dinamico con BGP tra le reti e Azure.

BGP offre nuove funzionalità e numerosi vantaggi, illustrati di seguito.

### <a name="support-automatic-and-flexible-prefix-updates"></a>Supporto per l'aggiornamento automatico e flessibile dei prefissi
Con BGP, è sufficiente dichiarare un prefisso minimo a un peer BGP specifico attraverso il tunnel VPN S2S IPsec. La dimensione minima consentita è quella di un prefisso dell'host (/32) dell'indirizzo IP del peer BGP per il dispositivo VPN locale. È possibile controllare quali prefissi di rete locale segnalare in Azure per consentire alla rete virtuale di Azure di accedere.

È anche possibile segnalare prefissi più grandi, che possono includere alcuni dei prefissi di indirizzo della rete virtuale, come uno spazio di indirizzi IP privato di grandi dimensioni, ad esempio 10.0.0.0/8. Si noti che i prefissi non possono essere identici ad alcuno dei prefissi della rete virtuale. Le route identiche ai prefissi della rete virtuale verranno rifiutate.

### <a name="support-multiple-tunnels-between-a-vnet-and-an-on-premises-site-with-automatic-failover-based-on-bgp"></a>Supporto di più tunnel tra una rete virtuale e un sito locale con failover automatico basato su BGP
È possibile stabilire più connessioni tra la rete virtuale di Azure e i dispositivi VPN locali nella stessa località. Questa funzionalità fornisce più tunnel, o percorsi, tra le due reti in una configurazione di tipo attivo/attivo. Se uno dei tunnel viene disconnesso, le route corrispondenti vengono ritirate tramite BGP e il traffico si sposta automaticamente nei tunnel restanti.

Il diagramma seguente mostra un esempio semplice di questa configurazione a disponibilità elevata:

![Più percorsi attivi](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

### <a name="support-transit-routing-between-your-on-premises-networks-and-multiple-azure-vnets"></a>Supporto del routing di transito tra le reti locali e più reti virtuali di Azure
BGP consente a più gateway di apprendere e propagare i prefissi da reti diverse, connesse sia direttamente che indirettamente. Questo consente il routing di transito con i gateway VPN di Azure tra i siti locali o tra più reti virtuali di Azure.

Il diagramma seguente illustra un esempio di una topologia multihop con più percorsi che possono far passare il traffico tra le due reti locali tramite i gateway VPN di Azure all'interno di reti Microsoft:

![Transito multihop](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="bgp-faq"></a>DOMANDE FREQUENTI SU BGP
[!INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)]

## <a name="next-steps"></a>Passaggi successivi
Per i passaggi di configurazione di BGP per le connessioni cross-premise e tra reti virtuali, vedere [Introduzione a BGP nei gateway VPN di Azure](vpn-gateway-bgp-resource-manager-ps.md) .


