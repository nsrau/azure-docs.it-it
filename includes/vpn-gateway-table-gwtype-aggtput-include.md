---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1643b20c6c157c43e93967cef364e703dbf4478e
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74829008"
---
|**VPN<br>Gateway<br>Generation** |**SKU**   | **Tunnel S2S/<br>rete virtuale-rete virtuale** | **P2S<br> Connessioni SSTP** | **P2S<br> Connessioni IKEv2/OpenVPN** | **Benchmark<br>velocità effettiva aggregata** | **BGP** | **Zone-redundant** |
|---            |---         | ---        | ---       | ---            | ---       | ---       | ---|
|**Generation1**|**Basic**   | Max. 10    | Max. 128  | Non supportato  | 100 Mbps  | Non supportato| No |
|**Generation1**|**VpnGw1**  | Max. 30*   | Max. 128  | Max. 250       | 650 Mbps  | Supportato | No |
|**Generation1**|**VpnGw2**  | Max. 30*   | Max. 128  | Max. 500       | 1 Gbps    | Supportato | No |
|**Generation1**|**VpnGw3**  | Max. 30*   | Max. 128  | Max. 1000      | 1,25 Gbps | Supportato | No |
|**Generation1**|**VpnGw1AZ**| Max. 30*   | Max. 128  | Max. 250       | 650 Mbps  | Supportato | Sì |
|**Generation1**|**VpnGw2AZ**| Max. 30*   | Max. 128  | Max. 500       | 1 Gbps    | Supportato | Sì |
|**Generation1**|**VpnGw3AZ**| Max. 30*   | Max. 128  | Max. 1000      | 1,25 Gbps | Supportato | Sì |
|        |            |            |           |                |           |           |     |
|**Generation2**|**VpnGw2**  | Max. 30*   | Max. 128  | Max. 500       | 1,25 Gbps | Supportato | No |
|**Generation2**|**VpnGw3**  | Max. 30*   | Max. 128  | Max. 1000      | 2,5 Gbps  | Supportato | No |
|**Generation2**|**VpnGw4**  | Max. 30*   | Max. 128  | Max. 5000      | 5 Gbps    | Supportato | No |
|**Generation2**|**VpnGw5**  | Max. 30*   | Max. 128  | Max. 10000      | 10 Gbps   | Supportato | No |
|**Generation2**|**VpnGw2AZ**| Max. 30*   | Max. 128  | Max. 500       | 1,25 Gbps | Supportato | Sì |
|**Generation2**|**VpnGw3AZ**| Max. 30*   | Max. 128  | Max. 1000      | 2,5 Gbps  | Supportato | Sì |
|**Generation2**|**VpnGw4AZ**| Max. 30*   | Max. 128  | Max. 5000      | 5 Gbps    | Supportato | Sì |
|**Generation2**|**VpnGw5AZ**| Max. 30*   | Max. 128  | Max. 10000      | 10 Gbps   | Supportato | Sì |

(*) Usare la [WAN virtuale](../articles/virtual-wan/virtual-wan-about.md) se servono più di 30 tunnel VPN S2S.

* Il ridimensionamento degli SKU VpnGw è consentito all'interno della stessa generazione, eccetto il ridimensionamento dello SKU Basic. Lo SKU Basic è uno SKU legacy e presenta alcune limitazioni in termini di funzionalità. Per passare da Basic a un altro SKU VpnGw, è necessario eliminare il gateway VPN dello SKU Basic e creare un nuovo gateway con la combinazione di generazione e dimensioni dello SKU preferita.

* Questi limiti di connessione sono separati. Ad esempio, è possibile avere 128 connessioni SSTP e anche 250 connessioni IKEv2 in uno SKU VpnGw1.

* Le informazioni sui prezzi sono disponibili nella pagina [Prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway) .

* Le informazioni sul contratto di servizio sono disponibili nella pagina [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) (Contratto di servizio).

* In un singolo tunnel è possibile ottenere una velocità effettiva massima di 1 Gbps. Il benchmark della velocità effettiva aggregata nella tabella precedente si basa sulle misurazioni di più tunnel aggregati tramite un singolo gateway. Il benchmark della velocità effettiva aggregata per un gateway VPN è S2S + P2S combinati. **Se si hanno molte connessioni P2S, si può avere un impatto negativo su una connessione S2S a causa delle limitazioni relative alla velocità effettiva.** Il benchmark della velocità effettiva aggregata non rappresenta una velocità effettiva garantita, perché può variare anche in funzione delle condizioni del traffico Internet e dei comportamenti dell'applicazione.

Per aiutare i clienti a comprendere le prestazioni relative degli SKU che usano algoritmi diversi, sono stati usati gli strumenti iPerf e CTSTraffic disponibili pubblicamente per misurare le prestazioni. La tabella seguente elenca i risultati dei test delle prestazioni per gli SKU VpnGw Generation1. È possibile notare come si ottengano le prestazioni migliori quando viene usato l'algoritmo GCMAES256 per la crittografia e l'integrità IPsec. Quando viene usato AES256 per la crittografia IPsec e SHA256 per l'integrità, si ottengono prestazioni medie. Quando infine viene usato DES3 per la crittografia IPsec e SHA256 per l'integrità, si ottengono le prestazioni più basse.

|**Generazione**|**SKU**   | **Algoritmi<br>usati** | **Velocità effettiva <br>osservata** | **Pacchetti al secondo<br>osservati** |
|---           |---       | ---                 | ---            | ---                    |
|**Generation1**|**VpnGw1**| GCMAES256<br>AES256 e SHA256<br>DES3 e SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58.000<br>50.000<br>50.000|
|**Generation1**|**VpnGw2**| GCMAES256<br>AES256 e SHA256<br>DES3 e SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90.000<br>80.000<br>55.000|
|**Generation1**|**VpnGw3**| GCMAES256<br>AES256 e SHA256<br>DES3 e SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105.000<br>90.000<br>60.000|
|**Generation1**|**VpnGw1AZ**| GCMAES256<br>AES256 e SHA256<br>DES3 e SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58.000<br>50.000<br>50.000|
|**Generation1**|**VpnGw2AZ**| GCMAES256<br>AES256 e SHA256<br>DES3 e SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90.000<br>80.000<br>55.000|
|**Generation1**|**VpnGw3AZ**| GCMAES256<br>AES256 e SHA256<br>DES3 e SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105.000<br>90.000<br>60.000|
