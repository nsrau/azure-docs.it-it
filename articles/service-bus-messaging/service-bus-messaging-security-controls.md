---
title: Controlli di sicurezza per la messaggistica del bus di servizio di AzureSecurity controls for Azure Service Bus Messaging
description: Elenco di controllo dei controlli di sicurezza per la valutazione della messaggistica del bus di servizio di Azure
services: service-bus-messaging
ms.service: service-bus-messaging
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: af119ef026b70fcb4a56b4f823d20c0e9eddddc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75903260"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Controlli di sicurezza per la messaggistica del bus di servizio di AzureSecurity controls for Azure Service Bus Messaging

Questo articolo documenta i controlli di sicurezza integrati nella messaggistica del bus di servizio di Azure.This article documents the security controls built into Azure Service Bus Messaging.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|---|---|--|--|
| Supporto dell'endpoint di servizioService endpoint support| Sì (solo livello Premium) | Gli endpoint del servizio VNet sono supportati solo per il livello Service Bus Premium.VNet [service endpoints](service-bus-premium-messaging.md) are supported for Service Bus Premium tier only. |  |
| Supporto per l'inserimento di vNet| No | |  |
| Isolamento della rete e supporto firewall| Sì (solo livello Premium) |  |  |
| Supporto per il tunneling forzato| No |  |  |

## <a name="monitoring--logging"></a>Monitoraggio della registrazione dei &

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Supporto per il monitoraggio di Azure (analisi dei log, informazioni dettagliate sulle app e così via)Azure monitoring support (Log analytics, App insights, etc.)| Sì | Supportato tramite [Monitoraggio e avvisi di Azure](service-bus-metrics-azure-monitor.md). |  |
| Controllo e gestione del controllo e dell'audit degli aerei| Sì | I registri delle operazioni sono disponibili.  | [Log di diagnostica del bus di servizio](service-bus-diagnostic-logs.md) |
| Registrazione e controllo dei piani dati| No |  |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Authentication| Sì | Gestito tramite [l'identità](service-bus-managed-service-identity.md)del servizio gestito di Azure Active Directory .| [Autenticazione e autorizzazione del bus di servizio](service-bus-authentication-and-authorization.md). |
| Autorizzazione| Sì | Supporta l'autorizzazione tramite [controllo degli accessi in base al ruolo](authenticate-application.md) e token di firma di accesso condiviso. | [Autenticazione e autorizzazione del bus di servizio](service-bus-authentication-and-authorization.md). |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|---|---|--|--|
| Crittografia lato server inattivi: chiavi gestite da MicrosoftServer-side encryption at rest: Microsoft-managed keys |  Sì per la crittografia sul lato server-at-pause per impostazione predefinita. |  |  |
| Crittografia lato server inattivi: chiavi gestite dal cliente (BYOK)Server-side encryption at rest: customer-managed keys (BYOK) | Sì. | Una chiave gestita dal cliente in KeyVault di Azure può essere usata per crittografare i dati nello spazio dei nomi del bus di servizio inattivi. | [Configurare le chiavi gestite dal cliente per la crittografia dei dati del bus di servizio di Azure inattivi tramite il portale di AzureConfigure customer-managed keys for encrypting Azure Service Bus data at rest by using the Azure portal](configure-customer-managed-key.md)  |
| Crittografia a livello di colonna (Azure Data Services)Column level encryption (Azure Data Services)| N/D | |   |
| Crittografia in transito (ad esempio la crittografia ExpressRoute, nella crittografia della rete virtuale e crittografia VNet-VNet)Encryption in transit (such as ExpressRoute encryption, in VNet encryption, and VNet-VNet encryption)| Sì | Supporta il meccanismo HTTPS/TLS standard. |   |
| Chiamate API crittografate| Sì | Le chiamate API vengono effettuate tramite [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |   |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Supporta il controllo delle versioni del provider di risorse tramite l'API di [Azure Resource Manager.](/rest/api/resources/)|   |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui controlli di sicurezza incorporati nei servizi di Azure.Learn more about the [built-in security controls across Azure services](../security/fundamentals/security-controls.md).
