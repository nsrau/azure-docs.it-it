---
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2019
ms.author: mbaldwin
ms.openlocfilehash: df11493fa9663d3fcbf0a2f74a5acbead55a25fb
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800276"
---
## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei dati inattivi (ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia) | Sì | Visualizzare [come crittografare una macchina virtuale Linux in Azure](/azure/virtual-machines/linux/encrypt-disks.md) e [crittografare i dischi virtuali in una macchina virtuale Windows](/azure/virtual-machines/windows/encrypt-disks.md). |
| Crittografia in transito (ad esempio la crittografia di ExpressRoute, nella crittografia di rete virtuale e la crittografia di rete)| Yes | Le macchine virtuali di Azure supporta [ExpressRoute](/azure/expressroute) e la crittografia di rete virtuale. Visualizzare [crittografia nelle VM di transito](/azure/security/security-azure-encryption-overview.md#in-transit-encryption-in-vms). |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| Yes | Le chiavi gestite dal cliente è uno scenario di crittografia di Azure supportati; visualizzare [Panoramica di crittografia di Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms.md).|
| Crittografia a livello di colonna (Azure Data Services)| N/D | |
| Chiamate API crittografate| Yes | Tramite HTTPS e SSL. |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| Yes | |
| Supportare l'inserimento di rete virtuale| Sì | . |
| Supporto per l'isolamento della rete e Firewalling| Sì |  |
| Supporto di tunneling forzato| Sì | Visualizzare [configurare il tunneling usando il modello di distribuzione Azure Resource Manager forzato](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm.md). |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Yes | Visualizzare [monitorare e aggiornare una macchina virtuale Linux in Azure](/azure/virtual-machines/linux/tutorial-monitoring.md) e [monitorare e aggiornare una macchina virtuale Windows in Azure](/azure/virtual-machines/windows/tutorial-monitoring.md). |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì |  |
| Authorization| Sì |  |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo| Sì |  |
| Controllo e registrazione di piano dati | No |  |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Yes |  | 
