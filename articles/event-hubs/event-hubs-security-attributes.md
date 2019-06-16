---
title: Attributi di sicurezza comuni per hub eventi di Azure
description: Un elenco di controllo di attributi di sicurezza comuni per la valutazione di hub eventi di Azure
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mbaldwin
ms.openlocfilehash: 50f19d50f211972c2042c69a1e3177ffb4a47624
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66247200"
---
# <a name="common-security-attributes-for-azure-event-hubs"></a>Attributi di sicurezza comuni per hub eventi di Azure

Questo articolo sono descritti gli attributi di sicurezza comuni incorporati in hub eventi di Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>|  Yes | |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Crittografia nella rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Yes | |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| No |  |
| Crittografia a livello di colonna (Azure Data Services)| N/D | |
| Chiamate API crittografate| Yes |  |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| Yes |  |
| supportare l'inserimento di rete virtuale| No | |
| Isolamento di rete e supporto firewall| Yes |  |
| Supporto di tunneling forzato| No |  |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Yes | |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | |
| Authorization|  Yes | |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo| Yes |  |
| Controllo e registrazione di piano dati| Yes |   |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Yes | |
