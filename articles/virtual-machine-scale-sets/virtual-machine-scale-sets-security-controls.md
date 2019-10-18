---
title: Controlli di sicurezza per i set di scalabilità di macchine virtuali di Azure
description: Elenco di controllo dei controlli di sicurezza per la valutazione dei set di scalabilità di macchine virtuali di Azure
services: virtual-machine-scale-sets
ms.service: virtual-machine-scale-sets
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 6aee9edaf3c594a2e7083416726da8e57bbf5c6b
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529147"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Controlli di sicurezza per i set di scalabilità di macchine virtuali di Azure

Questo articolo descrive i controlli di sicurezza incorporati nei set di scalabilità di macchine virtuali di Azure.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| SÌ | |
| Supporto di VNet Injection| SÌ | |
| Isolamento rete e supporto del firewall| SÌ |  |
| Supporto del tunneling forzato| SÌ | Vedere [configurare il tunneling forzato usando il modello di distribuzione Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>Monitoraggio & registrazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| SÌ | Vedere [monitorare e aggiornare una macchina virtuale Linux in Azure](/azure/virtual-machines/linux/tutorial-monitoring) e [monitorare e aggiornare una macchina virtuale Windows in Azure](/azure/virtual-machines/windows/tutorial-monitoring). |
| Registrazione e controllo del piano di gestione e controllo| SÌ |  |
| Registrazione e controllo del piano dati | No |  |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| SÌ |  |
| Authorization| SÌ |  |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server: chiavi gestite da Microsoft | SÌ | Vedere [crittografia dischi di Azure per i set di scalabilità di macchine virtuali](disk-encryption-overview.md). |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| SÌ | Macchine virtuali di Azure supporta la crittografia [ExpressRoute](/azure/expressroute) e VNet. Vedere [crittografia in transito nelle VM](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Crittografia lato server inattiva: chiavi gestite dal cliente (BYOK) | SÌ | Chiavi gestite dal cliente è uno scenario di crittografia di Azure supportato. vedere vedere [crittografia dischi di Azure per i set di scalabilità di macchine virtuali](disk-encryption-overview.md)|
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Chiamate API crittografate| SÌ | Tramite HTTPS e SSL. |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| SÌ |  | 

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).