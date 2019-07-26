---
title: Attributi di sicurezza per Azure Resource Manager
description: Elenco di controllo degli attributi di sicurezza per la valutazione Azure Resource Manager
services: azure-resource-manager
author: msmbaldwin
manager: barbkess
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mbaldwin
ms.openlocfilehash: e3bfb79c54ff57adfa947f2dd0100f6c05c7af9f
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444158"
---
# <a name="security-attributes-for-azure-resource-manager"></a>Attributi di sicurezza per Azure Resource Manager

Questo articolo descrive gli attributi di sicurezza incorporati in Azure Resource Manager.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia| Sì |  |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Yes | HTTPS/TLS. |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| N/D | Azure Resource Manager non archivia alcun contenuto del cliente, ma solo i dati di controllo. |
| Crittografia a livello di colonna (servizi dati di Azure)| Yes | |
| Chiamate API crittografate| Sì | |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| No | |
| Supporto di VNet Injection| Sì | |
| Isolamento rete e supporto del firewall| No |  |
| Supporto del tunneling forzato| No |  |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| No | |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Basato su [Azure Active Directory](/azure/active-directory) .|
| Authorization| Sì | |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Sì | I log attività espongono tutte le operazioni di scrittura (PUT, POST, DELETE) eseguite sulle risorse; vedere [visualizzare i log attività per controllare le azioni sulle risorse](resource-group-audit.md). |
| Registrazione e controllo del piano dati| N/D | |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Yes |  |
