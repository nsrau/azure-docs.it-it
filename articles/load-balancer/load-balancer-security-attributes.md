---
title: Attributi di sicurezza per Azure Load Balancer
description: Un elenco di controllo degli attributi di protezione per la valutazione di bilanciamento del carico
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 1892b918ed35221bc73d5070d5a73ecc359aa4c2
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800081"
---
# <a name="security-attributes-for-azure-load-balancer"></a>Attributi di sicurezza per Azure Load Balancer

Questo articolo sono descritti gli attributi comuni di sicurezza integrati di Azure Load Balancer.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei dati inattivi (ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia) | N/D | |
| Crittografia in transito (ad esempio la crittografia di ExpressRoute, nella crittografia di rete virtuale e la crittografia di rete)| N/D | |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| N/D | |
| Crittografia a livello di colonna (Azure Data Services)| N/D | |
| Chiamate API crittografate| Sì | Tramite il [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| N/D | |
| Supportare l'inserimento di rete virtuale| N/D | . |
| Supporto per l'isolamento della rete e Firewalling| N/D |  |
| Supporto di tunneling forzato| N/D | |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Yes | Visualizzare [log di monitoraggio di Azure per Load Balancer Basic pubblico](load-balancer-monitor-log.md). |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| N/D |  |
| Authorization| N/D |  |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo| Yes | Visualizzare [log di monitoraggio di Azure per Load Balancer Basic pubblico](load-balancer-monitor-log.md). |
| Controllo e registrazione di piano dati | N/D |  |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| N/D |  | 
