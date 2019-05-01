---
title: Attributi di sicurezza comuni per la messaggistica del Bus di servizio di Azure
description: Un elenco di controllo di attributi di sicurezza comuni per la valutazione di messaggistica del Bus di servizio di Azure
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: 11977a5aa36b023e468ce6a54862b5138995c417
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "64513524"
---
# <a name="common-security-attributes-for-azure-service-bus-messaging"></a>Attributi di sicurezza comuni per la messaggistica del Bus di servizio di Azure

La sicurezza è integrata in ogni aspetto di un servizio di Azure. Questo articolo sono descritti gli attributi comuni di sicurezza integrati di messaggistica del Bus di servizio di Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>|  Sì per il server-side encryption inattivi per impostazione predefinita. | Chiavi gestite dal cliente e la chiave BYOK non sono ancora supportati. Crittografia lato client è responsabilità del cliente |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Nella crittografia di rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Sì | Supporta il meccanismo HTTPS/TLS standard. |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| No  |   |
| Crittografia a livello di colonna (Azure Data Services)| N/D | |
| Chiamate API crittografate| Sì | Le chiamate API vengono effettuate tramite [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| Sì (solo livello Premium) | Endpoint del servizio rete virtuale sono supportate per [livello Premium del Bus di servizio](service-bus-premium-messaging.md) solo. |
| Supportare l'inserimento di rete virtuale| No  | |
| Isolamento di rete e supporto firewall| Sì (solo livello Premium) |  |
| Supporto di tunneling forzato| No  |  |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Sì | Supportato tramite [avvisi e monitoraggio di Azure](service-bus-metrics-azure-monitor.md). |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Gestito tramite [Azure Active Directory identità del servizio gestito](service-bus-managed-service-identity.md); vedere [autenticazione e autorizzazione Service Bus](service-bus-authentication-and-authorization.md).|
| Authorization| Sì | Supporta l'autorizzazione tramite [RBAC](service-bus-role-based-access-control.md) (anteprima) e il token di firma di accesso condiviso, vedere [autenticazione e autorizzazione Service Bus](service-bus-authentication-and-authorization.md). |



## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo| Sì | I log operazioni sono disponibili; visualizzare [log di diagnostica del Bus di servizio](service-bus-diagnostic-logs.md).  |
| Controllo e registrazione di piano dati| No  |  |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Sì | Supporta il controllo delle versioni del provider di risorse tramite il [API Azure Resource Manager](/rest/api/resources/).|
