---
title: Controlli di sicurezza per il gateway VPN di Azure
description: Elenco di controllo dei controlli di sicurezza per la valutazione del gateway VPN di Azure
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 6fc5b4c901254decdb2d34281a10ababd4d79d45
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82127863"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Controlli di sicurezza per il gateway VPN di Azure

Questo articolo descrive i controlli di sicurezza incorporati nel gateway VPN di Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| N/D | |
| Supporto di VNet Injection| N/D | |
| Isolamento rete e supporto del firewall| Sì | I gateway VPN sono istanze di VM dedicate per ogni rete virtuale dei clienti  |
| Supporto del tunneling forzato| Sì |  |

## <a name="monitoring--logging"></a>Monitoraggio & registrazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Vedere avviso [log](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & di monitoraggio di Azure-[avviso metriche di monitoraggio di Azure](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |
| Registrazione e controllo del piano di gestione e controllo| Sì | Azure Resource Manager log attività. |
| Registrazione e controllo del piano dati | Sì | [Log attività di monitoraggio di Azure](../azure-resource-manager/management/view-activity-logs.md) per la registrazione e il controllo della connettività VPN. |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Autenticazione| Sì | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) per la gestione del servizio e la configurazione del gateway VPN di Azure. |
| Autorizzazione| Sì | Supportare l'autorizzazione tramite [RBAC](../role-based-access-control/overview.md). |

## <a name="data-protection"></a>Protezione dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server: chiavi gestite da Microsoft | N/D | I dati dei clienti di transito del gateway VPN non archiviano i dati dei clienti |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | Il gateway VPN crittografa i pacchetti del cliente tra i gateway VPN di Azure e i dispositivi VPN locali dei clienti (S2S) o i client VPN (P2S). I gateway VPN supportano anche la crittografia da VNet a VNet. |
| Crittografia lato server inattiva: chiavi gestite dal cliente (BYOK) | No | Le chiavi precondivise specificate dal cliente sono crittografate a riposo; ma non è ancora stato integrato con CMK. |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Sì | Tramite [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS  |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Per le operazioni di gestione, lo stato di una configurazione del gateway VPN di Azure può essere esportato come modello di Azure Resource Manager e con controllo delle versioni nel tempo. |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).
