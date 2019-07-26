---
title: Attributi di sicurezza per hub eventi di Azure
description: Elenco di controllo degli attributi di sicurezza per la valutazione di hub eventi di Azure
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mbaldwin
ms.openlocfilehash: 2262609de774eb2b1334215bf46968b5554ed691
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442444"
---
# <a name="security-attributes-for-azure-event-hubs"></a>Attributi di sicurezza per hub eventi di Azure

Questo articolo descrive gli attributi di sicurezza incorporati in hub eventi di Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia|  Sì | |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Yes | |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| No |  |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Yes |  |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Sì |  |
| Supporto di VNet Injection| No | |
| Isolamento rete e supporto del firewall| Sì |  |
| Supporto del tunneling forzato| No |  |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | |
| Authorization|  Yes | |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Sì |  |
| Registrazione e controllo del piano dati| Sì |   |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | |
