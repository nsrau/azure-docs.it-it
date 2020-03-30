---
title: Controlli di sicurezza per Gestione API di AzureSecurity controls for Azure API Management
description: Elenco di controllo dei controlli di sicurezza per la valutazione di Gestione API
services: api-management
author: vladvino
ms.service: api-management
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: vlvinogr
ms.openlocfilehash: 670050efe01fb658fab52a43914f193e9798b828
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751133"
---
# <a name="security-controls-for-api-management"></a>Controlli di sicurezza per Gestione APISecurity controls for API Management

Questo articolo documenta i controlli di sicurezza integrati in Gestione API.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|---|---|--|--|
| Supporto dell'endpoint di servizioService endpoint support| No | |  |
| Supporto per l'inserimento di vNet| Sì | |  |
| Isolamento della rete e supporto firewall| Sì | Usando i gruppi di sicurezza di rete (NSG) e il gateway applicazione di Azure (o altre appliance software) rispettivamente. |  |
| Supporto per il tunneling forzato| Sì | Sulla rete di Azure è disponibile il tunneling forzato. |  |

## <a name="monitoring--logging"></a>Monitoraggio della registrazione dei &

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Supporto per il monitoraggio di Azure (analisi dei log, informazioni dettagliate sulle app e così via)Azure monitoring support (Log analytics, App insights, etc.)| Sì | | |
| Controllo e gestione del controllo e dell'audit degli aerei| Sì | [Log attività di Monitoraggio di AzureAzure Monitor activity logs](../azure-monitor/platform/platform-logs-overview.md) | |
| Registrazione e controllo dei piani dati| Sì | [Log di diagnostica](../azure-monitor/platform/platform-logs-overview.md) di Monitoraggio di Azure e (facoltativamente) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  | |


## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Authentication| Sì | |  |
| Autorizzazione| Sì | |  |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|---|---|--|--|
| Crittografia lato server inattivi: chiavi gestite da MicrosoftServer-side encryption at rest: Microsoft-managed keys | Sì | I dati sensibili, ad esempio certificati, chiavi e valori con nome segreto, vengono crittografati con chiavi gestite dal servizio per ogni istanza del servizio. |  |
| Crittografia lato server inattivi: chiavi gestite dal cliente (BYOK)Server-side encryption at rest: customer-managed keys (BYOK) | No | Tutte le chiavi di crittografia sono per istanza del servizio e sono gestite dal servizio. |  |
| Crittografia a livello di colonna (Azure Data Services)Column level encryption (Azure Data Services)| N/D | |  |
| Crittografia in transito (ad esempio la crittografia ExpressRoute, nella crittografia della rete virtuale e crittografia VNet-VNet)Encryption in transit (such as ExpressRoute encryption, in VNet encryption, and VNet-VNet encryption)| Sì | [La crittografia Express Route](../expressroute/index.yml) e VNet viene fornita dalla rete di [Azure.](../virtual-network/index.yml) |  |
| Chiamate API crittografate| Sì | Le chiamate al piano di gestione vengono effettuate tramite [Azure Resource Manager](../azure-resource-manager/index.yml) tramite TLS. È necessario un token JSON Web (JWT) valido.  Le chiamate ai piani dati possono essere protette con TLS e con uno dei meccanismi di autenticazione supportati (ad esempio, certificato client o JWT). |   |
 |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Uso del DevOps di Gestione API di AzureUsing the [Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) |  |

## <a name="vulnerability-scans-false-positives"></a>La vulnerabilità analizza i falsi positivi

In questa sezione vengono documentate le vulnerabilità comuni che non influiscono su Gestione API di Azure.This section document common vulnerabilities that do not affect Azure API Management.

| Vulnerabilità               | Descrizione                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed è vulnerabilità nell'implementazione dell'estensione SessionTicket TLS trovata in alcuni prodotti F5. Permette la perdita ("bleeding") fino a 31 byte di dati dalla memoria non inizializzata. Ciò è causato dalla spaziatura interna dello stack TLS un ID di sessione, passato dal client, con dati per renderlo lungo 32 bit. |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui controlli di sicurezza incorporati nei servizi di Azure.Learn more about the [built-in security controls across Azure services](../security/fundamentals/security-controls.md).