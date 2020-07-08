---
title: Partner e località WAN virtuali di Azure | Microsoft Docs
description: Questo articolo contiene un elenco di partner WAN virtuali di Azure e località dell'hub.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: 2f3641bf540b188ce3d2333ccd6bcf4a926ba130
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565100"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Posizioni dei partner e degli hub virtuali della rete WAN virtuale di Azure

Questo articolo fornisce informazioni sulle aree e i partner supportati da WAN virtuali per la connettività all'hub virtuale.

La rete WAN virtuale di Azure è un servizio di rete che offre connettività tra succursali ottimizzata e automatizzata tramite Azure. La rete WAN virtuale consente di connettere e configurare i dispositivi delle succursali per la comunicazione con Azure. Questa operazione può essere eseguita manualmente o usando i dispositivi del provider tramite un partner WAN virtuale. L'uso di dispositivi partner consente di semplificare l'uso, la semplificazione della connettività e la gestione della configurazione.

La connettività dal dispositivo locale viene stabilita in modo automatico per l'hub virtuale. Un hub virtuale è una rete virtuale gestita da Microsoft. L'hub contiene vari endpoint di servizio per abilitare la connettività dalla rete locale (vpnsite). È possibile avere solo un hub per area.

## <a name="automation-from-connectivity-partners"></a><a name="automation"></a>Automazione dai partner di connettività

I dispositivi che si connettono alla rete WAN virtuale di Azure dispongono di un'automazione incorporata per la connessione. Questa è in genere impostata nell'interfaccia utente di gestione del dispositivo (o equivalente), che consente di impostare la gestione della configurazione e della connettività tra il dispositivo VPN della succursale e un endpoint VPN dell'hub virtuale di Azure (gateway VPN).

L'automazione di alto livello seguente è impostata nella console del dispositivo/centro di gestione:

* Autorizzazioni appropriate per il dispositivo per accedere al gruppo di risorse della rete WAN virtuale di Azure
* Caricamento del dispositivo della succursale nella rete WAN virtuale di Azure
* Download automatico di informazioni relative alla connettività di Azure
* Configurazione del dispositivo della succursale locale 

Alcuni partner di connettività possono estendere l'automazione in modo da includere la creazione della rete virtuale dell'hub virtuale di Azure e di un Gateway VPN. Per altre informazioni sull'automazione, vedere [linee guida di automazione per i partner WAN virtuali](virtual-wan-configure-automation-providers.md).

## <a name="connectivity-through-partners"></a><a name="partners"></a>Connettività tramite partner

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

I partner seguenti sono disponibili nella roadmap per il prossimo futuro: 128 Technologies, Arista, Aruba HPE, Cisco Systems (Viptela), F5 Networks, Open Systems, Oracle SD-WAN e SharpLink.

## <a name="locations"></a><a name="locations"></a>Località

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti sulla rete WAN virtuale](virtual-wan-faq.md).

* Per altre informazioni su come automatizzare la connettività alla rete WAN virtuale di Azure, vedere [linee guida di automazione per i partner WAN virtuali](virtual-wan-configure-automation-providers.md).
