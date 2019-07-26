---
title: Attributi di sicurezza per Azure ExpressRoute
description: Elenco di controllo degli attributi di sicurezza per la valutazione di Azure ExpressRoute
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: c9a46497c18b99ad7774036fd92e63d024b47045
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442192"
---
# <a name="security-attributes-for-azure-expressroute"></a>Attributi di sicurezza per Azure ExpressRoute

Questo articolo descrive gli attributi di sicurezza incorporati in Azure ExpressRoute.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia|  N/D | ExpressRoute non archivia i dati dei clienti. |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| No | |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| N/D |  |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Sì | Tramite [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| N/D |  |
| Supporto di VNet Injection| N/D | |
| Isolamento rete e supporto del firewall| Yes | Ogni cliente è contenuto nel proprio dominio di routing ed è sottopercorso al tunneling per il proprio VNet |
| Supporto del tunneling forzato| N/D | Tramite Border Gateway Protocol (BGP). |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Vedere [monitoraggio, metriche e avvisi di ExpressRoute](expressroute-monitoring-metrics-alerts.md).|

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Account del servizio per il gateway per Microsoft (GWM) (controller); Accesso just-in-time (JIT) per dev e OP. |
| Authorization|  Yes |Account del servizio per il gateway per Microsoft (GWM) (controller); Accesso just-in-time (JIT) per dev e OP. |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note| 
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Yes |  |
| Registrazione e controllo del piano dati| No |   |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Yes | Tramite il provider di risorse di rete (NRP). |
