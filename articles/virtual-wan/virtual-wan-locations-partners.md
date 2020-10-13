---
title: Partner e località WAN virtuali di Azure | Microsoft Docs
description: Questo articolo contiene un elenco di partner WAN virtuali di Azure e località dell'hub.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: d1655b42b57edaeeaaaada802a248f56e0ba1470
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91801461"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Posizioni dei partner e degli hub virtuali della rete WAN virtuale di Azure

Questo articolo fornisce informazioni sulle aree e i partner supportati da WAN virtuali per la connettività all'hub virtuale.

La rete WAN virtuale di Azure è un servizio di rete che offre connettività tra succursali ottimizzata e automatizzata tramite Azure. La rete WAN virtuale consente di connettere e configurare i dispositivi delle succursali per la comunicazione con Azure. Questa operazione può essere eseguita manualmente o usando i dispositivi del provider tramite un partner WAN virtuale. L'uso di dispositivi partner consente di semplificare l'uso, la semplificazione della connettività e la gestione della configurazione.

La connettività dal dispositivo locale viene stabilita in modo automatico per l'hub virtuale. Un hub virtuale è una rete virtuale gestita da Microsoft. L'hub contiene vari endpoint di servizio per abilitare la connettività dalla rete locale (vpnsite). È possibile avere solo un hub per area.

## <a name="branch-ipsec-connectivity-automation-from-partners"></a><a name="automation"></a>Automazione della connettività IPSec del ramo dai partner

I dispositivi che si connettono alla rete WAN virtuale di Azure dispongono di un'automazione incorporata per la connessione. Questa è in genere impostata nell'interfaccia utente di gestione del dispositivo (o equivalente), che consente di impostare la gestione della configurazione e della connettività tra il dispositivo VPN della succursale e un endpoint VPN dell'hub virtuale di Azure (gateway VPN).

L'automazione di alto livello seguente è impostata nella console del dispositivo/centro di gestione:

* Autorizzazioni appropriate per il dispositivo per accedere al gruppo di risorse della rete WAN virtuale di Azure
* Caricamento del dispositivo della succursale nella rete WAN virtuale di Azure
* Download automatico di informazioni relative alla connettività di Azure
* Configurazione del dispositivo della succursale locale 

Alcuni partner di connettività possono estendere l'automazione in modo da includere la creazione della rete virtuale dell'hub virtuale di Azure e di un Gateway VPN. Per altre informazioni sull'automazione, vedere [linee guida di automazione per i partner WAN virtuali](virtual-wan-configure-automation-providers.md).

## <a name="branch-ipsec-connectivity-partners"></a><a name="partners"></a>Partner di connettività IPSec del ramo

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

I partner seguenti sono disponibili sulla nostra roadmap per il prossimo futuro: 128 Technologies, Arista, Cisco Systems (Viptela), F5 Networks, Oracle SD-WAN e SharpLink.

## <a name="partners-with-integrated-virtual-hub-offerings"></a>Partner con offerte di hub virtuale integrate
Oltre a avere la connettività IPSec automatizzata per le succursali, alcuni partner offrono **appliance virtuali di rete (appliance virtuali)** che possono essere integrate direttamente nell'hub WAN virtuale di Azure.  Ciò consente ai clienti di terminare le connessioni di Branch in un'appliance di terze parti compatibile nell'hub virtuale.  

I partner che offrono l'appliance virtuale di rete nell'hub WAN virtuale devono:

* L'automazione della connettività IPSec è stata implementata dal dispositivo Branch e l'offerta dell'appliance virtuale di rete è stata caricata nell'hub WAN virtuale di Azure.
* Disporre di un'offerta di appliance virtuale di rete esistente disponibile in Azure Marketplace.

Se si è un partner e si hanno domande sull'appliance virtuale di gestione nell'offerta dell'hub virtuale, contattaci inviando un messaggio di posta elettronica a vwannvaonboarding@microsoft.com

## <a name="integrated-virtual-hub-nva-partners"></a>Partner appliance virtuale di hub integrato
Questi partner dispongono di offerte di **applicazioni gestite** ora disponibili per la distribuzione nell'hub WAN virtuale.

|Partner|Guida alla configurazione/procedura/distribuzione|
|---|---|
|[Barracuda Networks](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overviewus/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overview)| [Guida alla distribuzione di Barracuda CloudGen WAN](https://campus.barracuda.com/product/cloudgenwan/doc/91980640/deployment/)|
|[Cisco Cloud Service Router (CSR) VWAN](https://aka.ms/ciscoMarketPlaceOffer)| Durante l'anteprima pubblica della rete WAN Cisco Services (CSR) nell'hub VWAN, Cisco richiede che il cliente finale venga registrato come cliente Cisco EFT (Early Field Trial) inviando un messaggio di posta elettronica a vwan_public_preview@external.cisco.com e richiedendo la guida alla distribuzione di vManage. |

I partner seguenti sono disponibili per l'inclusione di appliance virtuale di rete nell'hub virtuale nel prossimo futuro: Citrix, versa Networks e VeloCloud.

## <a name="locations"></a><a name="locations"></a>Località

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti sulla rete WAN virtuale](virtual-wan-faq.md).

* Per altre informazioni su come automatizzare la connettività alla rete WAN virtuale di Azure, vedere [linee guida di automazione per i partner WAN virtuali](virtual-wan-configure-automation-providers.md).
