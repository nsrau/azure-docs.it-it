---
title: Sicurezza di rete per il bus di servizio di Azure
description: Questo articolo descrive le funzionalità di sicurezza di rete, ad esempio tag di servizio, regole del firewall IP, endpoint di servizio ed endpoint privati.
services: service-bus-messaging
documentationcenter: .net
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: aschhab
ms.openlocfilehash: 95f8c2a3b47b59bab7df909be43dacdb1f9c58f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479280"
---
# <a name="network-security-for-azure-service-bus"></a>Sicurezza di rete per il bus di servizio di Azure 
Questo articolo descrive come usare le funzionalità di sicurezza seguenti con il bus di servizio di Azure: 

- Tag di servizio
- Regole del firewall IP
- Endpoint servizio di rete
- Endpoint privati (anteprima)


## <a name="service-tags"></a>Tag di servizio
Un tag di servizio rappresenta un gruppo di prefissi di indirizzi IP da un determinato servizio di Azure. Microsoft gestisce i prefissi di indirizzo inclusi nel tag del servizio e aggiorna automaticamente il tag di servizio in base alla modifica degli indirizzi, riducendo al minimo la complessità degli aggiornamenti frequenti alle regole di sicurezza di rete. Per altre informazioni sui tag di servizio, vedere [Cenni preliminari sui tag di servizio](../virtual-network/service-tags-overview.md).

È possibile usare i tag di servizio per definire i controlli di accesso alla rete nei [gruppi di sicurezza di rete](../virtual-network/security-overview.md#security-rules) o nel firewall di [Azure](../firewall/service-tags.md). Usare i tag del servizio al posto di indirizzi IP specifici quando si creano le regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, **ServiceBus**) nel campo di *origine* o di *destinazione* appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente.

| Tag servizio | Scopo | È possibile usare in ingresso o in uscita? | Può essere regionale? | È possibile usare con il firewall di Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ServiceBus** | Traffico del bus di servizio di Azure che usa il livello di servizio Premium. | In uscita | Sì | Sì |


## <a name="ip-firewall"></a>Firewall IP 
Per impostazione predefinita, gli spazi dei nomi del bus di servizio sono accessibili da Internet, purché la richiesta venga fornita con autenticazione e autorizzazione valide. Con il firewall IP, è possibile limitarlo ulteriormente a un set di indirizzi IPv4 o a intervalli di indirizzi IPv4 in notazione [CIDR (instradamento tra domini senza classi)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Questa funzionalità è utile negli scenari in cui il bus di servizio di Azure deve essere accessibile solo da determinati siti noti. Le regole del firewall consentono di configurare regole per accettare il traffico originato da indirizzi IPv4 specifici. Ad esempio, se si usa il bus di servizio con [Azure Express Route] [Express-Route], è possibile creare una **regola del firewall** per consentire il traffico solo da indirizzi IP o indirizzi IP dell'infrastruttura locale di un gateway NAT aziendale. 

Le regole del firewall IP vengono applicate al livello dello spazio dei nomi del bus di servizio. Vengono pertanto applicate a tutte le connessioni provenienti dai client con qualsiasi protocollo supportato. Qualsiasi tentativo di connessione proveniente da un indirizzo IP che non corrisponde a una regola di indirizzi IP consentiti nello spazio dei nomi di bus di servizio di Microsoft Azure viene rifiutato come non autorizzato. Nella risposta non viene fatto riferimento alla regola IP. Le regole del filtro IP vengono applicate in ordine e la prima regola corrispondente all'indirizzo IP determina l'azione di accettazione o rifiuto.

Per ulteriori informazioni, vedere [come configurare un firewall IP per uno spazio dei nomi del bus di servizio](service-bus-ip-filtering.md)

## <a name="network-service-endpoints"></a>Endpoint servizio di rete
L'integrazione di endpoint del servizio bus di servizio con [rete virtuale (VNet)](service-bus-service-endpoints.md) consente di accedere in modo sicuro alle funzionalità di messaggistica da carichi di lavoro come le macchine virtuali associate a reti virtuali, con il percorso del traffico di rete protetto su entrambe le estremità.

Una volta configurato per essere associato ad almeno un endpoint del servizio della subnet della rete virtuale, lo spazio dei nomi del bus di servizio corrispondente non accetterà più traffico se non dalle reti virtuali autorizzate. Dal punto di vista della rete virtuale, l'associazione di uno spazio dei nomi del bus di servizio a un endpoint del servizio consente di configurare un tunnel di rete isolato dalla subnet della rete virtuale al servizio di messaggistica.

Il risultato è una relazione privata e isolata tra i carichi di lavoro associati alla subnet e lo spazio dei nomi del bus di servizio corrispondente, nonostante l'indirizzo di rete osservabile dell'endpoint del servizio di messaggistica sia in un intervallo di IP pubblici.

> [!IMPORTANT]
> Le reti virtuali sono supportate solo negli spazi dei nomi del bus di servizio di [livello Premium](service-bus-premium-messaging.md).
> 
> Quando si usano gli endpoint del servizio VNet con il bus di servizio, è consigliabile non abilitare questi endpoint nelle applicazioni che combinano gli spazi dei nomi del bus di servizio di livello standard e Premium. Poiché il livello standard non supporta reti virtuali. L'endpoint è limitato solo agli spazi dei nomi del livello Premium.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Scenari di sicurezza avanzati resi possibili dall'integrazione della rete virtuale 

Per le soluzioni con meccanismi di sicurezza rigidi e suddivisi in vari compartimenti e per le quali le subnet della rete virtuale offrono la segmentazione tra i servizi nei diversi compartimenti, sono in genere comunque necessari percorsi di comunicazione tra i servizi che risiedono in tali compartimenti.

Qualsiasi route IP immediata tra i compartimenti, incluse quelle destinate al traffico HTTPS su TCP/IP, comportano il rischio di sfruttamento delle vulnerabilità dal livello rete verso l'alto. I servizi di messaggistica offrono percorsi di comunicazione completamente isolati, in cui i messaggi vengono anche scritti su disco durante la transizione tra le parti coinvolte. I carichi di lavoro in due reti virtuali distinte associate alla stessa istanza del bus di servizio possono comunicare in modo efficiente e affidabile tramite messaggi, pur mantenendo l'integrità del confine di isolamento rete.
 
Questo significa che le soluzioni cloud con requisiti di sicurezza elevati non solo possono ottenere l'accesso alle funzionalità di messaggistica asincrona scalabili e affidabili leader del settore di Azure, ma possono ora usare la messaggistica per creare percorsi di comunicazione tra compartimenti sicuri della soluzione, intrinsecamente più sicuri di quanto sia possibile con qualsiasi modalità di comunicazione peer-to-peer, inclusi i protocolli HTTPS e altri protocolli socket protetti da TLS.

### <a name="bind-service-bus-to-virtual-networks"></a>Associare il bus di servizio alle reti virtuali

Le *regole di rete virtuale* rappresentano una funzionalità di sicurezza del firewall che consente di definire se il server del bus di servizio di Azure accetta le comunicazioni inviate da una subnet specifica della rete virtuale.

L'associazione di uno spazio dei nomi del bus di servizio a una rete virtuale è un processo in due passaggi. Per prima cosa è necessario creare un **endpoint di servizio di rete virtuale** in una subnet di rete virtuale e abilitarlo per **Microsoft. ServiceBus** come illustrato nella [Panoramica dell'endpoint di servizio](service-bus-service-endpoints.md). Dopo aver aggiunto l'endpoint del servizio, è necessario associare lo spazio dei nomi del bus di servizio all'endpoint con una **regola di rete virtuale**.

La regola di rete virtuale è un'associazione tra lo spazio dei nomi del bus di servizio e una subnet della rete virtuale. Fino a quando esiste la regola, a tutti i carichi di lavoro associati alla subnet viene concesso l'accesso allo spazio dei nomi del bus di servizio. Il bus di servizio non stabilisce mai direttamente connessioni in uscita e non deve ottenere l'accesso, quindi non ottiene mai l'accesso alla subnet abilitando questa regola.

Per ulteriori informazioni, vedere [come configurare gli endpoint del servizio rete virtuale per uno spazio dei nomi del bus di servizio](service-bus-service-endpoints.md)

## <a name="private-endpoints"></a>Endpoint privati

Il servizio di collegamento privato di Azure consente di accedere ai servizi di Azure (ad esempio, il bus di servizio di Azure, archiviazione di Azure e Azure Cosmos DB) e i servizi cliente/partner ospitati in Azure tramite un **endpoint privato** nella rete virtuale.

Un endpoint privato è un'interfaccia di rete che si connette privatamente e in modo sicuro a un servizio basato sul collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute o connessioni VPN oppure indirizzi IP pubblici. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. È possibile connettersi a un'istanza di una risorsa di Azure, garantendo il massimo livello di granularità nel controllo di accesso.

Per altre informazioni, vedere [Che cos'è Collegamento privato di Azure?](../private-link/private-link-overview.md).

> [!NOTE]
> Questa funzionalità è supportata con il livello **Premium** del bus di servizio di Azure. Per ulteriori informazioni sul livello Premium, vedere l'articolo relativo ai [livelli di messaggistica standard e Premium del bus di servizio](service-bus-premium-messaging.md) .
>
> Questa funzionalità è attualmente disponibile in **Anteprima**. 


Per altre informazioni, vedere [come configurare gli endpoint privati per uno spazio dei nomi del bus di servizio](private-link-service.md)


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Come configurare un firewall IP per uno spazio dei nomi del bus di servizio](service-bus-ip-filtering.md)
- [Come configurare gli endpoint del servizio di rete virtuale per uno spazio dei nomi del bus di servizio](service-bus-service-endpoints.md)
- [Come configurare gli endpoint privati per uno spazio dei nomi del bus di servizio](private-link-service.md)
