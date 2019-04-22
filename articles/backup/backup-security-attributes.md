---
title: Attributi di sicurezza comuni per il Backup di Azure
description: Un elenco di controllo degli attributi di sicurezza comuni per la valutazione di Backup di Azure
services: backup
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: backup
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 93dd5372bffb278894987cd3cc2b8322cbc5e577
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679680"
---
# <a name="common-security-attributes-for-azure-backup"></a>Attributi di sicurezza comuni per il Backup di Azure

La sicurezza è integrata in ogni aspetto di un servizio di Azure. Questo articolo sono descritti gli attributi di sicurezza comuni incorporati in Backup di Azure. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Sì | Uso della crittografia del servizio di archiviazione per gli account di archiviazione. |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Crittografia nella rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| No  | Uso di HTTPS. |
| Gestione delle chiavi di crittografia (CMK, BYOK e così via)| No  |  |
| Crittografia a livello di colonna (Servizi dati di Azure)| No  |  |
| Chiamate API crittografate| Sì |  |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint di servizio| No  |  |
| Supporto aggiunta rete virtuale| No  |  |
| Supporto isolamento rete/protezione tramite firewall| Sì | Il tunneling forzato è supportato per il backup delle macchine virtuali. Il tunneling forzato non è supportato per i carichi di lavoro in esecuzione all'interno di macchine virtuali. |
| Supporto tunneling forzato | No  |  |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Sì | Log Analytics è supportato tramite i log di diagnostica. Visualizzare [monitoraggio di Azure Backup protetto da carichi di lavoro usando Log Analitica](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) per altre informazioni. |

## <a name="iam-support"></a>Supporto per la gestione delle identità e degli accessi

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Gestione degli accessi - Autenticazione| Sì | L'autenticazione avviene tramite Azure Active Directory. |
| Gestione degli accessi - Autorizzazione| Sì | Vengono usati ruoli RBAC creati dai clienti o predefiniti. Visualizzare [controllo di accesso degli accessi in base a gestire i punti di ripristino di Backup di Azure](/azure/backup/backup-rbac-rs-vault) per altre informazioni. |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Piano di gestione/controllo - Registrazione e controllo| Sì | Tutte le azioni attivate dai clienti dal portale di Azure vengono registrate nei log attività. |
| Piano dati - Registrazione e controllo| No  | Il piano dati di Backup di Azure non è raggiungibile direttamente.  |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Sì|  |