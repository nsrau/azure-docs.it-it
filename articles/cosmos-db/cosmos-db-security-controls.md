---
title: Controlli di sicurezza per Azure Cosmos DB
description: Ottenere un elenco di controllo dei controlli di sicurezza, ad esempio la rete, il monitoraggio, l'identità e la protezione dei dati, per valutare Azure Cosmos DB
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.openlocfilehash: 1ac4d12f58977497642cdb0706ab7e85e9a4db64
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/13/2020
ms.locfileid: "75913083"
---
# <a name="security-controls-for-azure-cosmos-db"></a>Controlli di sicurezza per Azure Cosmos DB

Questo articolo descrive i controlli di sicurezza incorporati in Azure Cosmos DB.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Sì |  |
| Supporto di VNet Injection| Sì | Con l'endpoint del servizio VNet è possibile configurare un account Azure Cosmos DB per consentire l'accesso solo da una subnet specifica di una rete virtuale (VNet). È anche possibile combinare l'accesso VNet con le regole del firewall. Per altre informazioni, vedere [accedere Azure Cosmos DB da reti virtuali](VNet-service-endpoint.md). |
| Isolamento rete e supporto firewall| Sì | Con il supporto del firewall, è possibile configurare l'account Azure Cosmos per consentire l'accesso solo da un set di indirizzi IP approvato, un intervallo di indirizzi IP e/o servizi cloud. Per altre informazioni, vedere [configurare il firewall IP in Azure Cosmos DB](how-to-configure-firewall.md).|
| Supporto del tunneling forzato| Sì | Può essere configurato sul lato client nel VNet in cui si trovano le macchine virtuali.   |

## <a name="monitoring--logging"></a>Monitoraggio & registrazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Vengono registrate tutte le richieste inviate a Azure Cosmos DB. Il [monitoraggio di Azure](../azure-monitor/overview.md), le metriche di Azure e la registrazione di controllo di Azure sono supportate.  È possibile registrare le informazioni corrispondenti alle richieste del piano dati, le statistiche di runtime delle query, il testo della query, le richieste MongoDB. È anche possibile configurare gli avvisi. |
| Registrazione e controllo del piano di gestione e controllo| Sì | Log attività di Azure per le operazioni a livello di account, ad esempio firewall, reti virtuali, accesso alle chiavi e IAM. |
| Registrazione e controllo del piano dati | Sì | Registrazione del monitoraggio della diagnostica per operazioni a livello di contenitore come create container, provisioning della velocità effettiva, indicizzazione dei criteri e operazioni CRUD sui documenti. |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Autenticazione| Sì | Sì a livello di account del database; a livello di piano dati Cosmos DB usa i token delle risorse e l'accesso alla chiave. |
| Autorizzazione| Sì | Supportato nell'account Azure Cosmos con chiavi master (primarie e secondarie) e token di risorsa. È possibile ottenere l'accesso in lettura/scrittura o in sola lettura ai dati con chiavi master. I token delle risorse consentono un accesso limitato al tempo a risorse quali documenti e contenitori. |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server: chiavi gestite da Microsoft | Sì | Tutti i database e i backup di Azure Cosmos sono crittografati per impostazione predefinita. vedere [crittografia dei dati in Azure Cosmos DB](database-encryption-at-rest.md). |
| Crittografia lato server inattiva: chiavi gestite dal cliente (BYOK) | Sì | Vedere [configurare le chiavi gestite dal cliente per l'account Azure Cosmos DB](how-to-setup-cmk.md)  |
| Crittografia a livello di colonna (servizi dati di Azure)| Sì | Solo nell'API Tables Premium. Non tutte le API supportano questa funzionalità. Vedere [Introduzione a Azure Cosmos DB: API tabella](table-introduction.md). |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | Tutti i dati Azure Cosmos DB vengono crittografati in transito. |
| Chiamate API crittografate| Sì | Tutte le connessioni a Azure Cosmos DB supportano HTTPS. Azure Cosmos DB supporta anche TLS 1,2.<br>È possibile imporre una versione minima di TLS sul lato server. A tale scopo, contattare [cosmosdbpm@microsoft.com](maito:cosmosdbpm@microsoft.com]). |

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