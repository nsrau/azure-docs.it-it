---
title: Domande frequenti su Azure ExpressRoute | Microsoft Docs
description: "In Domande frequenti su ExpressRoute sono disponibili informazioni su servizi di Azure supportati, costi, dati e connessioni, Contratto di servizio, provider e località, larghezza di banda e altri dettagli tecnici."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: 09b17bc4-d0b3-4ab0-8c14-eed730e1446e
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: 0456cde7e30e9b25f8baebdcd15e0e029f89d7ff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="expressroute-faq"></a>Domande frequenti su ExpressRoute

## <a name="what-is-expressroute"></a>Che cos'è ExpressRoute?

ExpressRoute è un servizio di Azure che permette di creare connessioni private tra i data center Microsoft e l'infrastruttura disponibile localmente o in una struttura con percorso condiviso. Le connessioni ExpressRoute non sfruttano la rete Internet pubblica e offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Quali sono i vantaggi dell'uso di ExpressRoute e delle connessioni di rete private?

Le connessioni ExpressRoute non sfruttano la rete Internet pubblica. Esse offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e latenze minori e coerenti rispetto alle connessioni Internet tradizionali. In alcuni casi, l'uso di connessioni ExpressRoute per trasferire dati tra dispositivi locali e Azure può produrre vantaggi significativi in termini di costi.

### <a name="where-is-the-service-available"></a>Dov'è disponibile il servizio?

Per informazioni sulla località e la disponibilità del servizio, vedere [Partner e località per ExpressRoute](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>In che modo è possibile usare ExpressRoute per connettersi a Microsoft se non è disponibile una relazione con uno dei partner del gestore ExpressRoute?

Si può selezionare un gestore regionale e connessioni Ethernet via terra in una delle sedi supportate del provider di Exchange. È quindi possibile connettersi a Microsoft nella sede del provider. Per verificare se il provider di servizio è presente in una delle località di Exchange, vedere l'ultima sezione della pagina [Partner e località per ExpressRoute](expressroute-locations.md) . Sarà quindi possibile ordinare un circuito ExpressRoute tramite il provider di servizi per connettersi ad Azure.

### <a name="how-much-does-expressroute-cost"></a>Quanto costa ExpressRoute?

Per informazioni sui prezzi, vedere [Dettagli prezzi](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Se si acquista un circuito ExpressRoute di una determinata larghezza di banda, la connessione VPN acquistata dal provider di servizi di rete deve essere della stessa velocità?

No. È possibile acquistare una connessione VPN di qualsiasi velocità dal provider di servizi. Tuttavia, la connessione ad Azure è limitata alla larghezza di banda del circuito ExpressRoute acquistato.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Se si paga per un circuito ExpressRoute di una determinata larghezza di banda, si ha la possibilità di potenziarlo fino a velocità più elevate, se necessario?

Sì. I circuiti ExpressRoute sono configurati per consentire di potenziare fino al doppio il limite di larghezza di banda acquistato, senza alcun costo aggiuntivo. Contattare il provider di servizi per vedere se supporta questa funzionalità.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Si possono usare contemporaneamente la stessa connessione di rete privata con la rete virtuale e altri servizi di Azure?

Sì. Dopo l'installazione, il circuito ExpressRoute consente di accedere ai servizi all'interno di una rete virtuale e ad altri servizi di Azure contemporaneamente. La connessione alle reti virtuali viene eseguita tramite il percorso di peering privato e ad altri servizi tramite il percorso di peering pubblico.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>ExpressRoute offre un contratto di servizio?

Per informazioni, vedere [Contratto di servizio di ExpressRoute](https://azure.microsoft.com/support/legal/sla/).

## <a name="supported-services"></a>Servizi supportati

ExpressRoute supporta [tre domini di routing](expressroute-circuit-peerings.md) per diversi tipi di servizi.

### <a name="private-peering"></a>Peering privato

* Reti virtuali, inclusi tutti i servizi cloud e tutte le macchine virtuali

### <a name="public-peering"></a>Peering pubblico

* Power BI
* Dynamics 365 for Finance Operations (noto in precedenza come Dynamics AX Online)
* La maggior parte dei servizi di Azure, con le eccezioni seguenti:
  * RETE CDN
  * Test del carico di Visual Studio Team Services
  * Multi-Factor Authentication
  * Gestione traffico

### <a name="microsoft-peering"></a>Peering Microsoft

* [Office 365](http://aka.ms/ExpressRouteOffice365)
* Applicazioni Dynamics 365 Customer Engagement (precedentemente note come CRM Online)
  * Dynamics 365 for Sales
  * Dynamics 365 for Customer Service
  * Dynamics 365 for Field Service
  * Dynamics 365 for Project Service
* Usando i [filtri route](#route-filters-for-microsoft-peering), si ottiene l'accesso per gli stessi servizi pubblici con il peering Microsoft:
  * Power BI
  * Dynamics 365 per Finanza e operazioni
  * La maggior parte dei servizi di Azure, con le eccezioni seguenti:
    * RETE CDN
    * Test del carico di Visual Studio Team Services
    * Multi-Factor Authentication
    * Gestione traffico

## <a name="data-and-connections"></a>Dati e connessioni

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Esistono limiti alla quantità di dati trasferibili usando ExpressRoute?

Non esistono limiti alla quantità di dati trasferibili. Per informazioni sulle tariffe relative alle velocità della larghezza di banda, vedere [Dettagli prezzi](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Quali sono le velocità di connessione supportate da ExpressRoute?

Offerte relative alle larghezze di banda supportate:

50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps

### <a name="which-service-providers-are-available"></a>Quali provider del servizio sono disponibili?

Per l'elenco dei provider di servizi e delle relative località, vedere [Partner e località per ExpressRoute](expressroute-locations.md) .

## <a name="technical-details"></a>Dettagli tecnici

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Quali sono i requisiti tecnici per la connessione della posizione locale ad Azure?

Per informazioni sui requisiti, vedere la [pagina dei prerequisiti di ExpressRoute](expressroute-prerequisites.md).

### <a name="are-connections-to-expressroute-redundant"></a>Le connessioni a ExpressRoute sono ridondanti?

Sì. Ogni circuito ExpressRoute ha una coppia ridondante di connessioni incrociate configurate per garantire una disponibilità elevata.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>In caso di errore di un collegamento a ExpressRoute, verrà persa la connettività?

In caso di errore di una delle connessioni incrociate, la connettività non verrà persa. È attualmente disponibile una connessione ridondante per supportare il carico della rete. Si possono creare più circuiti in sedi di peering diverse per ottenere una maggiore resilienza agli errori.

## <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Come è possibile garantire la disponibilità elevata in una rete virtuale connessa a ExpressRoute?

È possibile ottenere la disponibilità elevata connettendo alla rete virtuale più circuiti ExpressRoute in posizioni di peering diverse. Ad esempio, se si arresta un sito di ExpressRoute, la connettività eseguirà il failover a un altro sito di ExpressRoute. Per impostazione predefinita, il traffico in uscita dalla rete virtuale viene instradato sul routing ECMP (Equal-Cost-Multi-Path). È possibile usare il peso della connessione per preferire una connessione a un'altra. Vedere [Optimizing ExpressRoute Routing](expressroute-optimize-routing.md) (Ottimizzazione del routing di ExpressRoute).

### <a name="onep2plink"></a>Se non si condivide un percorso in un'infrastruttura Cloud Exchange e il provider di servizi offre una connessione punto a punto, è necessario ordinare due connessioni fisiche tra la rete locale e Microsoft?

Se il provider di servizi è in grado di stabilire due circuiti virtuali Ethernet sulla connessione fisica, è sufficiente una connessione fisica. La connessione fisica, ad esempio una fibra ottica, viene terminata su un dispositivo di livello 1 (L1) (vedere l'immagine). I due circuiti virtuali Ethernet sono contrassegnati con ID VLAN diversi, uno per il circuito primario e uno per quello secondario. Gli ID VLAN sono nell'intestazione Ethernet 802.1Q esterna. L'intestazione Ethernet 802.1Q interna, non indicata nella figura, viene mappata a un [dominio di routing ExpressRoute](expressroute-circuit-peerings.md)specifico.

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Si può estendere una delle reti VLAN ad Azure tramite ExpressRoute?

No. Non sono supportate estensioni alla connettività di livello 2 in Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>La sottoscrizione può includere più di un circuito ExpressRoute?

Sì. La sottoscrizione può includere più di un circuito ExpressRoute. Il limite predefinito è impostato a 10. Se necessario, contattare il supporto tecnico Microsoft per aumentare il limite.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Si possono usare circuiti ExpressRoute di diversi provider di servizi?

Sì. È possibile usare i circuiti ExpressRoute con molti provider di servizi. Ogni circuito ExpressRoute è associato a un solo provider di servizi. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-location"></a>È possibile avere più circuiti ExpressRoute nello stesso percorso?

Sì. È possibile avere più circuiti ExpressRoute con provider di servizi uguali o diversi nello stesso percorso. Non è tuttavia possibile collegare più di un circuito ExpressRoute alla stessa rete virtuale dalla stessa località.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Come si connettono le reti virtuali a un circuito ExpressRoute?

I passaggi di base sono:

* Stabilire un circuito ExpressRoute e richiedere al provider di servizi di abilitarlo.
* L'utente o il provider deve configurare i peering BGP.
* Collegare la rete virtuale al circuito ExpressRoute.

Per altre informazioni, vedere [Flussi di lavoro e stati di provisioning di un circuito ExpressRoute](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Sono previsti limiti di connettività per il circuito ExpressRoute?

Sì. L'articolo [Partner e località per ExpressRoute](expressroute-locations.md) offre una panoramica dei limiti di connettività per un circuito ExpressRoute. La connettività per un circuito ExpressRoute è limitata a una singola area geopolitica. È possibile espandere la connettività per superare le aree geopolitiche abilitando la funzionalità Premium di ExpressRoute.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Un circuito ExpressRoute può essere collegato a più reti virtuali?

Sì. È possibile avere fino a 10 connessioni di reti virtuali in un circuito ExpressRoute Standard e fino a 100 in un [circuito ExpressRoute Premium](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Sono disponibili più sottoscrizioni di Azure che contengono reti virtuali. È possibile connettere reti virtuali di sottoscrizioni distinte a un singolo circuito ExpressRoute?

Sì. È possibile autorizzare fino a 10 sottoscrizioni di Azure a usare un unico circuito ExpressRoute. Per aumentare questo limite, abilitare la funzionalità Premium di ExpressRoute.

Per altre informazioni, vedere [Condivisione di un circuito ExpressRoute tra più sottoscrizioni](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Sono disponibili più sottoscrizioni di Azure associate a diversi tenant di Azure Active Directory o le registrazioni di Enterprise Agreement. È possibile connettere reti virtuali presenti in tenant separati e registrazioni a un singolo circuito ExpressRoute che non si trova nello stesso tenant o registrazione?

Sì. Le autorizzazioni di ExpressRoute possono estendersi a sottoscrizione, tenant e registrazione, senza richiedere alcuna configurazione aggiuntiva. 

Per altre informazioni, vedere [Condivisione di un circuito ExpressRoute tra più sottoscrizioni](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Le reti virtuali connesse allo stesso circuito sono isolate tra loro?

No. Da un punto di vista del routing, tutte le reti virtuali collegate allo stesso circuito ExpressRoute appartengono allo stesso dominio di routing e non sono isolate le une dalle altre. Se è necessario l'isolamento delle route, creare un circuito ExpressRoute separato.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>È possibile connettere una sola rete virtuale a più circuiti ExpressRoute?

Sì. È possibile collegare una singola rete virtuale con un massimo di quattro circuiti ExpressRoute. Devono essere ordinati in quattro [località per ExpressRoute](expressroute-locations.md)diverse.

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Si può accedere a Internet usando le reti virtuali connesse a circuiti ExpressRoute?

Sì. Se non è stata eseguita la pubblicazione di route predefinite (0.0.0.0/0) o prefissi di route Internet tramite la sessione BGP, è possibile connettersi a Internet da una rete virtuale collegata a un circuito ExpressRoute.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Si può bloccare la connettività Internet per le reti virtuali connesse a circuiti ExpressRoute?

Sì. È possibile pubblicare route predefinite (0.0.0.0/0) per bloccare tutta la connettività Internet per le macchine virtuali distribuite in una rete virtuale e indirizzare tutto il traffico in uscita attraverso il circuito ExpressRoute.

Se si pubblicano route predefinite, il traffico verso i servizi offerti tramite peering pubblico, ad esempio l'archiviazione di Azure e database SQL, viene forzato verso l'istanza locale. Sarà necessario configurare i router in modo che restituiscano traffico ad Azure tramite il percorso di peering pubblico su Internet. Se è stato abilitato un endpoint del servizio (anteprima) per il servizio, il traffico verso il servizio non viene forzato in locale, ma rimane all'interno della rete backbone di Azure. Per altre informazioni sugli endpoint del servizio, vedere [Endpoint del servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json).

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Le reti virtuali collegate allo stesso circuito ExpressRoute possono comunicare tra loro?

Sì. Le macchine virtuali distribuite in reti virtuali connesse allo stesso circuito ExpressRoute possono comunicare tra loro.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>È possibile usare la connettività da sito per reti virtuali insieme a ExpressRoute?

Sì. ExpressRoute può coesistere con VPN da sito a sito.

### <a name="can-i-move-a-virtual-network-from-site-to-site--point-to-site-configuration-to-use-expressroute"></a>È possibile spostare una rete virtuale da una configurazione da sito a sito o da punto a sito, in modo da usare ExpressRoute?

Sì. Sarà necessario creare un gateway di ExpressRoute nella rete virtuale. Il processo comporta un breve tempo di inattività.

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Perché esiste un indirizzo IP pubblico associato al gateway ExpressRoute in una rete virtuale?

L'indirizzo IP pubblico viene usato esclusivamente per la gestione interna. Non viene esposto a Internet e non costituisce un rischio per la sicurezza della rete virtuale.

### <a name="what-do-i-need-to-connect-to-azure-storage-over-expressroute"></a>Cosa si deve fare per connettersi al servizio di archiviazione di Azure tramite ExpressRoute?

Si deve stabilire un circuito ExpressRoute e configurare le route per il peering pubblico.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Sono previsti limiti per i numeri di route pubblicabili?

Sì. Sono accettati al massimo 4000 prefissi di route per il peering privato e 200 ciascuno per il peering pubblico e il peering Microsoft. Se si abilita la funzionalità Premium di ExpressRoute, sarà possibile aumentare questo valore fino a 10.000 route per il peering privato.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Sono previste limitazioni per gli intervalli IP pubblicabili tramite la sessione BGP?

I prefissi privati (RFC1918) non sono accettati nella sessione BGP per il peering pubblico e di Microsoft.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Cosa succede se si superano i limiti per BGP?

Le sessioni BGP saranno rimosse. Saranno ripristinate quando il numero di prefissi tornerà sotto il limite consentito.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Che cos'è il tempo di attesa BGP ExpressRoute? È possibile regolarlo?

Il tempo di attesa è 180. I messaggi keep-alive vengono inviati ogni 60 secondi. Queste sono impostazioni fisse sul lato Microsoft che non è possibile modificare. È possibile configurare diversi timer, e i parametri della sessione BGP verranno negoziati di conseguenza.

### <a name="after-i-advertise-the-default-route-00000-to-my-virtual-networks-i-cant-activate-windows-running-on-my-azure-vms-how-to-i-fix-this"></a>Dopo la pubblicazione della route predefinita (0.0.0.0/0) alle reti virtuali, non è possibile attivare Windows in esecuzione nelle macchine virtuali di Azure. Come si risolve questo problema?

I passaggi seguenti permettono ad Azure di riconoscere la richiesta di attivazione:

1. Stabilire il peering pubblico per il circuito ExpressRoute.
2. Eseguire una ricerca DNS e trovare l'indirizzo IP di **kms.core.windows.net**
3. Il servizio di gestione delle chiavi deve riconoscere che la richiesta di attivazione proviene da Azure e onorare la richiesta. Eseguire una delle tre attività seguenti:

   * Nella rete locale reindirizzare il traffico destinato all'indirizzo IP che è stato ottenuto nel passaggio 2 ad Azure tramite il peering pubblico.
   * Richiedere al provider di servizi di rete di reindirizzare il traffico ad Azure tramite il peering pubblico.
   * Creare una route definita dall'utente che faccia riferimento a tale indirizzo IP con Internet come hop successivo e applicarla alle subnet in cui si trovano le macchine virtuali.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>È possibile modificare la larghezza di banda di un circuito ExpressRoute?

Sì, è possibile provare ad aumentare la larghezza di banda del circuito ExpressRoute nel portale di Azure o con PowerShell. Se è disponibile capacità sulla porta fisica in cui è stato creato il circuito, la modifica ha esito positivo. 

Se la modifica ha esito negativo, significa che la capacità residua sulla porta corrente non è sufficiente e che è necessario creare un nuovo circuito ExpressRoute con maggiore larghezza di banda, oppure che la località non ha altra capacità. In quest'ultimo caso non è possibile aumentare la larghezza di banda. 

Sarà anche necessario contattare il provider di connettività per assicurare che aggiorni le limitazioni della propria rete per supportare l'incremento della larghezza di banda. Non è tuttavia possibile ridurre la larghezza di banda del circuito ExpressRoute. È necessario creare un nuovo circuito ExpressRoute con larghezza di banda inferiore ed eliminare il circuito precedente.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Come si modifica la larghezza di banda di un circuito ExpressRoute?

È possibile aggiornare la larghezza di banda del circuito ExpressRoute usando l'API REST dedicata o il cmdlet di PowerShell.

## <a name="expressroute-premium"></a>ExpressRoute Premium

### <a name="what-is-expressroute-premium"></a>Che cos'è ExpressRoute Premium?

ExpressRoute Premium è una raccolta delle funzionalità seguenti:

* Aumento del limite delle tabelle di routing da 4000 a 10.000 route per il peering privato.
* Incremento del numero di VNet che possono essere connesse al circuito ExpressRoute (il valore predefinito è 10). Per altre informazioni, vedere la tabella [Limiti di ExpressRoute](#limits).
* Connettività a Office 365 e Dynamics 365.
* Connettività globale sulla rete di base Microsoft. È ora possibile collegare una VNet in un'area geopolitica a un circuito ExpressRoute in un'altra area.<br>
    **Esempi:**

    *  È possibile collegare una VNet creata in Europa occidentale a un circuito ExpressRoute creato a Silicon Valley. 
    *  Sul peering pubblico, i prefissi di altre aree geopolitiche vengono pubblicati in modo che sia ad esempio possibile connettersi a SQL Azure in Europa occidentale da un circuito di Silicon Valley.


### <a name="limits"></a>Quante VNet possono essere collegate a un circuito ExpressRoute se è stato abilitato ExpressRoute Premium?

Le tabelle seguenti illustrano i limiti di ExpressRoute e il numero di reti virtuali per ogni circuito ExpressRoute:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Come si abilita ExpressRoute Premium?

Le funzionalità di ExpressRoute Premium possono essere abilitate quando si attiva la funzionalità e possono essere arrestate tramite l'aggiornamento dello stato del circuito. È possibile abilitare ExpressRoute Premium in fase di creazione del circuito oppure chiamare l'API REST o il cmdlet di PowerShell.

### <a name="how-do-i-disable-expressroute-premium"></a>Come si disabilita ExpressRoute Premium?

È possibile disabilitare ExpressRoute Premium chiamando l'API REST o il cmdlet di PowerShell. È necessario assicurarsi di avere ridimensionato le esigenze di connettività per soddisfare i limiti predefiniti prima di disabilitare ExpressRoute Premium. Se l'uso supera i limiti predefiniti, la richiesta di disabilitazione di ExpressRoute Premium avrà esito negativo.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>È possibile scegliere solo alcune funzionalità di ExpressRoute Premium?

No. Non è possibile selezionare le funzionalità. Quando si attiva ExpressRoute Premium, vengono abilitate tutte le funzionalità.

### <a name="how-much-does-expressroute-premium-cost"></a>Quanto costa ExpressRoute Premium?

Per informazioni sui costi, vedere [Dettagli prezzi](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>È necessario un pagamento aggiuntivo per ExpressRoute Premium, oltre agli addebiti per la versione standard di ExpressRoute?

Sì. Gli addebiti per ExpressRoute Premium vengono sommati agli addebiti per ExpressRoute e agli addebiti richiesti dal provider di connettività.

## <a name="expressroute-for-office-365-and-dynamics-365"></a>ExpressRoute per Office 365 e Dynamics 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services-and-dynamics-365"></a>Come si crea un circuito di ExpressRoute per la connessione ai servizi di Office 365 e a Dynamics 365?

1. Per assicurarsi di soddisfare i requisiti previsti, vedere la pagina sui [prerequisiti di ExpressRoute](expressroute-prerequisites.md).
2. Per assicurarsi di soddisfare le esigenze specifiche a livello di connettività, vedere l'elenco di provider di servizi e località nell'articolo [Partner e località per ExpressRoute](expressroute-locations.md).
3. Per pianificare i requisiti relativi alla capacità, vedere la pagina relativa alla [pianificazione e al perfezionamento delle prestazioni di rete per Office 365](http://aka.ms/tune/).
4. Seguire la procedura indicata nei flussi di lavoro per configurare la connettività dei [flussi di lavoro ExpressRoute per provisioning e stati di un circuito](expressroute-workflows.md).

> [!IMPORTANT]
> Assicurarsi che sia stato abilitato il componente aggiuntivo ExpressRoute Premium quando si configura la connettività ai servizi di Office 365 e a Dynamics 365.
> 
> 

### <a name="do-i-need-to-enable-azure-public-peering-to-connect-to-office-365-services-and-dynamics-365"></a>È necessario abilitare il peering pubblico di Azure per connettersi ai servizi Office 365 e a Dynamics 365?

No, è sufficiente abilitare il peering Microsoft. Il traffico di autenticazione è inviato ad Azure AD attraverso il peering Microsoft. 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services-and-dynamics-365"></a>I circuiti di ExpressRoute esistenti possono supportare la connettività ai servizi di Office 365 e a Dynamics 365?

Sì. È possibile configurare i circuiti ExpressRoute esistenti per supportare la connettività ai servizi di Office 365. Assicurarsi di avere una capacità sufficiente a connettersi ai servizi di Office 365 e che sia stato abilitato il componente aggiuntivo Premium. [Pianificazione e al perfezionamento delle prestazioni di rete per Office 365](http://aka.ms/tune/) aiuta a pianificare le esigenze di connettività. Vedere inoltre [Creare e modificare un circuito ExpressRoute](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>A quali servizi di Office 365 è possibile accedere tramite una connessione ExpressRoute?

Per un elenco aggiornato dei servizi supportati con ExpressRoute, vedere la pagina [URL e intervalli di indirizzi IP per Office 365](http://aka.ms/o365endpoints).

### <a name="how-much-does-expressroute-for-office-365-services-and-dynamics-365-cost"></a>Quanto costa ExpressRoute per i servizi di Office 365 e Dynamics 365?

Per i servizi di Office 365 e Dynamics 365 è necessario abilitare il componente aggiuntivo Premium. Vedere la [pagina dei dettagli sui prezzi](https://azure.microsoft.com/pricing/details/expressroute/) per informazioni sui prezzi.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>In quali aree è supportato ExpressRoute per Office 365?

Vedere [Partner e località di peering per Azure ExpressRoute](expressroute-locations.md) per ottenere informazioni.

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>È possibile accedere a Office 365 tramite Internet anche se ExpressRoute è stato configurato per l'organizzazione specifica?

Sì. Gli endpoint del servizio di Office 365 sono raggiungibili tramite Internet anche se ExpressRoute è stato configurato per la rete specifica. Se ci si trova in una località configurata per la connessione ai servizi di Office 365 tramite ExpressRoute, la connessione verrà effettuata tramite ExpressRoute.

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>È possibile accedere ai servizi US Government Community (GCC) di Office 365 tramite un circuito ExpressRoute di Azure per enti pubblici statunitensi?

Sì. Gli endpoint del servizio GCC di Office 365 sono raggiungibili tramite ExpressRoute di Azure per enti pubblici statunitensi. Tuttavia, è innanzitutto necessario aprire un ticket di supporto nel portale di Azure per fornire i prefissi che si desidera segnalare a Microsoft. Verrà stabilita la connettività ai servizi GCC di Office 365 dopo aver risolto il ticket di supporto. 

### <a name="can-dynamics-365-for-operations-formerly-known-as-dynamics-ax-online-be-accessed-over-an-expressroute-connection"></a>È possibile accedere a Dynamics 365 for Operations (noto in precedenza come) Dynamics AX Online tramite una connessione ExpressRoute?

Sì. [Dynamics 365 for Operations](https://www.microsoft.com/dynamics365/operations) è ospitato in Azure. È possibile abilitare il peering pubblico di Azure nel circuito ExpressRoute per connettersi.

## <a name="route-filters-for-microsoft-peering"></a>Filtri di route per il peering Microsoft

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Sto attivando il peering Microsoft per la prima volta, quale sarà la route visualizzata?

Non verrà visualizzata nessuna route. È necessario allegare un filtro di route per il circuito per avviare gli annunci di prefisso. Per istruzioni, vedere [Configurare i filtri di route per il peering Microsoft](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Ho attivato il peering Microsoft e ora sto cercando di selezionare Exchange Online, ma compare un messaggio di errore che indica che non sono autorizzato a eseguire l'operazione.

Quando si usano filtri di route, tutti i clienti possono attivare il peering Microsoft. Tuttavia, per l'uso dei servizi di Office 365, è comunque necessario ottenere l'autorizzazione da Office 365.

### <a name="do-i-need-to-get-authorization-for-turning-on-dynamics-365-over-microsoft-peering"></a>È necessario ottenere l'autorizzazione per l'attivazione di Dynamics 365 tramite il peering Microsoft?

No, non è necessaria l'autorizzazione per Dynamics 365. È possibile creare una regola e selezionare la community di Dynamics 365 senza autorizzazione.

### <a name="i-enabled-microsoft-peering-prior-to-august-1st-2017-how-can-i-take-advantage-of-route-filters"></a>Il peering Microsoft è stato abilitato prima del 1 agosto 2017. Come posso usare al meglio i filtri di route?

Il circuito esistente continuerà ad annunciare i prefissi per Office 365 e Dynamics 365. Per aggiungere annunci di prefissi pubblici di Azure tramite lo stesso peering Microsoft, creare un filtro di route, selezionare i servizi che si vuole annunciare (inclusi i servizi di Office 365 e Dynamics 365 necessari) e collegare il filtro al peering Microsoft in uso. Per istruzioni, vedere [Configurare i filtri di route per il peering Microsoft](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Ho un peering Microsoft in una località, ora sto cercando di abilitarlo in un'altra località e non vengono visualizzati i prefissi.

* Il peering Microsoft dei circuiti ExpressRoute che sono stati configurati prima del 1° agosto 2017, avranno tutti i prefissi di servizio pubblicati tramite il peering Microsoft, anche se non sono definiti i filtri di route.

* Il peering Microsoft dei circuiti ExpressRoute che vengono configurati dopo il 1° agosto 2017 non avrà alcun prefisso annunciato fino a quando non viene associato un filtro di route per il circuito. Per impostazione predefinita, non verrà visualizzato alcun prefisso.
