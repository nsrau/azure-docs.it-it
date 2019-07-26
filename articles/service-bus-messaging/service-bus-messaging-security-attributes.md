---
title: Attributi di sicurezza per la messaggistica del bus di servizio di Azure
description: Elenco di controllo degli attributi di sicurezza per la valutazione della messaggistica del bus di servizio di Azure
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: 0e1d6e041b47a261b549fb8b608cf09d0d6362dd
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443888"
---
# <a name="security-attributes-for-azure-service-bus-messaging"></a>Attributi di sicurezza per la messaggistica del bus di servizio di Azure

Questo articolo descrive gli attributi di sicurezza incorporati nella messaggistica del bus di servizio di Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia|  Sì per la crittografia lato server per impostazione predefinita. | Chiavi gestite dal cliente e BYOK non sono ancora supportate. La crittografia lato client è responsabilità del client |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | Supporta il meccanismo HTTPS/TLS standard. |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| No |   |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Sì | Le chiamate API vengono eseguite tramite [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Sì (solo livello Premium) | Gli endpoint del servizio VNet sono supportati solo per il [livello Premium del bus di servizio](service-bus-premium-messaging.md) . |
| Supporto di VNet Injection| No | |
| Isolamento rete e supporto del firewall| Sì (solo livello Premium) |  |
| Supporto del tunneling forzato| No |  |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Supportato tramite [monitoraggio e avvisi di Azure](service-bus-metrics-azure-monitor.md). |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Gestito tramite [Azure Active Directory identità del servizio gestita](service-bus-managed-service-identity.md); vedere [autenticazione e autorizzazione del bus di servizio](service-bus-authentication-and-authorization.md).|
| Authorization| Sì | Supporta l'autorizzazione tramite il controllo [RBAC](service-bus-role-based-access-control.md) (anteprima) e il token SAS; vedere [autenticazione e autorizzazione del bus di servizio](service-bus-authentication-and-authorization.md). |



## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Sì | I log delle operazioni sono disponibili. vedere [log di diagnostica del bus di servizio](service-bus-diagnostic-logs.md).  |
| Registrazione e controllo del piano dati| No |  |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Supporta il controllo delle versioni del provider di risorse tramite l' [API Azure Resource Manager](/rest/api/resources/).|
