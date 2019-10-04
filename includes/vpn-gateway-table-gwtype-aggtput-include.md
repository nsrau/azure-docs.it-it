---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/22/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 65c1011e6e005c190d1ae5d51fdd009f66a20956
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70919695"
---
|**SKU**   | **Tunnel S2S/<br>rete virtuale-rete virtuale** | **P2S<br> Connessioni SSTP** | **P2S<br> Connessioni IKEv2/OpenVPN** | **Benchmark<br>velocità effettiva aggregata** | **BGP** | **Zone-redundant** |
|---       | ---        | ---       | ---            | ---       | --- | --- |
|**Basic** | Max. 10    | Max. 128  | Non supportato  | 100 Mbps  | Non supportato| No |
|**VpnGw1**| Max. 30*   | Max. 128  | Max. 250       | 650 Mbps  | Supportato | No |
|**VpnGw2**| Max. 30*   | Max. 128  | Max. 500       | 1 Gbps    | Supportato | No |
|**VpnGw3**| Max. 30*   | Max. 128  | Max. 1000      | 1,25 Gbps | Supportato | No |
|**VpnGw1AZ**| Max. 30*   | Max. 128  | Max. 250       | 650 Mbps  | Supportato | Sì |
|**VpnGw2AZ**| Max. 30*   | Max. 128  | Max. 500       | 1 Gbps    | Supportato | Sì |
|**VpnGw3AZ**| Max. 30*   | Max. 128  | Max. 1000      | 1,25 Gbps | Supportato | Sì |

(*) Usare la [WAN virtuale](../articles/virtual-wan/virtual-wan-about.md) se servono più di 30 tunnel VPN S2S.

* Questi limiti di connessione sono separati. Ad esempio, è possibile avere 128 connessioni SSTP e anche 250 connessioni IKEv2 in uno SKU VpnGw1.

* Le informazioni sui prezzi sono disponibili nella pagina [Prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway) .

* Le informazioni sul contratto di servizio sono disponibili nella pagina [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) (Contratto di servizio).

* VpnGw1, VpnGw2, VpnGw3, VpnGw1AZ, VpnGw2AZ e VpnGw3AZ sono supportati solo per gateway VPN che usano il modello di distribuzione Resource Manager.

* Lo SKU Basic viene considerato uno SKU legacy. Lo SKU Basic presenta alcune limitazioni in termini di funzionalità. Non è possibile ridimensionare un gateway che utilizza uno SKU Basic a uno dei nuovi SKU del gateway. È invece necessario passare a un nuovo SKU, operazione che comporta l'eliminazione e la ricreazione del gateway VPN. Verificare che la funzionalità necessaria sia supportata prima di utilizzare lo SKU Basic.

* Il benchmark della velocità effettiva aggregata si basa sulle misurazioni di più tunnel aggregati tramite un singolo gateway. Il benchmark della velocità effettiva aggregata per un gateway VPN è S2S + P2S combinati. **Se si hanno molte connessioni P2S, si può avere un impatto negativo su una connessione S2S a causa delle limitazioni relative alla velocità effettiva.** Il benchmark della velocità effettiva aggregata non rappresenta una velocità effettiva garantita, perché può variare anche in funzione delle condizioni del traffico Internet e dei comportamenti dell'applicazione.

* Per aiutare i clienti a comprendere le prestazioni relative degli SKU VpnGw, sono stati usati gli strumenti iPerf e CTSTraffic disponibili pubblicamente per misurare le prestazioni. La tabella seguente elenca i risultati dei test delle prestazioni con algoritmi diversi. È possibile notare come si ottengano le prestazioni migliori quando viene usato l'algoritmo GCMAES256 per la crittografia e l'integrità IPsec. Quando viene usato AES256 per la crittografia IPsec e SHA256 per l'integrità, si ottengono prestazioni medie. Quando infine viene usato DES3 per la crittografia IPsec e SHA256 per l'integrità, si ottengono le prestazioni più basse.

|**SKU**   | **Algoritmi<br>usati** | **Velocità effettiva <br>osservata** | **Pacchetti al secondo<br>osservati** |
|---       | ---                 | ---            | ---                    |
|**VpnGw1**| GCMAES256<br>AES256 e SHA256<br>DES3 e SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58.000<br>50.000<br>50.000|
|**VpnGw2**| GCMAES256<br>AES256 e SHA256<br>DES3 e SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90.000<br>80.000<br>55.000|
|**VpnGw3**| GCMAES256<br>AES256 e SHA256<br>DES3 e SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105.000<br>90.000<br>60.000|
|**VpnGw1AZ**| GCMAES256<br>AES256 e SHA256<br>DES3 e SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58.000<br>50.000<br>50.000|
|**VpnGw2AZ**| GCMAES256<br>AES256 e SHA256<br>DES3 e SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90.000<br>80.000<br>55.000|
|**VpnGw3AZ**| GCMAES256<br>AES256 e SHA256<br>DES3 e SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105.000<br>90.000<br>60.000|
