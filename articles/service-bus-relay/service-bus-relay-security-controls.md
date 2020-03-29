---
title: Controlli di sicurezza per l'inoltro del bus di servizio di AzureSecurity controls for Azure Service Bus Relay
description: In questo articolo viene fornito un elenco di controllo dei controlli di sicurezza incorporati per la valutazione dell'inoltro del bus di servizio di Azure.This articles provides a checklist of built-in security controls for evaluating Azure Service Bus Relay.
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 28d3ba14aa7769ac4f3fc22bd2b5bd7acd30557c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514018"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Controlli di sicurezza per l'inoltro del bus di servizio di AzureSecurity controls for Azure Service Bus Relay

Questo articolo documenta i controlli di sicurezza integrati nell'inoltro del bus di servizio di Azure.This article documents the security controls built into Azure Service Bus Relay.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|---|---|--|--|
| Supporto dell'endpoint di servizioService endpoint support| No |  |   |
| Isolamento della rete e supporto firewall| No |  |   |
| Supporto per il tunneling forzato| N/D | L'inoltro è il tunnel TLS  |   |

## <a name="monitoring--logging"></a>Monitoraggio della registrazione dei &

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Supporto per il monitoraggio di Azure (analisi dei log, informazioni dettagliate sulle app e così via)Azure monitoring support (Log analytics, App insights, etc.)| Sì | |   |
| Controllo e gestione del controllo e dell'audit degli aerei| Sì | Tramite [Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| Registrazione e controllo dei piani dati| Sì | Connessione riuscita/errore ed errori e registrazione.  |   |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Authentication| Sì | Tramite SAS. | [Autenticazione e autorizzazione di Inoltro di Azure](relay-authentication-and-authorization.md) |
| Autorizzazione|  Sì | Tramite SAS. | [Autenticazione e autorizzazione di Inoltro di Azure](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|---|---|--|--|
| Crittografia lato server inattivi: chiavi gestite da MicrosoftServer-side encryption at rest: Microsoft-managed keys |  N/D | L'inoltro è un socket Web e non rende persistenti i dati. |   |
| Crittografia lato server inattivi: chiavi gestite dal cliente (BYOK)Server-side encryption at rest: customer-managed keys (BYOK) | No | Utilizza solo i certificati Microsoft TLS.  |   |
| Crittografia a livello di colonna (Azure Data Services)Column level encryption (Azure Data Services)| N/D | |   |
| Crittografia in transito (ad esempio la crittografia ExpressRoute, nella crittografia della rete virtuale e crittografia VNet-VNet)Encryption in transit (such as ExpressRoute encryption, in VNet encryption, and VNet-VNet encryption)| Sì | Il servizio richiede TLS. |   |
| Chiamate API crittografate| Sì | HTTPS. |


## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Tramite [Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui controlli di sicurezza incorporati nei servizi di Azure.Learn more about the [built-in security controls across Azure services](../security/fundamentals/security-controls.md).