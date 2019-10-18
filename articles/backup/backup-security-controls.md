---
title: Controlli di sicurezza per backup di Azure
description: Elenco di controllo dei controlli di sicurezza per la valutazione di backup di Azure
ms.reviewer: mbaldwin
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: dacurwin
ms.openlocfilehash: 8170022a3ee4b4b65e8da0d9e5a2f8464675d08f
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528102"
---
# <a name="security-controls-for-azure-backup"></a>Controlli di sicurezza per backup di Azure

Questo articolo descrive i controlli di sicurezza incorporati in backup di Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note | Documentazione
|---|---|--|
| Supporto per endpoint di servizio| No |  |  |
| Supporto di VNet Injection| No |  |  |
| Isolamento rete e supporto del firewall| SÌ | Il tunneling forzato è supportato per il backup delle macchine virtuali. Il tunneling forzato non è supportato per i carichi di lavoro in esecuzione all'interno di macchine virtuali. |  |
| Supporto del tunneling forzato| No |  |  |

## <a name="monitoring--logging"></a>Monitoraggio & registrazione

| Controllo di sicurezza | Sì/No | Note| | Documentazione
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| SÌ | Log Analytics è supportato tramite i log delle risorse. Per altre informazioni, vedere [monitorare i carichi di lavoro protetti di backup di Azure con log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) . |  |
| Registrazione e controllo del piano di gestione e controllo| SÌ | Tutte le azioni attivate dai clienti dal portale di Azure vengono registrate nei log attività. |  |
| Registrazione e controllo del piano dati| No | Il piano dati di Backup di Azure non è raggiungibile direttamente.  |  |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note| | Documentazione
|---|---|--|
| Authentication| SÌ | L'autenticazione avviene tramite Azure Active Directory. |  |
| Authorization| SÌ | Vengono usati ruoli RBAC creati dai clienti o predefiniti. Per altre informazioni, vedere [usare il controllo degli accessi in base al ruolo per gestire i punti di ripristino di backup di Azure](/azure/backup/backup-rbac-rs-vault) . |  |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note | | Documentazione
|---|---|--|
| Crittografia lato server: chiavi gestite da Microsoft | SÌ | Uso della crittografia del servizio di archiviazione per gli account di archiviazione. |  |
| Crittografia lato server inattiva: chiavi gestite dal cliente (BYOK) | No |  |  |
| Crittografia a livello di colonna (servizi dati di Azure)| No |  |  |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| No | Uso di HTTPS. |  |
| Chiamate API crittografate| SÌ |  |  |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note| | Documentazione
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| SÌ|  |  |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).
