---
title: Attributi di sicurezza comuni per Azure ExpressRoute
description: Un elenco di controllo di attributi di sicurezza comuni per la valutazione di Azure ExpressRoute
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: d6156715fb87831d465197fd8eec59d245221e48
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083275"
---
# <a name="common-security-attributes-for-azure-expressroute"></a>Attributi di sicurezza comuni per Azure ExpressRoute

La sicurezza è integrata in ogni aspetto di un servizio di Azure. Questo articolo sono descritti gli attributi comuni di sicurezza integrati di Azure ExpressRoute.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>|  N/D | ExpressRoute non archivia i dati dei clienti. |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Crittografia nella rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| No | |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| N/D |  |
| Crittografia a livello di colonna (Azure Data Services)| N/D | |
| Chiamate API crittografate| Yes | Attraverso [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| N/D |  |
| supportare l'inserimento di rete virtuale| N/D | |
| Isolamento di rete e supporto firewall| Yes | Ogni cliente è contenuta nel proprio dominio di routing e sottoposti a tunneling alla propria rete virtuale |
| Supporto di tunneling forzato| N/D | Via Border Gateway Protocol (BGP). |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Yes | Visualizzare [ExpressRoute monitoraggio, le metriche e avvisi](expressroute-monitoring-metrics-alerts.md).|

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | Account del servizio per il Gateway di Microsoft (GWM) (controller); Just-in-Time (JIT) di accesso per lo sviluppo e operative |
| Authorization|  Yes |Account del servizio per il Gateway di Microsoft (GWM) (controller); Just-in-Time (JIT) di accesso per lo sviluppo e operative |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note| 
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo| Yes |  |
| Controllo e registrazione di piano dati| No |   |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Yes | Tramite il Provider di risorse di rete (NRP). |
