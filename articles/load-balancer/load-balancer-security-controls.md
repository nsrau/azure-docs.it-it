---
title: Controlli di sicurezza per Azure Load Balancer
description: Elenco di controllo dei controlli di sicurezza per la valutazione Load Balancer
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74214891"
---
# <a name="security-controls-for-azure-load-balancer"></a>Controlli di sicurezza per Azure Load Balancer

Questo articolo descrive i controlli di sicurezza incorporati in Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network

| Controllo di sicurezza | Sì/No | note |
|---|---|--|
| Supporto per endpoint di servizio| N/D | |
| Supporto di VNet Injection| N/D | |
| Isolamento rete e supporto del firewall| N/D |  |
| Supporto del tunneling forzato| N/D | |

## <a name="monitoring--logging"></a>Monitoraggio & registrazione

| Controllo di sicurezza | Sì/No | note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Vedere [log di monitoraggio di Azure per Load Balancer di base pubblici](load-balancer-monitor-log.md). |
| Registrazione e controllo del piano di gestione e controllo| Sì | Vedere [log di monitoraggio di Azure per Load Balancer di base pubblici](load-balancer-monitor-log.md). |
| Registrazione e controllo del piano dati | N/D |  |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | note|
|---|---|--|
| Autenticazione| N/D |  |
| Autorizzazione| N/D |  |

## <a name="data-protection"></a>Protezione dati

| Controllo di sicurezza | Sì/No | note |
|---|---|--|
| Crittografia lato server: chiavi gestite da Microsoft | N/D | |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| N/D | |
| Crittografia lato server inattiva: chiavi gestite dal cliente (BYOK) | N/D | |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Sì | Tramite il [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| N/D |  | 

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).