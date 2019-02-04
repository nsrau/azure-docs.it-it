---
title: Firewall IP per account Azure Cosmos
description: Informazioni su come proteggere i dati di Azure Cosmos DB usando i criteri di controllo di accesso agli indirizzi IP per il supporto del firewall.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: 7cc8e46c841b6e385d1a3b442fdb63d465486af8
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55477727"
---
# <a name="ip-firewall-in-azure-cosmos-db"></a>Firewall IP in Azure Cosmos DB

Per proteggere i dati archiviati in un account personale, Azure Cosmos DB supporta un modello di autorizzazione basato su segreto che usa un codice di autenticazione messaggi basato su hash (HMAC, Hash-based Message Authentication Code). Azure Cosmos DB supporta anche i controlli di accesso IP per il supporto del firewall in ingresso. Questo modello è simile alle regole del firewall di un sistema di database tradizionale e offre un livello aggiuntivo di sicurezza per l'account. Con i firewall è possibile configurare un account Azure Cosmos in modo che sia accessibile solo da un set di computer e/o servizi cloud approvato. Per l'accesso ai dati archiviati nel database Azure Cosmos da questi set di computer e servizi approvati, è comunque necessario che il chiamante presenti un token di autorizzazione valido.

## <a id="ip-access-control-overview"></a>Panoramica del controllo di accesso IP

Per impostazione predefinita, un account Azure Cosmos è accessibile da Internet purché la richiesta sia accompagnata da un token di autorizzazione valido. Per configurare il controllo di accesso IP basato su criteri, l'utente deve fornire il set di indirizzi IP o di intervalli di indirizzi IP in formato CIDR (Classless Inter-Domain Routing), in modo che venga incluso come elenco di IP client consentiti per l'accesso a un determinato account Azure Cosmos. Se viene applicata questa configurazione, tutte le richieste provenienti da computer non inclusi in questo elenco ricevono una risposta 403 (Accesso negato). Quando si usa il firewall IP, è consigliabile consentire al portale di Azure di accedere all'account. In questo modo, infatti, è possibile usare Esplora dati e recuperare le metriche relative all'account visualizzate nel portale di Azure.

È possibile combinare il firewall basato su IP con il controllo dell'accesso basato su subnet e rete virtuale, in modo da limitare l'accesso a qualsiasi origine che abbia un indirizzo IP pubblico e/o da una subnet specifica all'interno della rete virtuale. Per altre informazioni sull'uso del controllo dell'accesso basato su subnet e rete virtuale, vedere [Accedere a risorse di Azure Cosmos DB da reti virtuali](vnet-service-endpoint.md).

Per riepilogare, è sempre necessario un token di autorizzazione per accedere a un account Azure Cosmos. Se il firewall IP e gli elenchi di controllo di accesso della rete virtuale non sono configurati, è possibile accedere all'account Azure Cosmos con il token di autorizzazione. Dopo aver configurato il firewall IP e/o gli elenchi di controllo di accesso della rete virtuale nell'account Azure Cosmos, solo le richieste provenienti dalle origini specificate (e con il token di autorizzazione) ottengono risposte valide. 

## <a name="next-steps"></a>Passaggi successivi

È ora possibile configurare il firewall IP o un endpoint di servizio di rete virtuale per l'account personale consultando gli articoli seguenti:

* [Come configurare il firewall IP per l'account Azure Cosmos](how-to-configure-firewall.md)
* [Accedere alle risorse di Azure Cosmos DB dalle reti virtuali](vnet-service-endpoint.md)
* [Per configurare l'endpoint del servizio della rete virtuale per l'account Azure Cosmos](how-to-configure-vnet-service-endpoint.md)




