---
title: Controlli di sicurezza per i set di scalabilità di macchine virtuali di Azure
description: Un elenco di controllo dei controlli di sicurezza per valutare i set di scalabilità di macchine virtuali di Azure
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: security
ms.date: 09/05/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 4480ad425d9a3953fd5779f99d27b5b6b037e61e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87029417"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Controlli di sicurezza per i set di scalabilità di macchine virtuali di Azure

Questo articolo descrive i controlli di sicurezza incorporati nei set di scalabilità di macchine virtuali di Azure.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint di servizio| Sì | |
| Supporto aggiunta rete virtuale| Sì | |
| Supporto isolamento rete e protezione tramite firewall| Sì |  |
| Supporto del tunneling forzato| Sì | Vedere [Configurare il tunneling forzato tramite il modello di distribuzione Azure Resource Manager](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md). |

## <a name="monitoring--logging"></a>Monitoraggio e registrazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto monitoraggio di Azure (analisi dei log, analisi approfondita dell'app e così via)| Sì | Vedere [Monitorare e aggiornare una macchina virtuale Linux in Azure](../virtual-machines/linux/tutorial-monitor.md) e [Monitorare e aggiornare una macchina virtuale Windows in Azure](../virtual-machines/windows/tutorial-monitor.md). |
| Piano di gestione e controllo - Registrazione e controllo| Sì |  |
| Piano dati - Registrazione e controllo | No |  |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì |  |
| Autorizzazione| Sì |  |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server dei dati inattivi: chiavi gestite da Microsoft | Sì | Vedere [Crittografia dischi di Azure per set di scalabilità di macchine virtuali](disk-encryption-overview.md). |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | Macchine virtuali di Azure supporta la crittografia [ExpressRoute](../expressroute/index.yml) e VNet. Vedere [Crittografia dei dati in transito nelle VM](../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms). |
| Crittografia lato server dei dati inattivi: chiavi gestite dal cliente (BYOK) | Sì | Chiavi gestite dal cliente è uno scenario supportato di crittografia di Azure. Vedere [Crittografia dischi di Azure per set di scalabilità di macchine virtuali](disk-encryption-overview.md)|
| Crittografia a livello di colonna (Servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Sì | Tramite HTTPS e TLS. |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto gestione della configurazione (controllo delle versioni di configurazione e così via)| Sì |  | 

## <a name="next-steps"></a>Passaggi successivi

- Maggiori informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).
