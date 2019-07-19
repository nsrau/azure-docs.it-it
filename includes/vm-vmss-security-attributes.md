---
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 642f63966533416be7b86bae362d24cd286706b3
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68306107"
---
## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia | Sì | Vedere [come crittografare una macchina virtuale Linux in Azure](/azure/virtual-machines/linux/encrypt-disks) e [crittografare i dischi virtuali in una VM Windows](/azure/virtual-machines/windows/encrypt-disks). |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Yes | Macchine virtuali di Azure supporta la crittografia [ExpressRoute](/azure/expressroute) e vnet. Vedere [crittografia in transito nelle VM](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| Sì | Chiavi gestite dal cliente è uno scenario di crittografia di Azure supportato. vedere [Panoramica di crittografia di Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Sì | Tramite HTTPS e SSL. |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Yes | |
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
| Authorization| Yes |  |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Sì |  |
| Registrazione e controllo del piano dati | No |  |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Yes |  | 
