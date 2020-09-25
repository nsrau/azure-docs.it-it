---
title: Sicurezza di rete per l'inoltro di Azure
description: Questo articolo descrive come usare le regole del firewall IP ed endpoint privati con il servizio di inoltro di Azure.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 3aa3ffd119f65ec5181b0c382472cc4ef3c8bac4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91263726"
---
# <a name="network-security-for-azure-relay"></a>Sicurezza di rete per l'inoltro di Azure 
Questo articolo descrive come usare le funzionalità di sicurezza seguenti con il servizio di inoltro di Azure: 

- Regole del firewall IP (anteprima)
- Endpoint privati (anteprima)

> [!NOTE]
> Il servizio di inoltro di Azure non supporta gli endpoint servizio di rete. 


## <a name="ip-firewall"></a>Firewall IP 
Per impostazione predefinita, gli spazi dei nomi di Inoltro sono accessibili da Internet, purché la richiesta sia accompagnata da un'autenticazione e da un'autorizzazione valide. Con un firewall per gli indirizzi IP, è possibile limitare ulteriormente l'accesso a un set di indirizzi IPv4 o a intervalli di indirizzi IPv4 in notazione [CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Questa funzionalità è utile negli scenari in cui Inoltro di Azure deve essere accessibile solo da siti noti specifici. Le regole del firewall consentono di configurare regole di ammissione del traffico proveniente da indirizzi IPv4 specifici. Se ad esempio si usa Inoltro con [Azure ExpressRoute](../expressroute/expressroute-faqs.md#supported-services), è possibile creare una **regola del firewall** per consentire traffico solo dagli indirizzi IP dell'infrastruttura locale. 

Le regole del firewall IP vengono applicate a livello di spazio dei nomi di inoltro. Vengono pertanto applicate a tutte le connessioni provenienti dai client con qualsiasi protocollo supportato. Qualsiasi tentativo di connessione da un indirizzo IP che non corrisponde a una regola IP consentita nello spazio dei nomi di inoltro viene rifiutato come non autorizzato. Nella risposta non viene fatto riferimento alla regola IP. Le regole del filtro IP vengono applicate in ordine e la prima regola corrispondente all'indirizzo IP determina l'azione di accettazione o rifiuto.

Per ulteriori informazioni, vedere [come configurare un firewall IP per uno spazio dei nomi di inoltro](ip-firewall-virtual-networks.md)

## <a name="private-endpoints"></a>Endpoint privati

Il **servizio Collegamento privato** di Azure consente di accedere ai servizi di Azure, come ad esempio a Inoltro di Azure, al bus di servizio di Azure, ad Hub eventi di Azure, ad Archiviazione di Azure e ad Azure Cosmos DB, nonché ai servizi di clienti/partner ospitati in Azure tramite un endpoint privato nella rete virtuale. Per altre informazioni, vedere [Che cos'è Collegamento privato di Azure? (Anteprima)](../private-link/private-link-overview.md).

Un **endpoint privato** è un'interfaccia di rete che consente ai carichi di lavoro in esecuzione in una rete virtuale di connettersi privatamente e in modo sicuro a un servizio che dispone di una **risorsa di collegamento privato** (ad esempio, uno spazio dei nomi di Inoltro). L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute, connessioni VPN oppure indirizzi IP pubblici. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. È possibile fornire un livello di granularità nel controllo di accesso consentendo le connessioni a spazi dei nomi di Inoltro di Azure specifici.

> [!NOTE]
> Questa funzionalità è attualmente disponibile in **anteprima**. 

Per ulteriori informazioni, vedere [come configurare gli endpoint privati](private-link-service.md)


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Come configurare il firewall IP](ip-firewall-virtual-networks.md)
- [Come configurare gli endpoint privati](private-link-service.md)
