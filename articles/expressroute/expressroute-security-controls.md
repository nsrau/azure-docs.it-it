---
title: Controlli di sicurezza per Azure ExpressRoute
description: Elenco di controllo dei controlli di sicurezza per la valutazione di Azure ExpressRoute
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ab73ba6dd4b78d3cd0be5f4c7f7a502e5c58e08
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886693"
---
# <a name="security-controls-for-azure-expressroute"></a>Controlli di sicurezza per Azure ExpressRoute

Questo articolo descrive i controlli di sicurezza incorporati in Azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| N/D |  |
| Supporto di VNet Injection| N/D | |
| Isolamento rete e supporto del firewall| Yes | Ogni cliente è contenuto nel proprio dominio di routing ed è sottopercorso al tunneling per il proprio VNet |
| Supporto del tunneling forzato| N/D | Tramite Border Gateway Protocol (BGP). |

## <a name="monitoring--logging"></a>Monitoraggio & registrazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Vedere [monitoraggio, metriche e avvisi di ExpressRoute](expressroute-monitoring-metrics-alerts.md).|
| Registrazione e controllo del piano di gestione e controllo| Yes |  |
| Registrazione e controllo del piano dati| No |   |

## <a name="identity"></a>identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Account del servizio per il gateway per Microsoft (GWM) (controller); Accesso just-in-time (JIT) per dev e OP. |
| Authorization|  Sì |Account del servizio per il gateway per Microsoft (GWM) (controller); Accesso just-in-time (JIT) per dev e OP. |

## <a name="data-protection"></a>Protezione dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server: Chiavi gestite da Microsoft |  N/D | ExpressRoute non archivia i dati dei clienti. |
| Crittografia lato server inattiva: chiavi gestite dal cliente (BYOK) | N/D |  |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| No | |
| Chiamate API crittografate| Sì | Tramite [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |


## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Tramite il provider di risorse di rete (NRP). |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).