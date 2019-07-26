---
title: Attributi di sicurezza per Azure Load Balancer
description: Elenco di controllo degli attributi di sicurezza per la valutazione Load Balancer
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 7d40618d5f4fde4a2b3fdfbde8a6de0a049ce3b6
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68440893"
---
# <a name="security-attributes-for-azure-load-balancer"></a>Attributi di sicurezza per Azure Load Balancer

Questo articolo descrive gli attributi di sicurezza incorporati in Azure Load Balancer.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia | N/D | |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| N/D | |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| N/D | |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Yes | Tramite il [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| N/D | |
| Supporto di VNet Injection| N/D | . |
| Isolamento rete e supporto del firewall| N/D |  |
| Supporto del tunneling forzato| N/D | |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Vedere [log di monitoraggio di Azure per Load Balancer di base pubblici](load-balancer-monitor-log.md). |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| N/D |  |
| Authorization| N/D |  |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Yes | Vedere [log di monitoraggio di Azure per Load Balancer di base pubblici](load-balancer-monitor-log.md). |
| Registrazione e controllo del piano dati | N/D |  |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| N/D |  | 
