---
title: Controlli di sicurezza per Azure Resource Manager
description: Elenco di controllo dei controlli di sicurezza incorporati per la valutazione del servizio Azure Resource Manager.
services: azure-resource-manager
author: msmbaldwin
manager: rkarlin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 78f3f41ca2f0aa45d295fd9846cf3aa96cccea72
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390472"
---
# <a name="security-controls-for-azure-resource-manager"></a>Controlli di sicurezza per Azure Resource Manager

Questo articolo descrive i controlli di sicurezza incorporati in Azure Resource Manager.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server: chiavi gestite da Microsoft | SÌ |  |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| SÌ | HTTPS/TLS. |
| Crittografia lato server inattiva: chiavi gestite dal cliente (BYOK) | N/D | Azure Resource Manager non archivia alcun contenuto del cliente, ma solo i dati di controllo. |
| Crittografia a livello di colonna (servizi dati di Azure)| SÌ | |
| Chiamate API crittografate| SÌ | |

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| No | |
| Supporto di VNet Injection| SÌ | |
| Isolamento rete e supporto del firewall| No |  |
| Supporto del tunneling forzato| No |  |

## <a name="monitoring--logging"></a>Monitoraggio & registrazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| No | |
| Registrazione e controllo del piano di gestione e controllo| SÌ | I log attività espongono tutte le operazioni di scrittura (PUT, POST, DELETE) eseguite sulle risorse; vedere [visualizzare i log attività per controllare le azioni sulle risorse](resource-group-audit.md). |
| Registrazione e controllo del piano dati| N/D | |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| SÌ | Basato su [Azure Active Directory](/azure/active-directory) .|
| Authorization| SÌ | |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| SÌ |  |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).