---
title: Sicurezza di rete per l'inoltro di Azure
description: Questo articolo descrive come configurare l'accesso da endpoint privati
services: service-bus-relay
author: spelluru
ms.service: service-bus-relay
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: spelluru
ms.openlocfilehash: d0a0d03680877ae78f5af939f8d47e4e426abadb
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984772"
---
# <a name="network-security-for-azure-relay"></a>Sicurezza di rete per l'inoltro di Azure 
Questo articolo descrive come usare le funzionalità di sicurezza seguenti con il servizio di inoltro di Azure: 

- Regole del firewall IP (anteprima)
- Endpoint privati (anteprima)

> [!NOTE]
> Il servizio di inoltro di Azure non supporta gli endpoint servizio di rete. 


## <a name="ip-firewall"></a>Firewall IP 
Per impostazione predefinita, gli spazi dei nomi di inoltro sono accessibili da Internet, purché la richiesta venga fornita con autenticazione e autorizzazione valide. Con il firewall IP, è possibile limitarlo ulteriormente a un set di indirizzi IPv4 o a intervalli di indirizzi IPv4 in notazione [CIDR (instradamento tra domini senza classi)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Questa funzionalità è utile negli scenari in cui il servizio di inoltro di Azure deve essere accessibile solo da determinati siti noti. Le regole del firewall consentono di configurare regole per accettare il traffico originato da indirizzi IPv4 specifici. Se ad esempio si usa l'inoltro con [Azure Express Route](/azure/expressroute/expressroute-faqs#supported-services), è possibile creare una **regola del firewall** per consentire il traffico solo da indirizzi IP dell'infrastruttura locale. 

Le regole del firewall IP vengono applicate a livello di spazio dei nomi di inoltro. Vengono pertanto applicate a tutte le connessioni provenienti dai client con qualsiasi protocollo supportato. Qualsiasi tentativo di connessione da un indirizzo IP che non corrisponde a una regola IP consentita nello spazio dei nomi di inoltro viene rifiutato come non autorizzato. Nella risposta non viene fatto riferimento alla regola IP. Le regole del filtro IP vengono applicate in ordine e la prima regola corrispondente all'indirizzo IP determina l'azione di accettazione o rifiuto.

Per ulteriori informazioni, vedere [come configurare un firewall IP per uno spazio dei nomi di inoltro](ip-firewall-virtual-networks.md)

## <a name="private-endpoints"></a>Endpoint privati

Il **servizio di collegamento privato** di Azure consente di accedere ai servizi di Azure (ad esempio inoltro di Azure, bus di servizio di Azure, Hub eventi di Azure, archiviazione di azure e Azure Cosmos DB) e ai servizi cliente/partner ospitati di Azure tramite un endpoint privato nella rete virtuale. Per altre informazioni, vedere [Che cos'è Collegamento privato di Azure? (Anteprima)](../private-link/private-link-overview.md).

Un **endpoint privato** è un'interfaccia di rete che consente ai carichi di lavoro in esecuzione in una rete virtuale di connettersi privatamente e in modo sicuro a un servizio che dispone di una **risorsa di collegamento privato** (ad esempio, uno spazio dei nomi di inoltro). L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute, connessioni VPN o indirizzi IP pubblici. Il traffico tra la rete virtuale e il servizio attraversa la rete dorsale Microsoft eliminando l'esposizione dalla rete Internet pubblica. È possibile fornire un livello di granularità nel controllo di accesso consentendo le connessioni a spazi dei nomi di inoltro di Azure specifici.

> [!NOTE]
> Questa funzionalità è attualmente disponibile in **Anteprima**. 

Per ulteriori informazioni, vedere [come configurare gli endpoint privati](private-link-service.md)


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Come configurare il firewall IP](ip-firewall-virtual-networks.md)
- [Come configurare gli endpoint privati](private-link-service.md)