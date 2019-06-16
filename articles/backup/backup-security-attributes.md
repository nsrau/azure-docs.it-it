---
title: Attributi di sicurezza comuni per il Backup di Azure
description: Un elenco di controllo degli attributi di sicurezza comuni per la valutazione di Backup di Azure
services: backup
author: msmbaldwin
manager: barbkess
ms.service: backup
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 86b8ab96a94a6ffc44c304d8a0a689301560a989
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002810"
---
# <a name="security-attributes-for-azure-backup"></a>Attributi di sicurezza per il Backup di Azure

Questo articolo sono descritti gli attributi di sicurezza integrati di Backup di Azure. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Yes | Uso della crittografia del servizio di archiviazione per gli account di archiviazione. |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Nella crittografia di rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| No | Uso di HTTPS. |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| No |  |
| Crittografia a livello di colonna (Azure Data Services)| No |  |
| Chiamate API crittografate| Yes |  |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| No |  |
| Supportare l'inserimento di rete virtuale| No |  |
| Isolamento di rete e supporto firewall| Yes | Il tunneling forzato è supportato per il backup delle macchine virtuali. Il tunneling forzato non è supportato per i carichi di lavoro in esecuzione all'interno di macchine virtuali. |
| Supporto di tunneling forzato| No |  |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Yes | Log Analytics è supportato tramite i log di diagnostica. Visualizzare [monitoraggio di Azure Backup protetto da carichi di lavoro usando Log Analitica](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) per altre informazioni. |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | L'autenticazione avviene tramite Azure Active Directory. |
| Authorization| Yes | Vengono usati ruoli RBAC creati dai clienti o predefiniti. Visualizzare [controllo di accesso degli accessi in base a gestire i punti di ripristino di Backup di Azure](/azure/backup/backup-rbac-rs-vault) per altre informazioni. |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo| Yes | Tutte le azioni attivate dai clienti dal portale di Azure vengono registrate nei log attività. |
| Controllo e registrazione di piano dati| No | Il piano dati di Backup di Azure non è raggiungibile direttamente.  |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Yes|  |