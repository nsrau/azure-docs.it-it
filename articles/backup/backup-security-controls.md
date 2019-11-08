---
title: Controlli di sicurezza per backup di Azure
description: Informazioni sui controlli di sicurezza usati nel servizio backup di Azure. Questi controlli consentono al servizio di prevenire, rilevare e rispondere alle vulnerabilità di sicurezza.
ms.reviewer: mbaldwin
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: dacurwin
ms.openlocfilehash: 9d7bb2e9ea2d2cf9cce6c4f6d3b4f3346e7ce363
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747159"
---
# <a name="security-controls-for-azure-backup"></a>Controlli di sicurezza per backup di Azure

Questo articolo descrive i controlli di sicurezza incorporati in backup di Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note | Documentazione
|---|---|--|
| Supporto per endpoint di servizio| No |  |  |
| Supporto di VNet Injection| No |  |  |
| Isolamento rete e supporto del firewall| Sì | Il tunneling forzato è supportato per il backup delle macchine virtuali. Il tunneling forzato non è supportato per i carichi di lavoro in esecuzione all'interno di macchine virtuali. |  |
| Supporto del tunneling forzato| No |  |  |

## <a name="monitoring--logging"></a>Monitoraggio & registrazione

| Controllo di sicurezza | Sì/No | Note| | Documentazione
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Log Analytics è supportato tramite i log delle risorse. Per altre informazioni, vedere [monitorare i carichi di lavoro protetti di backup di Azure con log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |  |
| Registrazione e controllo del piano di gestione e controllo| Sì | Tutte le azioni attivate dai clienti dal portale di Azure vengono registrate nei log attività. |  |
| Registrazione e controllo del piano dati| No | Il piano dati di Backup di Azure non è raggiungibile direttamente.  |  |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note| | Documentazione
|---|---|--|
| Autenticazione| Sì | L'autenticazione avviene tramite Azure Active Directory. |  |
| Autorizzazione| Sì | Vengono usati ruoli RBAC creati dai clienti o predefiniti. Per altre informazioni, vedere [usare il controllo degli accessi in base al ruolo per gestire i punti di ripristino di backup di Azure](/azure/backup/backup-rbac-rs-vault). |  |

## <a name="data-protection"></a>Protezione dati

| Controllo di sicurezza | Sì/No | Note | | Documentazione
|---|---|--|
| Crittografia lato server: chiavi gestite da Microsoft | Sì | Uso della crittografia del servizio di archiviazione per gli account di archiviazione. |  |
| Crittografia lato server inattiva: chiavi gestite dal cliente (BYOK) | No |  |  |
| Crittografia a livello di colonna (servizi dati di Azure)| No |  |  |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| No | Viene usato HTTPS. |  |
| Chiamate API crittografate| Sì |  |  |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note| | Documentazione
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì|  |  |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).
