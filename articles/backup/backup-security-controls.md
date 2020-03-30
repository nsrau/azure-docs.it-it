---
title: Controlli di sicurezza
description: Informazioni sui controlli di sicurezza usati nel servizio Backup di Azure.Learn about the security controls used in the Azure Backup service. Questi controlli consentono al servizio di prevenire, rilevare e rispondere alle vulnerabilità della sicurezza.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 0e3f5ce942ea8aef9bf5eb98883ae1e72a7ab239
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172147"
---
# <a name="security-controls-for-azure-backup"></a>Controlli di sicurezza per Backup di AzureSecurity controls for Azure Backup

Questo articolo documenta i controlli di sicurezza integrati in Backup di Azure.This article documents the security controls built into Azure Backup.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note | Documentazione
|---|---|--|
| Supporto dell'endpoint di servizioService endpoint support| No |  |  |
| Supporto per l'inserimento di vNet| No |  |  |
| Isolamento della rete e supporto firewall| Sì | Il tunneling forzato è supportato per il backup delle macchine virtuali. Il tunneling forzato non è supportato per i carichi di lavoro in esecuzione all'interno di macchine virtuali. |  |
| Supporto per il tunneling forzato| No |  |  |

## <a name="monitoring--logging"></a>Monitoraggio della registrazione dei &

| Controllo di sicurezza | Sì/No | Note| | Documentazione
|---|---|--|
| Supporto per il monitoraggio di Azure (analisi dei log, informazioni dettagliate sulle app e così via)Azure monitoring support (Log analytics, App insights, etc.)| Sì | Log Analytics è supportato tramite i log delle risorse. Per altre informazioni, vedere Monitorare i carichi di lavoro protetti da Backup di Azure usando Log Analytics.For more information, see [Monitor Azure Backup protected workloads using Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |  |
| Controllo e gestione del controllo e dell'audit degli aerei| Sì | Tutte le azioni attivate dai clienti dal portale di Azure vengono registrate nei log attività. |  |
| Registrazione e controllo dei piani dati| No | Il piano dati di Backup di Azure non è raggiungibile direttamente.  |  |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note| | Documentazione
|---|---|--|
| Authentication| Sì | L'autenticazione avviene tramite Azure Active Directory. |  |
| Autorizzazione| Sì | Vengono usati ruoli RBAC creati dai clienti o predefiniti. Per altre informazioni, vedere Usare il controllo degli accessi in base al ruolo per gestire i punti di ripristino di Backup di Azure.For more information, see [Use Role-Based Access Control to manage Azure Backup recovery points.](/azure/backup/backup-rbac-rs-vault) |  |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note | | Documentazione
|---|---|--|
| Crittografia lato server inattivi: chiavi gestite da MicrosoftServer-side encryption at rest: Microsoft-managed keys | Sì | Uso della crittografia del servizio di archiviazione per gli account di archiviazione. |  |
| Crittografia lato server inattivi: chiavi gestite dal cliente (BYOK)Server-side encryption at rest: customer-managed keys (BYOK) | No |  |  |
| Crittografia a livello di colonna (Azure Data Services)Column level encryption (Azure Data Services)| No |  |  |
| Crittografia in transito (ad esempio la crittografia ExpressRoute, nella crittografia della rete virtuale e crittografia VNet-VNet)Encryption in transit (such as ExpressRoute encryption, in VNet encryption, and VNet-VNet encryption)| No | Uso di HTTPS. |  |
| Chiamate API crittografate| Sì |  |  |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note| | Documentazione
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì|  |  |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui controlli di sicurezza incorporati nei servizi di Azure.Learn more about the [built-in security controls across Azure services](../security/fundamentals/security-controls.md).
