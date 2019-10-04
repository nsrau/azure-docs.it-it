---
title: Controlli di sicurezza per l'inoltro del bus di servizio di Azure
description: Elenco di controllo dei controlli di sicurezza per la valutazione di inoltro del bus di servizio di Azure
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 7f4e2a31673905a7e28d1dbb5520650aefc6f368
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219977"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Controlli di sicurezza per l'inoltro del bus di servizio di Azure

Questo articolo descrive i controlli di sicurezza incorporati nell'inoltro del bus di servizio di Azure.

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
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Yes | |   |
| Registrazione e controllo del piano di gestione e controllo| Yes | Tramite [Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| Registrazione e controllo del piano dati| Sì | Connessione riuscita/non riuscita ed errori e registrato.  |   |

## <a name="identity"></a>identità

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Authentication| Sì | Tramite SAS. | [Autenticazione e autorizzazione di Inoltro di Azure](relay-authentication-and-authorization.md) |
| Authorization|  Sì | Tramite SAS. | [Autenticazione e autorizzazione di Inoltro di Azure](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|---|---|--|--|
| Crittografia lato server: Chiavi gestite da Microsoft |  N/D | L'inoltro è un socket Web e non rende persistenti i dati. |   |
| Crittografia lato server inattiva: chiavi gestite dal cliente (BYOK) | No | USA solo certificati TLS Microsoft.  |   |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |   |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Yes | Il servizio richiede TLS. |   |
| Chiamate API crittografate| Sì | HTTPS. |


## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Yes | Tramite [Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).