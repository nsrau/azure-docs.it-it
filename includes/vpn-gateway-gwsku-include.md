---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 05dc8ae48a9164e4f7118d378ab0eb7c30a4249e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2018
ms.locfileid: "30196857"
---
Quando si crea un gateway di rete virtuale è necessario specificare il codice SKU del gateway da usare. Selezionare lo SKU che soddisfa i requisiti relativi a tipi di carichi di lavoro, velocità effettive, funzionalità e contratti di servizio.

###  <a name="benchmark"></a>SKU del gateway per tunnel, connessione e velocità effettiva

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

###  <a name="feature"></a>SKU del gateway per set di funzionalità

I nuovi SKU del gateway VPN semplificano i set di funzionalità offerti nei gateway:

| **SKU**| **Funzionalità**|
| ---    | ---         |
|**Basic** (\*\*)   | **VPN basata su route**: 10 tunnel con P2S (da punto a sito); assenza di autenticazione RADIUS per P2S; assenza di IKEv2 per P2S<br>**VPN basata su criteri (IKEv1)**: 1 tunnel, nessuna connessione P2S|
| **VpnGw1, VpnGw2 e VpnGw3** | **VPN basata su route**: fino a 30 tunnel (\*), P2S, BGP, attivo-attivo, IPsec personalizzato/criteri IKE, coesistenza ExpressRoute/VPN |
|        |             |

( * ) È possibile configurare "PolicyBasedTrafficSelectors" per la connessione di un gateway VPN basato su route (VpnGw1, VpnGw2, VpnGw3) a più dispositivi firewall locali basati su criteri. Per informazioni dettagliate, vedere [Connect VPN gateways to multiple on-premises policy-based VPN devices using PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) (Connettere gateway VPN a più dispositivi VPN basati su criteri tramite PowerShell).

(\*\*) Lo SKU Basic viene considerato uno SKU legacy. Lo SKU Basic presenta alcune limitazioni in termini di funzionalità. Non è possibile ridimensionare un gateway che utilizza uno SKU Basic a uno dei nuovi SKU del gateway. È invece necessario passare a un nuovo SKU, operazione che comporta l'eliminazione e la ricreazione del gateway VPN.

###  <a name="workloads"></a>SKU del gateway - Carichi di lavoro di produzione e carichi di lavoro di sviluppo e test

Date le differenze nei Contratti di servizio e nei set di funzionalità, è consigliabile utilizzare i seguenti SKU per i carichi di lavoro di produzione e quelli di sviluppo e test:

| **Carico di lavoro**                       | **SKU**               |
| ---                                | ---                    |
| **Carichi di lavoro critici, di produzione** | VpnGw1, VpnGw2, VpnGw3 |
| **Sviluppo e test o modello di verifica**   | Basic (\*\*)                 |
|                                    |                        |

(\*\*) Lo SKU Basic viene considerato uno SKU legacy e presenta alcune limitazioni in termini di funzionalità. Verificare che la funzionalità necessaria sia supportata prima di utilizzare lo SKU Basic.

Se si usano SKU legacy, per quelli di produzione è consigliabile utilizzare gli SKU Standard e HighPerformance. Per informazioni sugli SKU legacy, vedere [SKU del gateway (legacy)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).