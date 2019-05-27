---
title: Attributi di sicurezza comuni per Azure Resource Manager
description: Un elenco di controllo di attributi di sicurezza comuni per la valutazione di Azure Resource Manager
services: azure-resource-manager
author: msmbaldwin
manager: barbkess
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mbaldwin
ms.openlocfilehash: a771d4c2ae22b7bf149c13c80fe5286ef52a4545
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002269"
---
# <a name="security-attributes-for-azure-resource-manager"></a>Attributi di sicurezza per Azure Resource Manager

Questo articolo sono descritti gli attributi di sicurezza creati in Azure Resource Manager.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Sì |  |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Nella crittografia di rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Sì | HTTPS/TLS. |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| N/D | Azure Resource Manager non archivia alcun contenuto del cliente, solo i dati di controllo. |
| Crittografia a livello di colonna (Azure Data Services)| Sì | |
| Chiamate API crittografate| Sì | |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| N. | |
| Supportare l'inserimento di rete virtuale| Sì | |
| Isolamento di rete e supporto firewall| N. |  |
| Supporto di tunneling forzato| N. |  |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| N. | |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | [Azure Active Directory](/azure/active-directory) basato.|
| Authorization| Sì | |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo| Sì | Log attività di esporre tutte le operazioni (PUT, POST, DELETE) eseguite sulle risorse; di scrittura visualizzare [visualizzare i log attività per controllare le azioni sulle risorse](resource-group-audit.md). |
| Controllo e registrazione di piano dati| N/D | |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Sì |  |
