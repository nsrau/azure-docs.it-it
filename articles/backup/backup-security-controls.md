---
title: Controlli di sicurezza
description: Informazioni sui controlli di sicurezza usati nel servizio backup di Azure. Questi controlli consentono al servizio di prevenire, rilevare e rispondere alle vulnerabilità di sicurezza.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: c5a324ac3738abfcf1a51442d7acd78a01e5717e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86513541"
---
# <a name="security-controls-for-azure-backup"></a>Controlli di sicurezza per backup di Azure

Questo articolo descrive i controlli di sicurezza incorporati in backup di Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note | Documentazione
|---|---|--|--|
| Supporto endpoint di servizio| No |  |  |
| Supporto aggiunta rete virtuale| No |  |  |
| Isolamento rete e supporto del firewall| Sì | Il tunneling forzato è supportato per il backup delle macchine virtuali. Il tunneling forzato non è supportato per i carichi di lavoro in esecuzione all'interno di macchine virtuali. |  |
| Supporto del tunneling forzato| No |  |  |

## <a name="monitoring--logging"></a>Monitoraggio e registrazione

| Controllo di sicurezza | Sì/No | Note| Documentazione
|---|---|--|--|
| Supporto monitoraggio di Azure (analisi dei log, analisi approfondita dell'app e così via)| Sì | Log Analytics è supportato tramite i log delle risorse. Per altre informazioni, vedere [monitorare i carichi di lavoro protetti di backup di Azure con log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |  |
| Piano di gestione e controllo - Registrazione e controllo| Sì | Tutte le azioni attivate dai clienti dal portale di Azure vengono registrate nei log attività. |  |
| Piano dati - Registrazione e controllo| No | Il piano dati di Backup di Azure non è raggiungibile direttamente.  |  |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note| Documentazione
|---|---|--|--|
| Authentication| Sì | L'autenticazione avviene tramite Azure Active Directory. |  |
| Autorizzazione| Sì | Vengono usati ruoli RBAC creati dai clienti o predefiniti. Per altre informazioni, vedere [usare il controllo degli accessi in base al ruolo per gestire i punti di ripristino di backup di Azure](./backup-rbac-rs-vault.md). |  |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note | Documentazione
|---|---|--|--|
| Crittografia lato server dei dati inattivi: chiavi gestite da Microsoft | Sì | Uso della crittografia del servizio di archiviazione per gli account di archiviazione. |  |
| Crittografia lato server dei dati inattivi: chiavi gestite dal cliente (BYOK) | No |  |  |
| Crittografia a livello di colonna (Servizi dati di Azure)| No |  |  |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| No | Uso di HTTPS. |  |
| Chiamate API crittografate| Sì |  |  |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note| Documentazione
|---|---|--|--|
| Supporto gestione della configurazione (controllo delle versioni di configurazione e così via)| Sì|  |  |

## <a name="next-steps"></a>Passaggi successivi

- Maggiori informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).
