---
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2019
ms.author: mbaldwin
ms.openlocfilehash: c4c9476f4b29e004fba4bc5f754d6dbfa1f3a195
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444244"
---
## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia | Sì | Vedere [come crittografare una macchina virtuale Linux in Azure](/azure/virtual-machines/linux/encrypt-disks) e [crittografare i dischi virtuali in una VM Windows](/azure/virtual-machines/windows/encrypt-disks). |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | Macchine virtuali di Azure supporta la crittografia [ExpressRoute](/azure/expressroute) e VNet. Vedere [crittografia in transito nelle VM](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| Sì | Chiavi gestite dal cliente è uno scenario di crittografia di Azure supportato. vedere [Panoramica di crittografia di Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Sì | Tramite HTTPS e SSL. |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Sì | |
| Supporto di VNet Injection| Sì | . |
| Isolamento rete e supporto del firewall| Sì |  |
| Supporto del tunneling forzato| Sì | Vedere [configurare il tunneling forzato usando il modello di distribuzione Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Vedere [monitorare e aggiornare una macchina virtuale Linux in Azure](/azure/virtual-machines/linux/tutorial-monitoring) e [monitorare e aggiornare una macchina virtuale Windows in Azure](/azure/virtual-machines/windows/tutorial-monitoring). |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes |  |
| Authorization| Sì |  |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Sì |  |
| Registrazione e controllo del piano dati | No |  |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Yes |  | 
