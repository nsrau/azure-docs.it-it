---
title: Informazioni sul gateway VPN di Azure
description: Informazioni sul gateway VPN e su come usarne uno per la connessione a reti virtuali VPN da punto a sito, da rete virtuale a rete virtuale e da sito a sito IPsec/IKE.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: overview
ms.date: 08/27/2020
ms.author: cherylmc
ms.custom: contperfq1
ms.openlocfilehash: 23d8d28a03217b1359462332da736f852cfaf8ea
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89015391"
---
# <a name="what-is-vpn-gateway"></a>Che cos'è un Gateway VPN?

Un gateway VPN è un tipo specifico di gateway di rete virtuale, usato per inviare traffico crittografato tra una rete virtuale di Azure e una posizione locale attraverso la rete Internet pubblica. È possibile usare un gateway VPN anche per inviare traffico crittografato tra le reti virtuali di Azure sulla rete Microsoft. Ogni rete virtuale può avere un solo gateway VPN, ma è possibile creare più connessioni allo stesso gateway VPN. Quando si creano più connessioni allo stesso gateway VPN, tutti i tunnel VPN condividono la larghezza di banda disponibile per il gateway.

## <a name="what-is-a-virtual-network-gateway"></a><a name="whatis"></a>Informazioni sul gateway di rete virtuale

Un gateway di rete virtuale è costituito da due o più macchine virtuali distribuite in una subnet precedentemente creata, denominata *subnet del gateway*. Le macchine virtuali di un gateway di rete virtuale contengono tabelle di routing ed eseguono specifici servizi gateway. Queste macchine virtuali vengono create contestualmente alla creazione del gateway di rete virtuale. Non è possibile configurare direttamente le macchine virtuali che fanno parte del gateway di rete virtuale.

Quando si configura un gateway di rete virtuale, si configura un'impostazione che specifica il tipo di gateway. Il tipo di gateway determina come verrà usato il gateway di rete virtuale e le azioni che dovrà eseguire. Il tipo del gateway 'Vpn' specifica che il tipo di gateway di rete virtuale creato è un 'gateway VPN'. Questo lo distingue da un gateway ExpressRoute, che usa un tipo di gateway diverso. In una rete virtuale possono coesistere due gateway di rete virtuale, un gateway VPN e un gateway ExpressRoute. Per altre informazioni, vedere [Tipi di gateway](vpn-gateway-about-vpn-gateway-settings.md#gwtype).

La creazione di un gateway di rete virtuale può richiedere fino a 45 minuti. Quando si crea un gateway VPN, le macchine virtuali del gateway vengono distribuite nella subnet del gateway e configurate con le impostazioni specificate. Dopo aver creato un gateway VPN, è possibile creare una connessione con tunnel VPN IPsec/IKE tra il gateway VPN creato e un altro gateway VPN (tra reti virtuali) o creare una connessione con tunnel VPN IPsec/IKE cross-premise tra il gateway VPN e un dispositivo VPN locale (da sito a sito). È anche possibile creare una connessione VPN da punto a sito (VPN su OpenVPN, IKEv2 o SSTP), che consente di connettersi alla rete virtuale da una posizione remota, ad esempio in una conferenza o da casa.

## <a name="configuring-a-vpn-gateway"></a><a name="configuring"></a>Configurazione di un gateway VPN

Una connessione gateway VPN si basa su più risorse configurate con impostazioni specifiche. La maggior parte delle risorse può essere configurata separatamente, anche se alcune risorse devono essere configurate in un determinato ordine.

### <a name="design"></a><a name="diagrams"></a>Progettazione

È importante tenere presente che sono disponibili configurazioni diverse per le connessioni del gateway VPN. È necessario determinare la configurazione più adatta alle proprie esigenze. Ad esempio, le connessioni da punto a sito, da sito a sito e coesistenti da sito a sito ed ExpressRoute hanno tutte requisiti di configurazione e istruzioni differenti. Per informazioni sulla progettazione e per visualizzare i diagrammi delle topologie di connessione, vedere [Progettazione](design.md).

### <a name="planning-table"></a><a name="planningtable"></a>Tabella di pianificazione

La tabella seguente può aiutare nella scelta della migliore opzione di connettività per la soluzione.

[!INCLUDE [cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="settings"></a><a name="settings"></a>Impostazioni

Le impostazioni scelte per ogni risorsa sono fondamentali per creare una connessione corretta. Per informazioni sulle singole risorse e le impostazioni per il gateway VPN, vedere [Informazioni sulle impostazioni del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md). L'articolo contiene informazioni su tipi di gateway, SKU dei gateway, tipi di VPN, tipi di connessione, subnet dei gateway, gateway di rete locale e diverse altre impostazioni delle risorse che potrebbero risultare utili.

### <a name="deployment-tools"></a><a name="tools"></a>Strumenti di distribuzione

È possibile iniziare a creare e configurare le risorse usando uno strumento di configurazione, ad esempio il portale di Azure, e successivamente decidere di passare a un altro strumento, ad esempio PowerShell, per configurare risorse aggiuntive o eventualmente modificare quelle esistenti. Attualmente, non è possibile configurare tutte le risorse e le relative impostazioni nel portale di Azure. Le istruzioni riportate negli articoli per ogni topologia di connessione indicano se è necessario usare uno strumento di configurazione specifico.

## <a name="gateway-skus"></a><a name="gwsku"></a>SKU del gateway

Quando si crea un gateway di rete virtuale, specificare lo SKU del gateway da usare. Selezionare lo SKU che soddisfa i requisiti relativi a tipi di carichi di lavoro, velocità effettive, funzionalità e contratti di servizio.

* Per altre informazioni sugli SKU del gateway, tra cui funzionalità supportate, produzione, sviluppo e test e procedure di configurazione, vedere la sezione [SKU del gateway dell'articolo sulle impostazioni del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
* Per informazioni sugli SKU legacy, vedere l'articolo sull'[uso di SKU legacy](vpn-gateway-about-skus-legacy.md).

### <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>SKU del gateway per tunnel, connessione e velocità effettiva

[!INCLUDE [Aggregated throughput by SKU](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="availability-zones"></a><a name="availability"></a>Zone di disponibilità

I gateway VPN possono essere distribuiti nelle zone di disponibilità di Azure. In questo modo, i gateway di rete virtuale ottengono maggiore disponibilità, scalabilità e resilienza. La distribuzione di gateway in zone di disponibilità di Azure separa fisicamente e logicamente i gateway all'interno di un'area e consente, al contempo, di proteggere la connettività di rete locale ad Azure da errori a livello di zona. Vedere [Informazioni sui gateway di rete virtuale con ridondanza della zona in zone di disponibilità di Azure](about-zone-redundant-vnet-gateways.md).

## <a name="pricing"></a><a name="pricing"></a>Prezzi

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

Per informazioni sugli SKU del gateway VPN, vedere [SKU del gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="faq"></a><a name="faq"></a>Domande frequenti

Per le domande frequenti sul gateway VPN, vedere le [Domande frequenti su Gateway VPN](vpn-gateway-vpn-faq.md).

## <a name="whats-new"></a><a name="new"></a>Novità

Sottoscrivere il feed RSS e visualizzare gli aggiornamenti più recenti delle funzionalità di Gateway VPN nella pagina [Aggiornamenti di Azure](https://azure.microsoft.com/updates/?category=networking&query=VPN%20Gateway).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere [Domande frequenti sul gateway VPN](vpn-gateway-vpn-faq.md).
- Vedere [Sottoscrizione e limiti del servizio](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
- Informazioni su alcune altre [funzionalità di rete](../networking/networking-overview.md) chiave di Azure.
