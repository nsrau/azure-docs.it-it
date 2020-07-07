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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82127863"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Controlli di sicurezza per il gateway VPN di Azure

Questo articolo descrive i controlli di sicurezza incorporati nel gateway VPN di Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint di servizio| N/D | |
| Supporto aggiunta rete virtuale| N/D | |
| Supporto isolamento rete e protezione tramite firewall| Sì | I gateway VPN sono istanze di VM dedicate per ogni rete virtuale dei clienti  |
| Supporto del tunneling forzato| Sì |  |

## <a name="monitoring--logging"></a>Monitoraggio e registrazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto monitoraggio di Azure (analisi dei log, analisi approfondita dell'app e così via)| Sì | Vedere avviso [log di monitoraggio](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)di Azure-  &  [avviso metriche di monitoraggio di Azure](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |
| Piano di gestione e controllo - Registrazione e controllo| Sì | Azure Resource Manager log attività. |
| Piano dati - Registrazione e controllo | Sì | [Log attività di monitoraggio di Azure](../azure-resource-manager/management/view-activity-logs.md) per la registrazione e il controllo della connettività VPN. |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) per la gestione del servizio e la configurazione del gateway VPN di Azure. |
| Autorizzazione| Sì | Supportare l'autorizzazione tramite [RBAC](../role-based-access-control/overview.md). |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server dei dati inattivi: chiavi gestite da Microsoft | N/D | I dati dei clienti di transito del gateway VPN non archiviano i dati dei clienti |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | Il gateway VPN crittografa i pacchetti del cliente tra i gateway VPN di Azure e i dispositivi VPN locali dei clienti (S2S) o i client VPN (P2S). I gateway VPN supportano anche la crittografia da VNet a VNet. |
| Crittografia lato server dei dati inattivi: chiavi gestite dal cliente (BYOK) | No | Le chiavi precondivise specificate dal cliente sono crittografate a riposo; ma non è ancora stato integrato con CMK. |
| Crittografia a livello di colonna (Servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Sì | Tramite [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS  |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto gestione della configurazione (controllo delle versioni di configurazione e così via)| Sì | Per le operazioni di gestione, lo stato di una configurazione del gateway VPN di Azure può essere esportato come modello di Azure Resource Manager e con controllo delle versioni nel tempo. |

## <a name="next-steps"></a>Passaggi successivi

- Maggiori informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).
