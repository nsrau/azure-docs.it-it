---
title: Controlli di sicurezza per Azure Cosmos DB
description: Elenco di controllo dei controlli di sicurezza per la valutazione Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 20cdfc61a4cdfe5263e48d049aab14cad2458b06
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886366"
---
# <a name="security-controls-for-azure-cosmos-db"></a>Controlli di sicurezza per Azure Cosmos DB

Questo articolo descrive i controlli di sicurezza incorporati in Azure Cosmos DB.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Sì |  |
| Supporto di VNet Injection| Sì | Con l'endpoint del servizio VNet è possibile configurare un account Azure Cosmos DB per consentire l'accesso solo da una subnet specifica di una rete virtuale (VNet). È anche possibile combinare l'accesso VNet con le regole del firewall.  Vedere [accedere Azure Cosmos DB da reti virtuali](VNet-service-endpoint.md). |
| Isolamento rete e supporto del firewall| Sì | Con il supporto del firewall, è possibile configurare l'account Azure Cosmos per consentire l'accesso solo da un set di indirizzi IP approvato, un intervallo di indirizzi IP e/o servizi cloud. Vedere [configurare il firewall IP in Azure Cosmos DB](how-to-configure-firewall.md).|
| Supporto del tunneling forzato| Sì | Può essere configurato sul lato client nel VNet in cui si trovano le macchine virtuali.   |

## <a name="monitoring--logging"></a>Monitoraggio & registrazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Vengono registrate tutte le richieste inviate a Azure Cosmos DB. Il [monitoraggio di Azure](../azure-monitor/overview.md), le metriche di Azure e la registrazione di controllo di Azure sono supportate.  È possibile registrare le informazioni corrispondenti alle richieste del piano dati, le statistiche di runtime delle query, il testo della query, le richieste MongoDB. È anche possibile configurare gli avvisi. |
| Registrazione e controllo del piano di gestione e controllo| Yes | Log attività di Azure per le operazioni a livello di account, ad esempio firewall, reti virtuali, accesso alle chiavi e IAM. |
| Registrazione e controllo del piano dati | Yes | Registrazione del monitoraggio della diagnostica per operazioni a livello di contenitore come create container, provisioning della velocità effettiva, indicizzazione dei criteri e operazioni CRUD sui documenti. |

## <a name="identity"></a>identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | Sì a livello di account del database; a livello di piano dati Cosmos DB usa i token delle risorse e l'accesso alla chiave. |
| Authorization| Sì | Supportato nell'account Azure Cosmos con chiavi master (primarie e secondarie) e token di risorsa. È possibile ottenere l'accesso in lettura/scrittura o in sola lettura ai dati con chiavi master. I token delle risorse consentono un accesso limitato al tempo a risorse quali documenti e contenitori. |

## <a name="data-protection"></a>Protezione dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server: Chiavi gestite da Microsoft | Sì | Tutti i database Cosmos e i backup sono crittografati per impostazione predefinita. vedere [crittografia dei dati in Azure Cosmos DB](database-encryption-at-rest.md). La crittografia lato server con chiavi gestite dal cliente non è supportata. |
| Crittografia lato server inattiva: chiavi gestite dal cliente (BYOK) | No |  |
| Crittografia a livello di colonna (servizi dati di Azure)| Yes | Solo nell'API Tables Premium. Non tutte le API supportano questa funzionalità. Vedere [Introduzione a Azure Cosmos DB: API Tabella](table-introduction.md). |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | Tutti i dati Azure Cosmos DB vengono crittografati in transito. |
| Chiamate API crittografate| Yes | Tutte le connessioni a Azure Cosmos DB supportano HTTPS. Azure Cosmos DB supporta anche le connessioni TLS 1,2, ma questo non è ancora applicato. Se i clienti disattivano TLS di livello inferiore al termine, possono garantire la connessione a Cosmos DB.  |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| No  | | 

## <a name="additional-security-controls-for-cosmos-db"></a>Controlli di sicurezza aggiuntivi per Cosmos DB

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Condivisione risorse tra le origini (CORS) | Sì | Vedere [configurare la condivisione di risorse tra le origini (CORS)](how-to-configure-cross-origin-resource-sharing.md). |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).