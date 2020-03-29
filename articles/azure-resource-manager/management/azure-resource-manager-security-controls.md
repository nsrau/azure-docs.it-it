---
title: Controlli di sicurezza
description: Elenco di controllo dei controlli di sicurezza predefiniti per la valutazione del servizio Azure Resource Manager.A checklist of built-in security controls for evaluating the Azure Resource Manager service.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: d0a0625153e428a0d261e52d40b31ef5142eddfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485624"
---
# <a name="security-controls-for-azure-resource-manager"></a>Controlli di sicurezza per Azure Resource ManagerSecurity controls for Azure Resource Manager

Questo articolo documenta i controlli di sicurezza integrati in Azure Resource Manager.This article documents the security controls built into Azure Resource Manager.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server inattivi: chiavi gestite da MicrosoftServer-side encryption at rest: Microsoft-managed keys | Sì |  |
| Crittografia in transito (ad esempio la crittografia ExpressRoute, nella crittografia della rete virtuale e crittografia VNet-VNet)Encryption in transit (such as ExpressRoute encryption, in VNet encryption, and VNet-VNet encryption)| Sì | HTTPS/TLS. |
| Crittografia lato server inattivi: chiavi gestite dal cliente (BYOK)Server-side encryption at rest: customer-managed keys (BYOK) | N/D | Azure Resource Manager non archivia alcun contenuto del cliente, ma solo dati di controllo. |
| Crittografia a livello di colonna (Azure Data Services)Column level encryption (Azure Data Services)| Sì | |
| Chiamate API crittografate| Sì | |

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto dell'endpoint di servizioService endpoint support| No | |
| Supporto per l'inserimento di vNet| Sì | |
| Isolamento della rete e supporto firewall| No |  |
| Supporto per il tunneling forzato| No |  |

## <a name="monitoring--logging"></a>Monitoraggio della registrazione dei &

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per il monitoraggio di Azure (analisi dei log, informazioni dettagliate sulle app e così via)Azure monitoring support (Log analytics, App insights, etc.)| No | |
| Controllo e gestione del controllo e dell'audit degli aerei| Sì | I log attività espongono tutte le operazioni di scrittura (PUT, POST, DELETE) eseguite sulle risorse. Vedere [Visualizzare i log attività per controllare le azioni sulle risorse](view-activity-logs.md). |
| Registrazione e controllo dei piani dati| N/D | |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Basato su [Azure Active Directory.](/azure/active-directory)|
| Autorizzazione| Sì | |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì |  |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui controlli di sicurezza incorporati nei servizi di Azure.Learn more about the [built-in security controls across Azure services](../../security/fundamentals/security-controls.md).