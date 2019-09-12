---
title: Controlli di sicurezza per gestione API di Azure
description: Elenco di controllo dei controlli di sicurezza per la valutazione di gestione API
services: api-management
author: msmbaldwin
manager: rkarlin
ms.service: api-management
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: e808f373ed3c977fb3263bc9e2e25bc602c7a7e1
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886798"
---
# <a name="security-controls-for-api-management"></a>Controlli di sicurezza per gestione API

Questo articolo descrive i controlli di sicurezza incorporati in gestione API.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| No | |
| Supporto di VNet Injection| Sì | |
| Isolamento rete e supporto del firewall| Sì | Usando i gruppi di sicurezza di rete (NSG) e applicazione Azure Gateway (o altro appliance software) rispettivamente. |
| Supporto del tunneling forzato| Sì | Sulla rete di Azure è disponibile il tunneling forzato. |

## <a name="monitoring--logging"></a>Monitoraggio & registrazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | |
| Registrazione e controllo del piano di gestione e controllo| Sì | [Log attività di monitoraggio di Azure](../azure-monitor/platform/activity-logs-overview.md) |
| Registrazione e controllo del piano dati| Sì | [Log di diagnostica di monitoraggio di Azure](../azure-monitor/platform/diagnostic-logs-overview.md) e, facoltativamente, [applicazione Azure informazioni dettagliate](../azure-monitor/app/app-insights-overview.md).  |

## <a name="identity"></a>identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | |
| Authorization| Sì | |

## <a name="data-protection"></a>Protezione dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server: Chiavi gestite da Microsoft | Sì | I dati sensibili, ad esempio i certificati, le chiavi e i valori con nome segreto, vengono crittografati con chiavi di istanza di servizio gestite dal servizio. |
| Crittografia lato server inattiva: chiavi gestite dal cliente (BYOK) | No | Tutte le chiavi di crittografia sono per istanza del servizio e sono gestite dal servizio. |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Yes | [Express Route](../expressroute/index.yml) e la crittografia VNet sono fornite da [rete di Azure](../virtual-network/index.yml). |
| Chiamate API crittografate| Sì | Le chiamate del piano di gestione vengono effettuate tramite [Azure Resource Manager](../azure-resource-manager/index.yml) su TLS. È necessario un token JSON Web (JWT) valido.  Le chiamate del piano dati possono essere protette con TLS e uno dei meccanismi di autenticazione supportati (ad esempio, il certificato client o JWT).
 |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Uso del [Resource Kit DevOps di gestione API di Azure](https://aka.ms/apimdevops) |

## <a name="vulnerability-scans-false-positives"></a>Analisi della vulnerabilità falsi positivi

Questa sezione documenta le vulnerabilità comuni che non influiscono sulla gestione API di Azure.

| Vulnerabilità               | Descrizione                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed è una vulnerabilità nell'implementazione dell'estensione TLS SessionTicket presente in alcuni prodotti F5. Consente la perdita ("emorragia") di un massimo di 31 byte di dati dalla memoria non inizializzata. Questo problema si verifica perché lo stack TLS riempie un ID di sessione, passato dal client, con dati per renderlo 32 bit. |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).