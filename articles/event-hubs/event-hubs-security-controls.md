---
title: Controlli di sicurezza per hub eventi di Azure
description: Elenco di controllo dei controlli di sicurezza per la valutazione di hub eventi di Azure
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: ae357d25a37e188ed043aaa0ca750bb0e52d58da
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903537"
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
