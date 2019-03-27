---
title: Panoramica della rete WAN virtuale di Azure | Microsoft Docs
description: Informazioni sulla connettività scalabile da ramo a ramo, sulle aree disponibili e sui partner.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 03/20/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 6f2f002c5ff08e21741927d07a0facfd09ec0914
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295692"
---
# <a name="what-is-azure-virtual-wan"></a>Che cos'è la rete WAN virtuale di Azure?

La rete WAN virtuale di Azure è un servizio di rete che offre connettività tra rami ottimizzata e automatizzata verso e tramite Azure. Le aree di Azure fungono da hub a cui è possibile scegliere di connettere i rami. Dopo aver connesso i rami, è possibile sfruttare il backbone di Azure per stabilire la connettività da ramo a rete virtuale e da ramo a ramo. Per un elenco di partner e località che supportano VPN di WAN virtuali, vedere l'articolo [Partner e località della rete WAN virtuale](virtual-wan-locations-partners.md).

La rete WAN virtuale di Azure raggruppa in una singola interfaccia operativa numerosi servizi di connettività cloud di Azure, ad esempio VPN da sito a sito (disponibile a livello generale), ExpressRoute (anteprima), VPN utente da punto a sito (anteprima). La connettività con le reti virtuali di Azure viene stabilita tramite connessioni di rete virtuali.

![Diagramma della rete WAN virtuale](./media/virtual-wan-about/vwangraphic.png)

Questo articolo offre una panoramica rapida della connettività di rete nella rete WAN virtuale di Azure. La rete WAN virtuale offre i vantaggi seguenti:

* **Soluzioni di connettività integrate in un modello hub-spoke:** automatizzano la configurazione da sito a sito e la connettività tra siti locali e un hub di Azure.
* **Impostazione e configurazione automatizzate dello spoke:** consentono di connettere reti virtuali e carichi di lavoro all'hub di Azure senza interruzioni.
* **Risoluzione dei problemi intuitiva:** è possibile visualizzare l'intero flusso all'interno di Azure e quindi usare queste informazioni per intraprendere le azioni necessarie.

## <a name="resources"></a>Risorse della rete WAN virtuale

Per configurare una rete WAN virtuale end-to-end, creare le risorse seguenti:

* **Rete WAN virtuale:** la risorsa rete WAN virtuale rappresenta una sovrapposizione virtuale della rete di Azure ed è una raccolta di più risorse. Sono inclusi collegamenti a tutti gli hub virtuali che si desidera avere all'interno della rete WAN virtuale. Le risorse della rete WAN virtuale vengono isolate l'una dall'altra e non possono contenere un hub comune. Gli hub virtuali nella rete WAN virtuale non comunicano tra loro. La proprietà 'Consenti traffico da ramo a ramo' consente il traffico tra siti VPN nonché tra siti con supporto da VPN a ExpressRoute (attualmente in anteprima).

* **Hub**: Un hub virtuale è una rete virtuale gestita da Microsoft. L'hub contiene vari endpoint di servizio per abilitare la connettività dalla rete locale (vpnsite). L'hub è l'elemento centrale della rete in un'area. Può esistere solo un unico hub per ogni area di Azure. Quando si crea un hub usando il portale di Azure, viene creata una rete virtuale dell'hub virtuale e un gateway VPN dell'hub virtuale.

  Un gateway dell'hub non equivale a un gateway di rete virtuale, usato per ExpressRoute e Gateway VPN. Quando si usa una rete WAN virtuale, ad esempio, non si crea una connessione da sito a sito dal sito locale direttamente alla rete virtuale, ma al contrario una connessione da sito a sito all'hub. Il traffico passa sempre attraverso il gateway dell'hub. Questo significa che le reti virtuali non hanno bisogno di un gateway di rete virtuale proprio. La rete WAN virtuale consente alle reti virtuali di sfruttare i vantaggi di una facile scalabilità tramite l'hub virtuale e il gateway di hub virtuale.

* **Connessione di rete virtuale e hub:** questa risorsa viene usata per connettere facilmente l'hub alla rete virtuale. Al momento è possibile connettersi solo alle reti virtuali all'interno della stessa area dell'hub.

* **Tabella di route dell'hub:**  è possibile creare una route dell'hub virtuale e applicare la route alla tabella della route dell'hub virtuale. È possibile applicare più route alla tabella di route dell'hub virtuale.

**Altre risorse della rete WAN virtuale**

  * **Sito:** questa risorsa si usa solo per le connessioni da sito a sito. La risorsa sito è **vpnsite**. Rappresenta il dispositivo VPN locale e le relative impostazioni. Se si collabora con un partner di rete WAN virtuale, si avrà a disposizione una soluzione predefinita per esportare automaticamente queste informazioni in Azure.

## <a name="connectivity"></a>Connettività

La rete WAN virtuale consente tre tipi di connettività: da sito a sito, da punto a sito (anteprima) ed ExpressRoute (anteprima).

### <a name="s2s"></a>Connessioni VPN da sito a sito

![Diagramma della rete WAN virtuale](./media/virtual-wan-about/virtualwan.png)

Per creare una connessione della rete WAN virtuale da sito a sito, è possibile collaborare con un partner disponibile. Se non si vuole coinvolgere un partner, è possibile configurare la connessione manualmente. Per altre informazioni, vedere [Creare una connessione da sito a sito con la rete WAN virtuale](virtual-wan-site-to-site-portal.md).

#### <a name="s2spartner"></a>Flusso di lavoro del partner della rete WAN virtuale

Se si collabora con un partner della rete WAN virtuale, il flusso di lavoro è il seguente:

1. Il controller (VPN/SDWAN) del dispositivo della succursale viene autenticato per esportare le informazioni specifiche del sito in [Azure usando un'entità servizio di Azure](../active-directory/develop/howto-create-service-principal-portal.md).
2. Il controller (VPN/SDWAN) del dispositivo della succursale ottiene la configurazione della connettività di Azure e aggiorna il dispositivo locale. Ciò consente di automatizzare il download della configurazione, la modifica e l'aggiornamento del dispositivo VPN locale.
3. Quando il dispositivo ha la configurazione di Azure corretta, viene stabilita una connessione da sito a sito (due tunnel attivi) alla rete WAN di Azure. Azure supporta IKEv1 e IKEv2. BGP è facoltativo.

#### <a name="partners"></a>Partner per le connessioni di rete WAN virtuale da sito a sito

Per un elenco di partner e località disponibili, vedere l'articolo [Partner e località della rete WAN virtuale](virtual-wan-locations-partners.md).

### <a name="p2s"></a>Connessioni VPN da punto a sito (anteprima)

Una connessione da punto a sito (P2S) consente di creare una connessione sicura all'hub virtuale da un singolo computer client. Una connessione da punto a sito viene stabilita avviandola dal computer client. Questa soluzione è utile per i telelavoratori che vogliono connettersi da una posizione remota, ad esempio da casa o durante una riunione. Una VPN da punto a sito è anche una soluzione utile da usare al posto di una VPN da sito a sito quando devono connettersi solo pochi client.

Per creare la connessione, vedere [Creare una connessione da punto a sito tramite la rete WAN virtuale](virtual-wan-point-to-site-portal.md).

### <a name="er"></a>Connessioni ExpressRoute (anteprima)

ExpressRoute consente di connettere la rete locale ad Azure tramite una connessione privata. Per creare la connessione, vedere [Creare una connessione ExpressRoute tramite la rete WAN virtuale](virtual-wan-expressroute-portal.md).

## <a name="locations"></a>Località

Per informazioni sulle località, vedere l'articolo [Partner e località della rete WAN virtuale](virtual-wan-locations-partners.md).

## <a name="faq"></a>Domande frequenti

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Passaggi successivi

[Creare una connessione da sito a sito tramite la rete WAN virtuale](virtual-wan-site-to-site-portal.md)
