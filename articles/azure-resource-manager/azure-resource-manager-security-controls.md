---
title: Controlli di sicurezza
description: Elenco di controllo dei controlli di sicurezza incorporati per la valutazione del servizio Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: b2916a87c5cef01605054569285362fcf1c649c8
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147562"
---
# <a name="security-controls-for-azure-resource-manager"></a>Controlli di sicurezza per Azure Resource Manager

Questo articolo descrive i controlli di sicurezza incorporati in Azure Resource Manager.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Protezione dati

| Controllo di sicurezza | Sì/No | note |
|---|---|--|
| Crittografia lato server: chiavi gestite da Microsoft | Sì |  |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | HTTPS/TLS. |
| Crittografia lato server inattiva: chiavi gestite dal cliente (BYOK) | N/D | Azure Resource Manager non archivia alcun contenuto del cliente, ma solo i dati di controllo. |
| Crittografia a livello di colonna (servizi dati di Azure)| Sì | |
| Chiamate API crittografate| Sì | |

## <a name="network"></a>Network

| Controllo di sicurezza | Sì/No | note |
|---|---|--|
| Supporto per endpoint di servizio| No | |
| Supporto di VNet Injection| Sì | |
| Isolamento rete e supporto del firewall| No |  |
| Supporto del tunneling forzato| No |  |

## <a name="monitoring--logging"></a>Monitoraggio & registrazione

| Controllo di sicurezza | Sì/No | note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| No | |
| Registrazione e controllo del piano di gestione e controllo| Sì | I log attività espongono tutte le operazioni di scrittura (PUT, POST, DELETE) eseguite sulle risorse; vedere [visualizzare i log attività per controllare le azioni sulle risorse](resource-group-audit.md). |
| Registrazione e controllo del piano dati| N/D | |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | note|
|---|---|--|
| Autenticazione| Sì | Basato su [Azure Active Directory](/azure/active-directory) .|
| Autorizzazione| Sì | |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì |  |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).