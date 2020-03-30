---
title: 'Azure ExpressRoute: Controlli di sicurezzaAzure ExpressRoute: Security controls'
description: Elenco di controllo dei controlli di sicurezza per la valutazione di Azure ExpressRoute
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: a288b44c07bc2df8529f07264dcee648f3af379a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74079922"
---
# <a name="security-controls-for-azure-expressroute"></a>Controlli di sicurezza per Azure ExpressRouteSecurity controls for Azure ExpressRoute

Questo articolo documenta i controlli di sicurezza incorporati in Azure ExpressRoute.This article documents the security controls built into Azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto dell'endpoint di servizioService endpoint support| N/D |  |
| Supporto per l'inserimento di vNet| N/D | |
| Isolamento della rete e supporto firewall| Sì | Ogni cliente è contenuto nel proprio dominio di routing e sottoposto a tunneling alla propria rete virtuale |
| Supporto per il tunneling forzato| N/D | Tramite il protocollo BGP (Border Gateway Protocol). |

## <a name="monitoring--logging"></a>Monitoraggio della registrazione dei &

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per il monitoraggio di Azure (analisi dei log, informazioni dettagliate sulle app e così via)Azure monitoring support (Log analytics, App insights, etc.)| Sì | Vedere [Monitoraggio, metriche e avvisi di ExpressRoute.](expressroute-monitoring-metrics-alerts.md)|
| Controllo e gestione del controllo e dell'audit degli aerei| Sì |  |
| Registrazione e controllo dei piani dati| No |   |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Account di servizio per Gateway per Microsoft (GWM) (controllore); Accesso Just in Time (JIT) per Dev e OP. |
| Autorizzazione|  Sì |Account di servizio per Gateway per Microsoft (GWM) (controllore); Accesso Just in Time (JIT) per Dev e OP. |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server inattivi: chiavi gestite da MicrosoftServer-side encryption at rest: Microsoft-managed keys |  N/D | ExpressRoute non archivia i dati dei clienti. |
| Crittografia lato server inattivi: chiavi gestite dal cliente (BYOK)Server-side encryption at rest: customer-managed keys (BYOK) | N/D |  |
| Crittografia a livello di colonna (Azure Data Services)Column level encryption (Azure Data Services)| N/D | |
| Crittografia in transito (ad esempio la crittografia ExpressRoute, nella crittografia della rete virtuale e crittografia VNet-VNet)Encryption in transit (such as ExpressRoute encryption, in VNet encryption, and VNet-VNet encryption)| No | |
| Chiamate API crittografate| Sì | Tramite [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |


## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Tramite il provider di risorse di rete (NRP). |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui controlli di sicurezza incorporati nei servizi di Azure.Learn more about the [built-in security controls across Azure services](../security/fundamentals/security-controls.md).