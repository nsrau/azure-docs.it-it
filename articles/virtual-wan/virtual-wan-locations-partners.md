---
title: Posizioni dei partner della rete WAN virtuale di Azure | Microsoft Docs
description: Questo articolo contiene un elenco delle posizioni dei partner e degli hub della rete WAN virtuale di Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: 76542392522ff14642ed7ccc6de8ed543d66513f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984994"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Posizioni dei partner e degli hub virtuali della rete WAN virtuale di Azure

Questo articolo fornisce informazioni sulle aree della rete WAN virtuale supportate e dei partner preferiti per la connettività all'hub virtuale.

La rete WAN virtuale di Azure è un servizio di rete che offre connettività tra succursali ottimizzata e automatizzata tramite Azure. La rete WAN virtuale consente di connettere e configurare i dispositivi delle succursali per la comunicazione con Azure. Questa operazione può essere eseguita manualmente oppure usando i dispositivi di provider preferiti tramite un partner di rete WAN virtuale preferito. L'uso di dispositivi di partner preferiti consente la facilità d'uso, semplifica le operazioni di connettività e agevola la gestione della configurazione. La connettività dal dispositivo locale viene stabilita in modo automatico per l'hub virtuale. Un hub virtuale è una rete virtuale gestita da Microsoft. L'hub contiene vari endpoint di servizio per abilitare la connettività dalla rete locale (vpnsite). È possibile avere solo un hub per area.

## <a name="regions"></a>Regioni

Segue l'elenco delle aree disponibili e supportate:

|Area geopolitica | Aree di Azure|
|---|---|
|America del Nord | Stati Uniti orientali, Stati Uniti occidentali, Stati Uniti orientali 2, Stati Uniti occidentali 2, Stati Uniti centrali, Stati Uniti centro-meridionali, Stati Uniti centro-settentrionali, Stati Uniti centro-occidentali, Canada centrale, Canada orientale |
|America del Sud |Brasile meridionale |
| Europa | Francia centrale, Francia meridionale, Europa settentrionale, Europa occidentale, Regno Unito occidentale, Regno Unito meridionale |
| Asia | Asia orientale, Asia sudorientale |
| Giappone  | Giappone occidentale, Giappone orientale |
| Australia | Australia sudorientale, Australia orientale | 
| Australia Government | Australia centrale, Australia centrale 2 |
| India | India occidentale, India centrale, India Meridionale |
| Corea del Sud | Corea del Sud centrale, Corea del Sud meridionale | 

## <a name="automation-from-connectivity-partners"></a>Automazione dai partner di connettività

Questa sezione descrive i dettagli di alto livello dell'automazione dai provider di connettività.

I dispositivi che si connettono alla rete WAN virtuale di Azure dispongono di un'automazione incorporata per la connessione. Questa è in genere impostata nell'interfaccia utente di gestione del dispositivo (o equivalente), che consente di impostare la gestione della configurazione e della connettività tra il dispositivo VPN della succursale e un endpoint VPN dell'hub virtuale di Azure (gateway VPN).

L'automazione di alto livello seguente è impostata nella console del dispositivo/centro di gestione:

* Autorizzazioni appropriate per il dispositivo per accedere al gruppo di risorse della rete WAN virtuale di Azure
* Caricamento del dispositivo della succursale nella rete WAN virtuale di Azure
* Download automatico di informazioni relative alla connettività di Azure 
* Configurazione del dispositivo della succursale locale 

Alcuni partner di connettività possono estendere l'automazione in modo da includere la creazione della rete virtuale dell'hub virtuale di Azure e di un Gateway VPN. Se si vuole approfondire la conoscenza dell'automazione, vedere [Configurare l'automazione - partner WAN](virtual-wan-configure-automation-providers.md).

## <a name="connectivity-through-preferred-partners"></a>Connettività tramite i partner preferiti

Se il partner del dispositivo della succursale non è elencato nella sezione seguente, contattare il provider di dispositivi della succursale e richiedere di contattare Microsoft inviando un messaggio di posta elettronica a azurevirtualwan@microsoft.com.

Per altre informazioni sui servizi offerti dai partner preferiti, è possibile esaminare i seguenti collegamenti. 

|Partner preferiti|
|---|
|[Barracuda Networks](https://www.barracuda.com/AzurevWAN)|
| [Check Point](https://www.checkpoint.com/solutions/microsoft-azure-virtual-wan/) |
| [Citrix](https://www.citrix.com/global-partners/microsoft/sd-wan-for-azure-virtual-wan.html)|
|[Netfoundry](https://netfoundry.io/solutions/netfoundry-for-microsoft-azure-virtual-wan/)|
|[Palo Alto Networks](https://researchcenter.paloaltonetworks.com/2018/09/azure-vwan-integration/) |
|[Riverbed Technology](https://www.riverbed.com/go/steelconnect-azurewan.html)|
|[128 Technology](https://www.128technology.com/partners/azure) |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti sulla rete WAN virtuale](virtual-wan-faq.md).

Per altre informazioni su come automatizzare la connettività alla rete WAN virtuale di Azure, vedere la sezione relativa ai [partner della rete WAN virtuale - configurate l'automazione](virtual-wan-configure-automation-providers.md).
