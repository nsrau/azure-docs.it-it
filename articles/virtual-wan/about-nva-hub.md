---
title: "Rete WAN virtuale di Azure: informazioni sull'appliance virtuale di rete nell'hub"
description: In questo articolo vengono illustrate le appliance virtuali di rete nell'hub WAN virtuale.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: scottnap
Customer intent: As someone with a networking background, I want to learn about Network Virtual Appliances in the Virtual WAN hub.
ms.openlocfilehash: 1e4b8a2d801d7d7eccfaf558c3926ead1ab0a953
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91313774"
---
# <a name="about-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Informazioni sull'appliance virtuale di rete in un hub WAN virtuale di Azure (anteprima)

La rete WAN virtuale di Azure ha collaborato con i partner di rete per creare un'automazione che semplifica la connessione delle apparecchiature locali del cliente (CPE) a un gateway VPN di Azure nell'hub virtuale. Azure collabora con i partner di rete selezionati per consentire ai clienti di distribuire un'appliance virtuale di rete di terze parti direttamente nell'hub virtuale. Ciò consente ai clienti che desiderano connettere il proprio ramo CPE alla stessa appliance appliance virtuale di rete nell'hub virtuale, in modo che possano sfruttare le funzionalità di SD-WAN end-to-end proprietarie.

Barracuda Networks è il primo partner a offrire un'offerta di appliance virtuale di rete che può essere distribuita direttamente nell'hub WAN virtuale con il prodotto [Barracuda CLOUDGEN WAN](https://www.barracuda.com/products/cloudgenwan) . Azure sta lavorando con più partner, quindi aspettarsi che vengano seguite altre offerte.

> [!NOTE]
> Solo le offerte di appliance virtuale di rete disponibili per la distribuzione nell'hub WAN virtuale possono essere distribuite nell'hub WAN virtuale. Non possono essere distribuiti in una rete virtuale arbitraria in Azure.

## <a name="how-does-it-work"></a><a name="how"></a>Come funziona?

I appliance virtuali disponibili per essere distribuiti direttamente nell'hub WAN virtuale di Azure sono progettati specificamente per l'uso nell'hub virtuale. L'offerta di appliance virtuale di Azure viene pubblicata in Azure Marketplace come applicazione gestita e i clienti possono distribuire l'offerta direttamente da Azure Marketplace oppure possono distribuire l'offerta dall'hub virtuale tramite il portale di Azure.

:::image type="content" source="./media/about-nva-hub/high-level-process.png" alt-text="Panoramica del processo":::

L'offerta di appliance virtuale di ogni partner avrà un'esperienza e una funzionalità leggermente diverse in base ai requisiti di distribuzione. Tuttavia, esistono alcune cose comuni a tutte le offerte dei partner per l'appliance virtuale di rete nell'hub WAN virtuale.

* Esperienza di applicazione gestita offerta tramite Azure Marketplace.
* Capacità e fatturazione basate sull'unità dell'infrastruttura NVA.
* Metriche di integrità emerse attraverso monitoraggio di Azure.

### <a name="managed-application"></a><a name="managed"></a>Applicazione gestita

Tutte le offerte di appliance virtuale di rete disponibili per la distribuzione nell'hub WAN virtuale avranno un' **applicazione gestita** disponibile in Azure Marketplace. Le applicazioni gestite consentono ai partner di eseguire le operazioni seguenti:

* Creare un'esperienza di distribuzione personalizzata per la propria appliance virtuale di dispositivo.
* Fornire un modello di Gestione risorse specializzato che consenta loro di creare l'appliance virtuale di rete direttamente nell'hub WAN virtuale.
* Fattura i costi di licenza software direttamente o tramite Azure Marketplace.
* Esporre le proprietà personalizzate e i contatori delle risorse.

I partner di appliance virtuale di dispositivi possono creare risorse diverse in base alle esigenze di distribuzione, licenze e gestione della configurazione. Quando un cliente crea un'appliance virtuale di rete nell'hub WAN virtuale, come tutte le applicazioni gestite, verranno creati due gruppi di risorse nella sottoscrizione.

* **Gruppo di risorse del cliente** : contiene un segnaposto per l'applicazione gestita. I partner possono usarlo per esporre le proprietà dei clienti che scelgono qui.
* **Gruppo di risorse gestite** : i clienti non possono configurare o modificare direttamente le risorse in questo gruppo di risorse, perché questo è controllato dall'editore dell'applicazione gestita. Questo gruppo di risorse conterrà la risorsa **NetworkVirtualAppliances** .

:::image type="content" source="./media/about-nva-hub/managed-app.png" alt-text="Panoramica del processo":::

### <a name="nva-infrastructure-units"></a><a name="units"></a>Unità di infrastruttura NVA

Quando si crea un'appliance virtuale di rete nell'hub WAN virtuale, è necessario scegliere il numero di unità di infrastruttura dell'appliance virtuale di rete con cui si vuole eseguire la distribuzione. Un' **unità di infrastruttura** appliance virtuale di rete è un'unità di capacità di larghezza di banda aggregata per un appliance virtuale di rete nell'hub WAN virtuale. Un' **unità dell'infrastruttura** appliance virtuale di rete è simile a un' [unità di scala](pricing-concepts.md#scale-unit) VPN in termini di capacità e dimensionamento.

* 1 l'unità dell'infrastruttura dell'appliance virtuale di sistema rappresenta 500 Mbps di larghezza di banda aggregata per tutte le connessioni del sito di succursale in questa appliance virtuale, a un costo di $0,25/ora
* Azure supporta da 1-80 unità di infrastruttura di appliance virtuale di Azure per una distribuzione dell'hub virtuale di appliance di Azure.
* Ogni partner può offrire bundle di unità di infrastruttura di rete virtuale diversi che sono un subset di tutte le configurazioni di unità di infrastruttura di rete virtuale supportate.

Analogamente alle unità di scala VPN, se si sceglie 1 unità di infrastruttura di rete virtuale di rete *= 500 Mbps*, significa che verranno create due istanze per la ridondanza, ognuna con una velocità effettiva massima di 500 Mbps. Se, ad esempio, si hanno cinque rami, ognuno dei quali contribuisce per 10 Mbps, sarà necessaria una velocità aggregata di 50 Mbps all'estremità finale. La pianificazione della capacità di aggregazione dell'appliance virtuale di sistema deve essere eseguita dopo la valutazione della capacità necessaria per supportare il numero di rami nell'hub.

## <a name="network-virtual-appliance-configuration-process"></a><a name="configuration"></a>Processo di configurazione dell'appliance virtuale di rete

I partner hanno lavorato per offrire un'esperienza che configuri automaticamente l'appliance virtuale di virtuali come parte del processo di distribuzione. Una volta eseguito il provisioning dell'appliance virtuale di sistema nell'hub virtuale, eventuali configurazioni aggiuntive eventualmente necessarie per l'appliance virtuale di sistema devono essere eseguite tramite il portale dei partner di appliance virtuale di gestione o l'applicazione di gestione. L'accesso diretto all'appliance virtuale di dispositivo non è disponibile.

## <a name="site-and-connection-resources-with-nvas"></a><a name="resources"></a>Risorse di connessione e sito con appliance virtuali

Diversamente dalle configurazioni del gateway VPN di Azure, non è necessario creare risorse del **sito** , risorse **di connessione da sito a sito** o risorse di **connessione da punto a sito** per connettere i siti di succursale all'appliance virtuale di rete nell'hub WAN virtuale. Questa operazione viene gestita tramite il partner appliance virtuale di virtualizzazione.

È comunque necessario creare connessioni da Hub a VNet per connettere l'hub WAN virtuale alle reti virtuali di Azure.

## <a name="supported-regions"></a><a name="regions"></a>Aree supportate

Appliance virtuale di virtualizzazione nell'hub virtuale è disponibile per l'anteprima nelle aree seguenti:

|Area geopolitica | Aree di Azure|
|---|---|
| America del Nord| Stati Uniti occidentali, Stati Uniti centro-meridionali, Stati Uniti orientali 2   |
| America del Sud | Brasile meridionale |
| Europa | Europa occidentale, Regno Unito meridionale|
|  Medio Oriente | Emirati Arabi Uniti settentrionali |
| Asia | Giappone orientale |
| Australia | Australia orientale |

## <a name="faq"></a>Domande frequenti

### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub--can-i-join-this-partner-program"></a>Sono un partner di dispositivi di rete e desidero ottenere l'appliance virtuale di rete nell'hub.  È possibile partecipare a questo programma partner?

Sfortunatamente, al momento non è disponibile la capacità di caricare nuove offerte partner. Consultaci a novembre!

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>È possibile distribuire qualsiasi appliance virtuale di rete da Azure Marketplace nell'hub WAN virtuale?

No. Al momento, è possibile distribuire solo [Barracuda CLOUDGEN WAN](https://aka.ms/BarracudaMarketPlaceOffer) nell'hub WAN virtuale.

### <a name="what-is-the-cost-of-the-nva"></a>Qual è il costo dell'appliance virtuale di dispositivo?

È necessario acquistare una licenza per l'appliance di rete virtuale Barracuda CloudGen da Barracuda. Per ulteriori informazioni sulle licenze, vedere [la pagina relativa alla rete WAN CloudGen di Barracuda](https://www.barracuda.com/products/cloudgenwan). Inoltre, si incorreranno anche addebiti da Microsoft per le unità di infrastruttura di appliance virtuale di dispositivo utilizzate e da qualsiasi altra risorsa. Per ulteriori informazioni, vedere [concetti relativi ai prezzi](pricing-concepts.md).

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>È possibile distribuire un'appliance virtuale di dispositivo in un hub di base?

No. È necessario usare un hub standard se si vuole distribuire un'appliance virtuale di dispositivo.

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>È possibile distribuire un'appliance virtuale di dispositivo in un hub sicuro?

Sì. Barracuda CloudGen WAN può essere distribuito in un hub con il firewall di Azure.

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>È possibile connettere qualsiasi dispositivo CPE della filiale a Barracuda CloudGen WAN appliance virtuale di rete nell'hub?

No. Barracuda CloudGen WAN è compatibile solo con i dispositivi di Barracuda CPE. Per altre informazioni sui requisiti della rete WAN CloudGen, vedere [la pagina WAN CloudGen di Barracuda](https://www.barracuda.com/products/cloudgenwan).

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>Quali scenari di routing sono supportati con appliance virtuale di Hub nell'hub?

Tutti gli scenari di routing supportati dalla rete WAN virtuale sono supportati con appliance virtuali nell'hub.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere l'articolo [Panoramica della rete WAN virtuale](virtual-wan-about.md) .