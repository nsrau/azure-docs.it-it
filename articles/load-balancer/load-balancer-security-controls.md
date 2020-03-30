---
title: Controlli di sicurezza per Azure Load BalancerSecurity controls for Azure Load Balancer
description: Elenco di controllo dei controlli di sicurezza per la valutazione di Load Balancer
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74214891"
---
# <a name="security-controls-for-azure-load-balancer"></a>Controlli di sicurezza per Azure Load BalancerSecurity controls for Azure Load Balancer

Questo articolo documenta i controlli di sicurezza integrati in Azure Load Balancer.This article documents the security controls built into Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto dell'endpoint di servizioService endpoint support| N/D | |
| Supporto per l'inserimento di vNet| N/D | |
| Supporto per l'isolamento della rete e il firewall| N/D |  |
| Supporto per il tunneling forzato| N/D | |

## <a name="monitoring--logging"></a>Monitoraggio della registrazione dei &

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per il monitoraggio di Azure (analisi dei log, informazioni dettagliate sulle app e così via)Azure monitoring support (Log analytics, App insights, etc.)| Sì | Vedere Log di Monitoraggio di [Azure per Bilanciamento del carico di base pubblico.](load-balancer-monitor-log.md) |
| Controllo e gestione del controllo e dell'audit degli aerei| Sì | Vedere Log di Monitoraggio di [Azure per Bilanciamento del carico di base pubblico.](load-balancer-monitor-log.md) |
| Registrazione e controllo dei piani dati | N/D |  |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| N/D |  |
| Autorizzazione| N/D |  |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server inattivi: chiavi gestite da MicrosoftServer-side encryption at rest: Microsoft-managed keys | N/D | |
| Crittografia in transito (ad esempio crittografia ExpressRoute, nella crittografia della rete virtuale e crittografia VNet-VNet )| N/D | |
| Crittografia lato server inattivi: chiavi gestite dal cliente (BYOK)Server-side encryption at rest: customer-managed keys (BYOK) | N/D | |
| Crittografia a livello di colonna (Azure Data Services)Column level encryption (Azure Data Services)| N/D | |
| Chiamate API crittografate| Sì | Tramite [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| N/D |  | 

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui controlli di sicurezza incorporati nei servizi di Azure.Learn more about the [built-in security controls across Azure services](../security/fundamentals/security-controls.md).