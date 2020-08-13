---
title: 'Azure ExpressRoute: controlli di sicurezza'
description: Informazioni sui controlli di sicurezza in Azure ExpressRoute, che sono qualità o funzionalità che consentono di prevenire, rilevare e rispondere alle vulnerabilità di sicurezza.
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 6a4589d1aa768548f2ae9ffa01f289c823c0b2e5
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192428"
---
# <a name="security-controls-for-azure-expressroute"></a>Controlli di sicurezza per Azure ExpressRoute

Questo articolo descrive i controlli di sicurezza incorporati in Azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint di servizio| N/D |  |
| Supporto aggiunta rete virtuale| N/D | |
| Isolamento rete e supporto del firewall| Sì | Ogni cliente è contenuto nel proprio dominio di routing ed è sottopercorso al tunneling per il proprio VNet |
| Supporto del tunneling forzato| N/D | Tramite Border Gateway Protocol (BGP). |

## <a name="monitoring--logging"></a>Monitoraggio e registrazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto monitoraggio di Azure (analisi dei log, analisi approfondita dell'app e così via)| Sì | Vedere [monitoraggio, metriche e avvisi di ExpressRoute](expressroute-monitoring-metrics-alerts.md).|
| Piano di gestione e controllo - Registrazione e controllo| Sì |  |
| Piano dati - Registrazione e controllo| No |   |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Account del servizio per il gateway per Microsoft (GWM) (controller); Accesso just-in-time (JIT) per dev e OP. |
| Autorizzazione|  Sì |Account del servizio per il gateway per Microsoft (GWM) (controller); Accesso just-in-time (JIT) per dev e OP. |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server dei dati inattivi: chiavi gestite da Microsoft |  N/D | ExpressRoute non archivia i dati dei clienti. |
| Crittografia lato server dei dati inattivi: chiavi gestite dal cliente (BYOK) | N/D |  |
| Crittografia a livello di colonna (Servizi dati di Azure)| N/D | |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| No | |
| Chiamate API crittografate| Sì | Tramite [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |


## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto gestione della configurazione (controllo delle versioni di configurazione e così via)| Sì | Tramite il provider di risorse di rete (NRP). |

## <a name="next-steps"></a>Passaggi successivi

- Maggiori informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).