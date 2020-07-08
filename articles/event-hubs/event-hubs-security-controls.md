---
title: Controlli di sicurezza per hub eventi di Azure
description: Questo articolo fornisce un elenco di controllo dei controlli di sicurezza per la valutazione di hub eventi di Azure (rete, identità, protezione dei dati e così via).
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: da20778f1e24372e445d635e675df6484905f195
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85315396"
---
# <a name="security-controls-for-azure-event-hubs"></a>Controlli di sicurezza per hub eventi di Azure

Questo articolo descrive i controlli di sicurezza incorporati in hub eventi di Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|---|---|--|--|
| Supporto endpoint di servizio| Sì |  |  |
| Supporto aggiunta rete virtuale| No | |  |
| Isolamento rete e supporto del firewall| Sì |  |  |
| Supporto del tunneling forzato| No |  |  |

## <a name="monitoring--logging"></a>Monitoraggio e registrazione

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Supporto monitoraggio di Azure (analisi dei log, analisi approfondita dell'app e così via)| Sì | |  |
| Piano di gestione e controllo - Registrazione e controllo| Sì |  |  |
| Piano dati - Registrazione e controllo| Sì |   |  |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Authentication| Sì | | [Autorizzare l'accesso a hub eventi di Azure](authorize-access-event-hubs.md), [autorizzare l'accesso alle risorse di hub eventi usando Azure Active Directory](authorize-access-azure-active-directory.md), [autorizzando l'accesso alle risorse di hub eventi usando firme di accesso condiviso](authorize-access-shared-access-signature.md) |
| Autorizzazione|  Sì | | [Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse di hub eventi](authenticate-managed-identity.md), [autenticare un'applicazione con Azure Active Directory per accedere alle risorse di hub eventi](authenticate-application.md), [autenticare l'accesso alle risorse di hub eventi usando firme di accesso condiviso (SAS)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|---|---|--|--|
| Crittografia lato server dei dati inattivi: chiavi gestite da Microsoft |  Sì | |  |
| Crittografia lato server dei dati inattivi: chiavi gestite dal cliente (BYOK) | Sì. Disponibile per i cluster dedicati. | Una chiave gestita dal cliente nell'insieme di credenziali delle chiavi di Azure può essere usata per crittografare i dati in un hub eventi inattivi. | [Configurare chiavi gestite dal cliente per la crittografia dei dati inattivi di hub eventi di Azure usando il portale di Azure](configure-customer-managed-key.md) |
| Crittografia a livello di colonna (Servizi dati di Azure)| N/D | |  |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | |  |
| Chiamate API crittografate| Sì |  |  |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Supporto gestione della configurazione (controllo delle versioni di configurazione e così via)| Sì | |  |

## <a name="next-steps"></a>Passaggi successivi

- Maggiori informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).
