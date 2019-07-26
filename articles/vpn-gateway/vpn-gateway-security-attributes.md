---
title: Attributi di sicurezza per il gateway VPN di Azure
description: Elenco di controllo degli attributi di sicurezza per la valutazione del gateway VPN di Azure
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: c355f70975f441609304a4c9ee2ead75f0e0ce25
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444591"
---
# <a name="security-attributes-for-azure-vpn-gateway"></a>Attributi di sicurezza per il gateway VPN di Azure

Questo articolo descrive gli attributi di sicurezza incorporati nel gateway VPN di Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia | N/D | I dati dei clienti di transito del gateway VPN non archiviano i dati dei clienti |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Yes | Il gateway VPN crittografa i pacchetti del cliente tra i gateway VPN di Azure e i dispositivi VPN locali dei clienti (S2S) o i client VPN (P2S). I gateway VPN supportano anche la crittografia da VNet a VNet. |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| No | Le chiavi precondivise specificate dal cliente sono crittografate a riposo; ma non è ancora stato integrato con CMK. |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Yes | Tramite [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS  |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| N/D | |
| Supporto di VNet Injection| N/D | . |
| Isolamento rete e supporto del firewall| Sì | I gateway VPN sono istanze di VM dedicate per ogni rete virtuale dei clienti  |
| Supporto del tunneling forzato| Sì |  |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Vedere [log di diagnostica di monitoraggio di Azure/avvisi](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & /[metriche di monitoraggio di Azure](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) per la gestione del servizio e la configurazione del gateway VPN di Azure. |
| Authorization| Yes | Supportare l'autorizzazione tramite [RBAC](../role-based-access-control/overview.md). |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Yes | Azure Resource Manager log attività. |
| Registrazione e controllo del piano dati | Yes | [Log di diagnostica di monitoraggio di Azure](../azure-resource-manager/resource-group-audit.md) per la registrazione e il controllo della connettività VPN. |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Yes | Per le operazioni di gestione, lo stato di una configurazione del gateway VPN di Azure può essere esportato come modello di Azure Resource Manager e con controllo delle versioni nel tempo. | 