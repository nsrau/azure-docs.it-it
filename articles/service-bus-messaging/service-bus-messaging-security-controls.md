---
title: Controlli di sicurezza per la messaggistica del bus di servizio di Azure
description: Elenco di controllo dei controlli di sicurezza per la valutazione della messaggistica del bus di servizio di Azure
services: service-bus-messaging
ms.service: service-bus-messaging
author: axisc
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: aschhab
ms.openlocfilehash: ac931ac7cf6c8609019b120388e1b6ca836f72b8
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886694"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Controlli di sicurezza per la messaggistica del bus di servizio di Azure

Questo articolo descrive i controlli di sicurezza incorporati nella messaggistica del bus di servizio di Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Sì (solo livello Premium) | Gli endpoint del servizio VNet sono supportati solo per il [livello Premium del bus di servizio](service-bus-premium-messaging.md) . |
| Supporto di VNet Injection| No | |
| Isolamento rete e supporto del firewall| Sì (solo livello Premium) |  |
| Supporto del tunneling forzato| No |  |

## <a name="monitoring--logging"></a>Monitoraggio & registrazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Supportato tramite [monitoraggio e avvisi di Azure](service-bus-metrics-azure-monitor.md). |
| Registrazione e controllo del piano di gestione e controllo| Sì | I log delle operazioni sono disponibili. vedere [log di diagnostica del bus di servizio](service-bus-diagnostic-logs.md).  |
| Registrazione e controllo del piano dati| No |  |

## <a name="identity"></a>identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | Gestito tramite [Azure Active Directory identità del servizio gestita](service-bus-managed-service-identity.md); vedere [autenticazione e autorizzazione del bus di servizio](service-bus-authentication-and-authorization.md).|
| Authorization| Yes | Supporta l'autorizzazione tramite il controllo [RBAC](authenticate-application.md) e il token SAS. vedere [autenticazione e autorizzazione del bus di servizio](service-bus-authentication-and-authorization.md). |

## <a name="data-protection"></a>Protezione dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server: Chiavi gestite da Microsoft |  Sì per la crittografia lato server per impostazione predefinita. | Chiavi gestite dal cliente e BYOK non sono ancora supportate. La crittografia lato client è responsabilità del client |
| Crittografia lato server inattiva: chiavi gestite dal cliente (BYOK) | No |   |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Yes | Supporta il meccanismo HTTPS/TLS standard. |
| Chiamate API crittografate| Sì | Le chiamate API vengono eseguite tramite [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Supporta il controllo delle versioni del provider di risorse tramite l' [API Azure Resource Manager](/rest/api/resources/).|

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).