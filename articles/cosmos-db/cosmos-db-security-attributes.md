---
title: Attributi di sicurezza per Azure Cosmos DB
description: Un elenco di controllo degli attributi di protezione per la valutazione di Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: ffeb60f5476a540e3da46a82c240b0dda9aa6be2
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480471"
---
# <a name="security-attributes-for-azure-cosmos-db"></a>Attributi di sicurezza per Azure Cosmos DB

Questo articolo sono descritti gli attributi comuni di sicurezza integrati di Azure Cosmos DB.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/no | Note |
|---|---|--|
| Crittografia dei dati inattivi (ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia) | Yes | Tutti i database di Cosmos DB e i backup vengono crittografati per impostazione predefinita. visualizzare [crittografia dei dati in Azure Cosmos DB](database-encryption-at-rest.md). Crittografia lato server con chiavi gestite dal cliente non è supportata. |
| Crittografia in transito (ad esempio la crittografia di ExpressRoute, nella crittografia di rete virtuale e la crittografia di rete)| Yes | Tutti i dati di Azure Cosmos DB vengono crittografati in transito. |
| Gestione delle chiavi di crittografia (CMK, BYOK e così via)| No |  |
| Crittografia a livello di colonna (Servizi dati di Azure)| Yes | Solo nell'API tabelle Premium. Non tutte le API supportano questa funzionalità. Vedere [Introduzione ad Azure Cosmos DB: API tabelle](table-introduction.md). |
| Chiamate API crittografate| Yes | Tutte le connessioni ad Azure Cosmos DB supportano HTTPS. Azure Cosmos DB supporta anche le connessioni TLS 1.2, ma questo non viene ancora applicato. Se i clienti Disattiva livello inferiore TLS estremità, è possibile verificare che per connettersi a Cosmos DB.  |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/no | Note |
|---|---|--|
| Supporto endpoint di servizio| Yes |  |
| Supporto aggiunta rete virtuale| Yes | Con l'endpoint del servizio rete virtuale, è possibile configurare un account Azure Cosmos DB per consentire l'accesso solo da una subnet specifica di una rete virtuale (VNet). È anche possibile combinare l'accesso di rete virtuale con le regole del firewall.  Visualizzare [Access Azure Cosmos DB da reti virtuali](vnet-service-endpoint.md). |
| Supporto per l'isolamento della rete e Firewalling| Yes | Con supporto del firewall, è possibile configurare l'account Azure Cosmos per consentire l'accesso solo da un set approvato di indirizzi IP, un intervallo di indirizzi IP e/o servizi cloud. Visualizzare [firewall configurare IP in Azure Cosmos DB](how-to-configure-firewall.md).|
| Supporto tunneling forzato | Yes | Può essere configurato sul lato client nella rete virtuale in cui si trovano le macchine virtuali.   |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/no | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Yes | Tutte le richieste inviate ad Azure Cosmos DB vengono registrate. [Monitoraggio di Azure](../azure-monitor/overview.md), sono supportate le metriche di Azure, la registrazione di controllo di Azure.  È possibile registrare le informazioni corrispondenti per le richieste dei piani dati, le statistiche di runtime di query, il testo della query, richieste di MongoDB. È anche possibile configurare avvisi. |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/no | Note|
|---|---|--|
| Authentication| Yes | Sì a livello di Account di Database; a livello di piano dati, Cosmos DB Usa i token delle risorse e l'accesso alla chiave. |
| Authorization| Yes | L'account Azure Cosmos con le chiavi Master (primaria e secondaria) e i token delle risorse supportate. È possibile ottenere lettura/scrittura o accesso in sola lettura ai dati con le chiavi master. I token delle risorse consentono l'accesso di tempo limitato alle risorse, ad esempio documenti e i contenitori. |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/no | Note|
|---|---|--|
| Piano di gestione/controllo - Registrazione e controllo| Yes | Log attività di Azure per le operazioni a livello di account, ad esempio i firewall, reti virtuali, chiavi di accesso e IAM. |
| Piano dati - Registrazione e controllo | Yes | Diagnostica di monitoraggio la registrazione per le operazioni a livello di contenitore, ad esempio crea contenitori, velocità effettiva di provisioning, l'indicizzazione criteri e le operazioni CRUD su documenti. |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| No  | | 

## <a name="additional-security-attributes-for-cosmos-db"></a>Attributi di sicurezza aggiuntive per Cosmos DB

| Attributo di sicurezza | Sì/no | Note|
|---|---|--|
| Cross Origin Resource Sharing (CORS) | Yes | Visualizzare [Cross-Origin Resource Sharing (CORS) configurare](how-to-configure-cross-origin-resource-sharing.md). |
