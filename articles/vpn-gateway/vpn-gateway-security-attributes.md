---
title: Attributi di sicurezza per il Gateway VPN di Azure
description: Un elenco di controllo degli attributi di protezione per la valutazione di Gateway VPN di Azure
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 0e58e7b3f77d9bb673e300aa60ad07ca9dba5153
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083132"
---
# <a name="security-attributes-for-azure-vpn-gateway"></a>Attributi di sicurezza per il Gateway VPN di Azure

Questo articolo sono descritti gli attributi comuni di sicurezza integrati di Gateway VPN di Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei dati inattivi (ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia) | N/D | I dati dei clienti di transito gateway VPN, non vengono archiviati i dati dei clienti |
| Crittografia in transito (ad esempio la crittografia di ExpressRoute, nella crittografia di rete virtuale e la crittografia di rete)| Yes | Gateway VPN di crittografare i pacchetti dei clienti tra i gateway VPN di Azure e i dispositivi VPN locali dei clienti (S2S) o i client VPN (P2S). I gateway VPN supportano anche la crittografia VNet-to-VNet. |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| No | Le chiavi precondivise specificati dal cliente vengono crittografate a riposo; ma non è integrato con CMK ancora. |
| Crittografia a livello di colonna (Azure Data Services)| N/D | |
| Chiamate API crittografate| Yes | Attraverso [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS  |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| N/D | |
| Supportare l'inserimento di rete virtuale| N/D | . |
| Supporto per l'isolamento della rete e Firewalling| Yes | I gateway VPN sono istanze di macchine Virtuali dedicate per ogni rete virtuale del cliente  |
| Supporto di tunneling forzato| Yes |  |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Yes | Visualizzare [log di diagnostica di monitoraggio di Azure/avviso](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [metriche di monitoraggio di Azure/avviso](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) per la gestione del servizio e la configurazione del gateway VPN di Azure. |
| Authorization| Yes | Supporto di autorizzazione tramite [RBAC](../role-based-access-control/overview.md). |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo| Yes | Log di attività di Azure Resource Manager. |
| Controllo e registrazione di piano dati | Yes | [Log di diagnostica Azure Monitor](../azure-resource-manager/resource-group-audit.md) per la connettività VPN, registrazione e controllo. |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Yes | Per le operazioni di gestione, lo stato di una configurazione del gateway VPN di Azure può essere esportato come un modello di Azure Resource Manager e con controllo delle versioni nel corso del tempo. | 