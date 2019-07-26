---
title: Attributi di sicurezza per gestione API di Azure
description: Elenco di controllo degli attributi di sicurezza per la valutazione di gestione API
services: api-management
author: msmbaldwin
manager: barbkess
ms.service: api-management
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 2049e2349e3a25ebd4d3f4db19ec47bbaeb067de
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442288"
---
# <a name="security-attributes-for-api-management"></a>Attributi di sicurezza per gestione API

Questo articolo descrive gli attributi di sicurezza incorporati in gestione API.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia| Sì (solo crittografia lato servizio) | I dati sensibili, ad esempio i certificati, le chiavi e i valori con nome segreto, vengono crittografati con chiavi di istanza di servizio gestite dal servizio. |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | [Express Route](../expressroute/index.yml) e la crittografia VNet sono fornite da [rete di Azure](../virtual-network/index.yml). |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| No | Tutte le chiavi di crittografia sono per istanza del servizio e sono gestite dal servizio. |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Sì | Le chiamate del piano di gestione vengono effettuate tramite [Azure Resource Manager](../azure-resource-manager/index.yml) su TLS. È necessario un token JSON Web (JWT) valido.  Le chiamate del piano dati possono essere protette con TLS e uno dei meccanismi di autenticazione supportati (ad esempio, il certificato client o JWT).
 |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| No | |
| Supporto di VNet Injection| Sì | |
| Isolamento rete e supporto del firewall| Yes | Usando i gruppi di sicurezza di rete (NSG) e applicazione Azure Gateway (o altro appliance software) rispettivamente. |
| Supporto del tunneling forzato| Sì | Sulla rete di Azure è disponibile il tunneling forzato. |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | |
| Authorization| Sì | |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Sì | [Log attività di monitoraggio di Azure](../azure-monitor/platform/activity-logs-overview.md) |
| Registrazione e controllo del piano dati| Sì | [Log di diagnostica di monitoraggio di Azure](../azure-monitor/platform/diagnostic-logs-overview.md) e, facoltativamente, [applicazione Azure informazioni dettagliate](../azure-monitor/app/app-insights-overview.md).  |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Uso del [Resource Kit DevOps di gestione API di Azure](https://aka.ms/apimdevops) |

## <a name="vulnerability-scans-false-positives"></a>Analisi della vulnerabilità falsi positivi

Questa sezione documenta le vulnerabilità comuni che non influiscono sulla gestione API di Azure.

| Vulnerabilità               | Descrizione                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed è una vulnerabilità nell'implementazione dell'estensione TLS SessionTicket presente in alcuni prodotti F5. Consente la perdita ("emorragia") di un massimo di 31 byte di dati dalla memoria non inizializzata. Questo problema si verifica perché lo stack TLS riempie un ID di sessione, passato dal client, con dati per renderlo 32 bit. |
