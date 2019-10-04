---
title: Controlli di sicurezza per hub eventi di Azure
description: Elenco di controllo dei controlli di sicurezza per la valutazione di hub eventi di Azure
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 277a745d26961ed509258d5423fc3c0da9b79a24
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219402"
---
# <a name="security-controls-for-azure-event-hubs"></a>Controlli di sicurezza per hub eventi di Azure

Questo articolo descrive i controlli di sicurezza incorporati in hub eventi di Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|---|---|--|--|
| Supporto per endpoint di servizio| Sì |  |  |
| Supporto di VNet Injection| No | |  |
| Isolamento rete e supporto del firewall| Sì |  |  |
| Supporto del tunneling forzato| No |  |  |

## <a name="monitoring--logging"></a>Monitoraggio & registrazione

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Yes | |  |
| Registrazione e controllo del piano di gestione e controllo| Yes |  |  |
| Registrazione e controllo del piano dati| Yes |   |  |

## <a name="identity"></a>identità

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Authentication| Yes | | [Autorizzare l'accesso a hub eventi di Azure](authorize-access-event-hubs.md), [autorizzare l'accesso alle risorse di hub eventi usando Azure Active Directory](authorize-access-azure-active-directory.md), [autorizzando l'accesso alle risorse di hub eventi usando firme di accesso condiviso](authorize-access-shared-access-signature.md) |
| Authorization|  Yes | | [Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse di hub eventi](authenticate-managed-identity.md), [autenticare un'applicazione con Azure Active Directory per accedere alle risorse di hub eventi](authenticate-application.md), [autenticare l'accesso alle risorse di hub eventi usando firme di accesso condiviso (SAS)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|---|---|--|--|
| Crittografia lato server: Chiavi gestite da Microsoft |  Yes | |  |
| Crittografia lato server inattiva: chiavi gestite dal cliente (BYOK) | No |  |  |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |  |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Yes | |  |
| Chiamate API crittografate| Yes |  |  |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Yes | |  |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).
