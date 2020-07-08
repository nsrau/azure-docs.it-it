---
title: Controlli di sicurezza
description: Elenco di controllo dei controlli di sicurezza incorporati per la valutazione del servizio Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: bb8742c38fae88dc1fd1fd1ec175b248f30df3a0
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86054458"
---
# <a name="security-controls-for-azure-resource-manager"></a>Controlli di sicurezza per Azure Resource Manager

Questo articolo descrive i controlli di sicurezza incorporati in Azure Resource Manager.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server dei dati inattivi: chiavi gestite da Microsoft | Sì |  |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | HTTPS/TLS. |
| Crittografia lato server dei dati inattivi: chiavi gestite dal cliente (BYOK) | N/D | Azure Resource Manager non archivia alcun contenuto del cliente, ma solo i dati di controllo. |
| Crittografia a livello di colonna (Servizi dati di Azure)| Sì | |
| Chiamate API crittografate| Sì | |

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint di servizio| No | |
| Supporto aggiunta rete virtuale| Sì | |
| Isolamento rete e supporto del firewall| No |  |
| Supporto del tunneling forzato| No |  |

## <a name="monitoring--logging"></a>Monitoraggio e registrazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto monitoraggio di Azure (analisi dei log, analisi approfondita dell'app e così via)| No | |
| Piano di gestione e controllo - Registrazione e controllo| Sì | I log attività espongono tutte le operazioni di scrittura (PUT, POST, DELETE) eseguite sulle risorse; vedere [visualizzare i log attività per controllare le azioni sulle risorse](view-activity-logs.md). |
| Piano dati - Registrazione e controllo| N/D | |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Basato su [Azure Active Directory](../../active-directory/index.yml) .|
| Autorizzazione| Sì | |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto gestione della configurazione (controllo delle versioni di configurazione e così via)| Sì |  |

## <a name="next-steps"></a>Passaggi successivi

- Maggiori informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../../security/fundamentals/security-controls.md).
