---
title: Panoramica della rete WAN virtuale di Azure | Microsoft Docs
description: Informazioni sulla connettività tra succursali scalabile automatizzata della rete WAN virtuale.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 09/21/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: a21b97f603fc28cdbba8983547ce958a34df0774
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968595"
---
# <a name="what-is-azure-virtual-wan"></a>Che cos'è la rete WAN virtuale di Azure?

La rete WAN virtuale di Azure è un servizio di rete che offre connettività tra succursali ottimizzata e automatizzata tramite Azure. La rete WAN virtuale consente di connettere e configurare i dispositivi delle succursali per la comunicazione con Azure. Questa operazione può essere eseguita manualmente oppure usando i dispositivi di partner preferiti tramite un partner di rete WAN virtuale. Vedere l'articolo [Partner preferiti](https://go.microsoft.com/fwlink/p/?linkid=2019615) per informazioni dettagliate. L'uso di dispositivi di partner preferiti consente la facilità d'uso, semplifica le operazioni di connettività e agevola la gestione della configurazione. Il dashboard predefinito della rete WAN di Azure offre informazioni dettagliate immediate per la risoluzione dei problemi, che consentono di risparmiare tempo e permettono di visualizzare con facilità la connettività su larga scala.

![Diagramma della rete WAN virtuale](./media/virtual-wan-about/virtualwan.png)

Questo articolo offre una panoramica rapida della connettività di rete per i carichi di lavoro di Azure e non di Azure. La rete WAN virtuale offre i vantaggi seguenti:

* **Soluzioni di connettività integrate con una configurazione di tipo hub-spoke:** automatizzare la configurazione da sito a sito e la connettività tra siti locali e un hub di Azure.
* **Installazione e configurazione automatizzate dello spoke:** connettere reti virtuali e carichi di lavoro all'hub di Azure senza problemi.
* **Risoluzione dei problemi intuitiva:** è possibile visualizzare l'interno flusso all'interno di Azure e usare queste informazioni per intraprendere le azioni necessarie.

## <a name="s2s"></a>Connessioni da sito a sito

Per creare una connessione da sito a sito tramite la rete WAN virtuale, è possibile richiedere la collaborazione di un [partner di rete WAN virtuale](https://go.microsoft.com/fwlink/p/?linkid=2019615) oppure creare manualmente la connessione.

### <a name="s2spartner"></a>Collaborazione con un partner di rete WAN virtuale

Durante le operazioni con una rete WAN virtuale partner, il processo è il seguente:

1. Il controller (VPN/SDWAN) del dispositivo della succursale viene autenticato per esportare le informazioni specifiche del sito in [Azure usando un'entità servizio di Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md).
2. Il controller (VPN/SDWAN) del dispositivo della succursale ottiene la configurazione della connettività di Azure e aggiorna il dispositivo locale. Ciò consente di automatizzare il download della configurazione, la modifica e l'aggiornamento del dispositivo VPN locale.
3. Quando il dispositivo ha la configurazione di Azure corretta, viene stabilita una connessione da sito a sito (due tunnel attivi) alla rete WAN di Azure. Azure supporta IKEv1 e IKEv2. BGP è facoltativo.


Se non si desidera usare un partner preferito, è possibile configurare manualmente la connessione, vedere [Creare una connessione da sito a sito tramite la rete WAN virtuale](virtual-wan-site-to-site-portal.md).

## <a name="p2s"></a>Connessioni da punto a sito (anteprima)

Per creare manualmente la connessione, vedere [Creare una connessione da sito a sito tramite la rete WAN virtuale](https://go.microsoft.com/fwlink/p/?linkid=2020051&clcid).

## <a name="er"></a>Connessioni ExpressRoute (anteprima)

Per creare manualmente la connessione, vedere [Create an ExpressRoute connection using Virtual WAN](https://go.microsoft.com/fwlink/p/?linkid=2020148&clcid) (Creare una connessione ExpressRoute tramite la rete WAN virtuale).


## <a name="resources"></a>Risorse della rete WAN virtuale

Per configurare una rete WAN virtuale completa, si creano le risorse seguenti:

* **virtualWAN:** la risorsa virtualWAN rappresenta una sovrapposizione virtuale della rete di Azure ed è una raccolta di più risorse. Sono inclusi collegamenti a tutti gli hub virtuali che si desidera avere all'interno della rete WAN virtuale. Le risorse della rete WAN virtuale vengono isolate l'una dall'altra e non possono contenere un hub comune. Gli hub virtuali nella rete WAN virtuale non comunicano tra loro. La proprietà "Consenti traffico da ramo a ramo" consente il traffico tra siti VPN nonché tra siti con supporto da VPN a ExpressRoute. Tenere presente che ExpressRoute nella rete WAN virtuale di Azure è attualmente in modalità Anteprima.

* **Sito:** la risorsa sito, nota come vpnsite, rappresenta il dispositivo VPN locale e le relative impostazioni. Se si collabora con un partner di rete WAN virtuale, si avrà a disposizione una soluzione predefinita per esportare automaticamente queste informazioni in Azure.

* **Hub:** un hub virtuale è una rete virtuale gestita da Microsoft. L'hub contiene vari endpoint di servizio per abilitare la connettività dalla rete locale (vpnsite). L'hub è l'elemento centrale della rete in un'area. Può esistere solo un unico hub per ogni area di Azure. Quando si crea un hub usando il portale di Azure, viene creata una rete virtuale dell'hub virtuale e un gateway VPN dell'hub virtuale.

  Un gateway dell'hub non equivale a un gateway di rete virtuale, usato per ExpressRoute e Gateway VPN. Quando si usa una rete WAN virtuale, ad esempio, non si crea una connessione da sito a sito dal sito locale direttamente alla rete virtuale. È invece necessario creare una connessione da sito a sito all'hub. Il traffico passa sempre attraverso il gateway dell'hub. Questo significa che le reti virtuali non hanno bisogno di un gateway di rete virtuale proprio. La rete WAN virtuale consente alle reti virtuali di sfruttare i vantaggi di una facile scalabilità tramite l'hub virtuale e il gateway di hub virtuale. 

* **Connessione di rete virtuale hub:** la risorsa connessione di rete virtuale hub viene usata per connettere facilmente l'hub alla rete virtuale. Al momento è possibile connettersi solo alle reti virtuali all'interno della stessa area dell'hub.

* **Tabella della route dell'hub:** è possibile creare una route dell'hub virtuale e applicare la route alla tabella della route dell'hub virtuale. È possibile applicare più route alla tabella di route dell'hub virtuale.

## <a name="faq"></a>Domande frequenti

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]


## <a name="next-steps"></a>Passaggi successivi

* Visualizzare le [posizioni e i partner della rete WAN virtuale di Azure](https://aka.ms/virtualwan)