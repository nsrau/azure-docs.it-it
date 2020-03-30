---
title: Controlli di sicurezza per hub eventi di AzureSecurity controls for Azure Event Hubs
description: Questo articolo fornisce un elenco di controllo dei controlli di sicurezza per la valutazione degli hub eventi di Azure (rete, identità, protezione dei dati e così via).
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 0769e88eb72b5b347dd9ebf4b1634501ca54098e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76309507"
---
# <a name="security-controls-for-azure-event-hubs"></a>Controlli di sicurezza per hub eventi di AzureSecurity controls for Azure Event Hubs

Questo articolo documenta i controlli di sicurezza incorporati negli hub eventi di Azure.This article documents the security controls built into Azure Event Hubs.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|---|---|--|--|
| Supporto dell'endpoint di servizioService endpoint support| Sì |  |  |
| Supporto per l'inserimento di vNet| No | |  |
| Isolamento della rete e supporto firewall| Sì |  |  |
| Supporto per il tunneling forzato| No |  |  |

## <a name="monitoring--logging"></a>Monitoraggio della registrazione dei &

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Supporto per il monitoraggio di Azure (analisi dei log, informazioni dettagliate sulle app e così via)Azure monitoring support (Log analytics, App insights, etc.)| Sì | |  |
| Controllo e gestione del controllo e dell'audit degli aerei| Sì |  |  |
| Registrazione e controllo dei piani dati| Sì |   |  |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Authentication| Sì | | [Autorizzare l'accesso agli hub eventi](authorize-access-event-hubs.md)di Azure, [autorizzare l'accesso alle risorse di Hub eventi usando Azure Active Directory](authorize-access-azure-active-directory.md), [autorizzando l'accesso alle risorse degli hub eventi usando le firme](authorize-access-shared-access-signature.md) di accesso condivisoAuthorize access to Azure Event Hubs , Authorize access to Event Hubs resources using Azure Active Directory , Authorizing access to Event Hubs resources using Shared Access Signatures |
| Autorizzazione|  Sì | | [Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse degli hub eventi](authenticate-managed-identity.md), [Autenticare un'applicazione con Azure Active Directory per accedere alle risorse degli hub eventi,](authenticate-application.md) [autenticare l'accesso alle risorse degli hub eventi usando firme](authenticate-shared-access-signature.md) di accesso condiviso |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|---|---|--|--|
| Crittografia lato server inattivi: chiavi gestite da MicrosoftServer-side encryption at rest: Microsoft-managed keys |  Sì | |  |
| Crittografia lato server inattivi: chiavi gestite dal cliente (BYOK)Server-side encryption at rest: customer-managed keys (BYOK) | Sì. Disponibile per i cluster dedicati. | Una chiave gestita dal cliente in KeyVault di Azure può essere usata per crittografare i dati in un hub eventi inattivi. | [Configurare le chiavi gestite dal cliente per la crittografia dei dati di Hub eventi di Azure inattivi tramite il portale di AzureConfigure customer-managed keys for encrypting Azure Event Hubs data at rest by using the Azure portal](configure-customer-managed-key.md) |
| Crittografia a livello di colonna (Azure Data Services)Column level encryption (Azure Data Services)| N/D | |  |
| Crittografia in transito (ad esempio la crittografia ExpressRoute, nella crittografia della rete virtuale e crittografia VNet-VNet)Encryption in transit (such as ExpressRoute encryption, in VNet encryption, and VNet-VNet encryption)| Sì | |  |
| Chiamate API crittografate| Sì |  |  |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | |  |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui controlli di sicurezza incorporati nei servizi di Azure.Learn more about the [built-in security controls across Azure services](../security/fundamentals/security-controls.md).
