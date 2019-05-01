---
title: Attributi di sicurezza comuni per gestione API di Azure
description: Un elenco di controllo di attributi di sicurezza comuni per la valutazione di gestione API
services: api-management
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: api-management
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 040d628f9fe89f68a1c5ab91a1522c6a3cb724d9
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "64508145"
---
# <a name="common-security-attributes-for-api-management"></a>Attributi di sicurezza comuni per gestione API

La sicurezza è integrata in ogni aspetto di un servizio di Azure. Questo articolo sono descritti gli attributi comuni di sicurezza integrati di gestione API.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Sì (solo crittografia sul lato servizio) | I dati sensibili, ad esempio i certificati, chiavi e valori di nome segreto sono crittografati con gestite dal servizio, per le chiavi di istanza di servizio. |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Nella crittografia di rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Sì | [Express Route](../expressroute/index.yml) e la crittografia di rete virtuale è disponibile dal [la rete di Azure](../virtual-network/index.yml). |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| No  | Tutte le chiavi di crittografia sono per ogni istanza del servizio e servizio gestito. |
| Crittografia a livello di colonna (Azure Data Services)| N/D | |
| Chiamate API crittografate| Sì | Gestione piano chiamate vengono effettuate tramite [Azure Resource Manager](../azure-resource-manager/index.yml) su TLS. È necessario un token JSON Web (JWT) valido.  Le chiamate di piano dati è possibile proteggere con TLS e uno dei meccanismi di autenticazione supportati (ad esempio, il certificato client o JWT).
 |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| No  | |
| Supportare l'inserimento di rete virtuale| Sì | |
| Isolamento di rete e supporto firewall| Sì | Uso di gruppi di sicurezza di rete (NSG) e il Gateway applicazione di Azure (o altro dispositivo di software) rispettivamente. |
| Supporto di tunneling forzato| Sì | Sulla rete di Azure è disponibile il tunneling forzato. |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Sì | |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | |
| Authorization| Sì | |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo| Sì | [Azure log attività di monitoraggio](../azure-monitor/platform/activity-logs-overview.md) |
| Controllo e registrazione di piano dati| Sì | [Log di diagnostica Azure Monitor](../azure-monitor/platform/diagnostic-logs-overview.md) ed eventualmente [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Sì | Uso di [DevOps Resource Kit di gestione API di Azure](https://aka.ms/apimdevops) |

## <a name="vulnerability-scans-false-positives"></a>Una vulnerabilità analizza i falsi positivi

Questa sezione vengono illustrate le vulnerabilità comuni, che non influiscono sulla gestione API di Azure.

| Vulnerabilità               | DESCRIZIONE                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed è una vulnerabilità nell'implementazione dell'estensione SessionTicket TLS disponibile in alcuni prodotti F5. Consente la fuga ("sanguinare") di un massimo di 31 byte di dati dalla memoria non inizializzata. Ciò è dovuto lo stack TLS spaziatura interna di un ID di sessione, passato dal client, con i dati per renderlo 32-bit lungo. |