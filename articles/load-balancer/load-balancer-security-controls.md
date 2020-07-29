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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74214891"
---
# <a name="security-controls-for-azure-load-balancer"></a>Controlli di sicurezza per Azure Load Balancer

Questo articolo descrive i controlli di sicurezza incorporati in Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint di servizio| N/D | |
| Supporto aggiunta rete virtuale| N/D | |
| Supporto isolamento rete e protezione tramite firewall| N/D |  |
| Supporto del tunneling forzato| N/D | |

## <a name="monitoring--logging"></a>Monitoraggio e registrazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto monitoraggio di Azure (analisi dei log, analisi approfondita dell'app e così via)| Sì | Vedere [log di monitoraggio di Azure per Load Balancer di base pubblici](load-balancer-monitor-log.md). |
| Piano di gestione e controllo - Registrazione e controllo| Sì | Vedere [log di monitoraggio di Azure per Load Balancer di base pubblici](load-balancer-monitor-log.md). |
| Piano dati - Registrazione e controllo | N/D |  |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| N/D |  |
| Autorizzazione| N/D |  |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server dei dati inattivi: chiavi gestite da Microsoft | N/D | |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| N/D | |
| Crittografia lato server dei dati inattivi: chiavi gestite dal cliente (BYOK) | N/D | |
| Crittografia a livello di colonna (Servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Sì | Tramite il [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto gestione della configurazione (controllo delle versioni di configurazione e così via)| N/D |  | 

## <a name="next-steps"></a>Passaggi successivi

- Maggiori informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).