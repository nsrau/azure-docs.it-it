---
title: Controlli di sicurezza per hub eventi di Azure
description: Questo articolo fornisce un elenco di controllo dei controlli di sicurezza per la valutazione di hub eventi di Azure (rete, identità, protezione dei dati e così via).
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 0769e88eb72b5b347dd9ebf4b1634501ca54098e
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76309507"
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
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | |  |
| Registrazione e controllo del piano di gestione e controllo| Sì |  |  |
| Registrazione e controllo del piano dati| Sì |   |  |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Autenticazione| Sì | | [Autorizzare l'accesso a hub eventi di Azure](authorize-access-event-hubs.md), [autorizzare l'accesso alle risorse di hub eventi usando Azure Active Directory](authorize-access-azure-active-directory.md), [autorizzando l'accesso alle risorse di hub eventi usando firme di accesso condiviso](authorize-access-shared-access-signature.md) |
| Autorizzazione|  Sì | | [Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse di hub eventi](authenticate-managed-identity.md), [autenticare un'applicazione con Azure Active Directory per accedere alle risorse di hub eventi](authenticate-application.md), [autenticare l'accesso alle risorse di hub eventi usando firme di accesso condiviso (SAS)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|---|---|--|--|
| Crittografia lato server: chiavi gestite da Microsoft |  Sì | |  |
| Crittografia lato server inattiva: chiavi gestite dal cliente (BYOK) | Sì. Disponibile per i cluster dedicati. | Una chiave gestita dal cliente nell'insieme di credenziali delle chiavi di Azure può essere usata per crittografare i dati in un hub eventi inattivi. | [Configurare chiavi gestite dal cliente per la crittografia dei dati inattivi di hub eventi di Azure usando il portale di Azure](configure-customer-managed-key.md) |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |  |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | |  |
| Chiamate API crittografate| Sì |  |  |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | |  |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).
