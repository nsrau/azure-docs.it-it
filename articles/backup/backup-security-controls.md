---
title: Controlli di sicurezza
description: Informazioni sui controlli di sicurezza usati nel servizio backup di Azure. Questi controlli consentono al servizio di prevenire, rilevare e rispondere alle vulnerabilità di sicurezza.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 96899c0669f3063232c36ad3ae1fec76a90e0a5c
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090863"
---
# <a name="security-controls-for-azure-backup"></a>Controlli di sicurezza per backup di Azure

Questo articolo descrive i controlli di sicurezza incorporati in backup di Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note | Documentazione
|---|---|--|--|
| Supporto endpoint di servizio| No |  |  |
| Supporto aggiunta rete virtuale| No |  |  |
| Isolamento rete e supporto del firewall| Sì | |  |
| Supporto del tunneling forzato per le macchine virtuali di Azure | Sì  |  |  |
| Supporto del tunneling forzato per le applicazioni in esecuzione nelle macchine virtuali di Azure| No  |  |  |

## <a name="monitoring--logging"></a>Monitoraggio e registrazione

| Controllo di sicurezza | Sì/No | Note| Documentazione
|---|---|--|--|
| Supporto di monitoraggio di Azure (ad esempio log Analytics, App Insights)| Sì | Log Analytics è supportato tramite i log delle risorse. Per altre informazioni, vedere [monitorare i carichi di lavoro protetti di backup di Azure con log Analytics](backup-azure-diagnostics-mode-data-model.md). |  |
| Piano di gestione e controllo - Registrazione e controllo| Sì | Tutte le azioni attivate dai clienti dal portale di Azure vengono registrate nei log attività. |  |
| Piano dati - Registrazione e controllo| No | Il piano dati di Backup di Azure non è raggiungibile direttamente.  |  |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note| Documentazione
|---|---|--|--|
| Authentication| Sì | L'autenticazione avviene tramite Azure Active Directory. |  |
| Autorizzazione| Sì | Creati dal cliente e vengono usati i ruoli predefiniti di Azure. Per altre informazioni, vedere [usare il controllo degli accessi in base al ruolo di Azure per gestire i punti di ripristino di backup di Azure](./backup-rbac-rs-vault.md). |  |

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
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì|  |  |

## <a name="next-steps"></a>Passaggi successivi

- Maggiori informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).
