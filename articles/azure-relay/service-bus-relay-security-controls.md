---
title: Controlli di sicurezza per il relay di Azure
description: Questo articolo fornisce un elenco di controllo dei controlli di sicurezza predefiniti per la valutazione del relay di Azure.
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: f8165d994e998af4f15cd6aa2fd08b75191b8b64
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211461"
---
# <a name="security-controls-for-azure-relay"></a>Controlli di sicurezza per il relay di Azure

Questo articolo descrive i controlli di sicurezza incorporati nel servizio di inoltro di Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|---|---|--|--|
| Supporto per endpoint di servizio| No |  |   |
| Isolamento rete e supporto del firewall| No |  |   |
| Supporto del tunneling forzato| N/D | L'inoltro è il tunnel TLS  |   |

## <a name="monitoring--logging"></a>Monitoraggio & registrazione

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | |   |
| Registrazione e controllo del piano di gestione e controllo| Sì | Tramite [Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| Registrazione e controllo del piano dati| Sì | Connessione riuscita/non riuscita ed errori e registrato.  |   |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Authentication| Sì | Tramite SAS. | [Autenticazione e autorizzazione di Inoltro di Azure](relay-authentication-and-authorization.md) |
| Autorizzazione|  Sì | Tramite SAS. | [Autenticazione e autorizzazione di Inoltro di Azure](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|---|---|--|--|
| Crittografia lato server: chiavi gestite da Microsoft |  N/D | L'inoltro è un socket Web e non rende persistenti i dati. |   |
| Crittografia lato server inattiva: chiavi gestite dal cliente (BYOK) | No | USA solo certificati TLS Microsoft.  |   |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |   |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | Il servizio richiede TLS. |   |
| Chiamate API crittografate| Sì | HTTPS. |


## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Tramite [Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).