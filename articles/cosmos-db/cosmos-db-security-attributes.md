---
title: Attributi di sicurezza per Azure Cosmos DB
description: Elenco di controllo degli attributi di sicurezza per la valutazione Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 50711f8675e1b8aca6b9f90925ea921d22020ddd
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442548"
---
# <a name="security-attributes-for-azure-cosmos-db"></a>Attributi di sicurezza per Azure Cosmos DB

Questo articolo descrive gli attributi di sicurezza incorporati in Azure Cosmos DB.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia | Sì | Tutti i backup e i database di Cosmos DB vengono crittografati per impostazione predefinita. vedere [crittografia dei dati in Azure Cosmos DB](database-encryption-at-rest.md). La crittografia lato server con chiavi gestite dal cliente non è supportata. |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | Tutti i dati Azure Cosmos DB vengono crittografati in transito. |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| No |  |
| Crittografia a livello di colonna (servizi dati di Azure)| Sì | Solo nell'API Tables Premium. Non tutte le API supportano questa funzionalità. Vedere [Introduzione a Azure Cosmos DB: API Tabella](table-introduction.md). |
| Chiamate API crittografate| Yes | Tutte le connessioni a Azure Cosmos DB supportano HTTPS. Azure Cosmos DB supporta anche le connessioni TLS 1,2, ma questo non è ancora applicato. Se i clienti disattivano TLS di livello inferiore al termine, possono garantire la connessione a Cosmos DB.  |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Sì |  |
| Supporto di VNet Injection| Sì | Con l'endpoint del servizio VNet è possibile configurare un account Azure Cosmos DB per consentire l'accesso solo da una subnet specifica di una rete virtuale (VNet). È anche possibile combinare l'accesso VNet con le regole del firewall.  Vedere [accedere Azure Cosmos DB da reti virtuali](VNet-service-endpoint.md). |
| Isolamento rete e supporto del firewall| Sì | Con il supporto del firewall, è possibile configurare l'account Azure Cosmos per consentire l'accesso solo da un set di indirizzi IP approvato, un intervallo di indirizzi IP e/o servizi cloud. Vedere [configurare il firewall IP in Azure Cosmos DB](how-to-configure-firewall.md).|
| Supporto del tunneling forzato| Sì | Può essere configurato sul lato client nel VNet in cui si trovano le macchine virtuali.   |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Vengono registrate tutte le richieste inviate a Azure Cosmos DB. Il [monitoraggio di Azure](../azure-monitor/overview.md), le metriche di Azure e la registrazione di controllo di Azure sono supportate.  È possibile registrare le informazioni corrispondenti alle richieste del piano dati, le statistiche di runtime delle query, il testo della query, le richieste MongoDB. È anche possibile configurare gli avvisi. |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | Sì a livello di account del database; a livello di piano dati Cosmos DB usa i token delle risorse e l'accesso alla chiave. |
| Authorization| Sì | Supportato nell'account Azure Cosmos con chiavi master (primarie e secondarie) e token di risorsa. È possibile ottenere l'accesso in lettura/scrittura o in sola lettura ai dati con chiavi master. I token delle risorse consentono un accesso limitato al tempo a risorse quali documenti e contenitori. |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Sì | Log attività di Azure per le operazioni a livello di account, ad esempio firewall, reti virtuali, accesso alle chiavi e IAM. |
| Registrazione e controllo del piano dati | Sì | Registrazione del monitoraggio della diagnostica per operazioni a livello di contenitore come create container, provisioning della velocità effettiva, indicizzazione dei criteri e operazioni CRUD sui documenti. |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| No  | | 

## <a name="additional-security-attributes-for-cosmos-db"></a>Attributi di sicurezza aggiuntivi per Cosmos DB

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Condivisione risorse tra le origini (CORS) | Yes | Vedere [configurare la condivisione di risorse tra le origini (CORS)](how-to-configure-cross-origin-resource-sharing.md). |
