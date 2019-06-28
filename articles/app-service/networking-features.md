---
title: Le funzionalità di distribuzione - servizio App di Azure di rete | Microsoft Docs
description: Come usare il servizio App di varie funzionalità di rete
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 362f0b32ea99d0b888ea9f1efa0cd4ea74eb2fa1
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67338502"
---
# <a name="app-service-networking-features"></a>Funzionalità di rete del servizio App

Le applicazioni nel servizio App di Azure possono essere distribuite in diversi modi. Per impostazione predefinita, servizio App di App ospitate sono direttamente accessibile tramite internet e possono solo raggiungere gli endpoint internet ospitata. Molte applicazioni dei clienti è tuttavia necessario controllare il traffico di rete in ingresso e in uscita. Sono disponibili diverse funzionalità nel servizio App consentono di soddisfare tali requisiti. La sfida consiste nel sapere quali funzionalità deve essere utilizzata per risolvere un problema specifico. Questo documento è destinato per aiutare i clienti di determinare quali funzionalità deve essere utilizzato in alcuni casi d'uso di esempio base.

Esistono due tipi di distribuzione principali per il servizio App di Azure. È presente il servizio multi-tenant pubblico, che ospita i piani di servizio App di Free, Shared, Basic, Standard, Premium e Premiumv2 prezzi SKU. Non vi è l'ambiente del servizio App, che ospita i piani di servizio App con SKU isolati direttamente nella rete virtuale di Azure (VNet) a singolo tenant. Le funzionalità usate variano se si utilizza il servizio multi-tenant o in un ambiente del servizio app. 

## <a name="multi-tenant-app-service-networking-features"></a>Funzionalità di rete del servizio App multi-tenant 

Servizio App di Azure è un sistema distribuito. I ruoli che gestiscono le richieste HTTP/HTTPS in ingresso vengono chiamati front-end. I ruoli che ospitano il carico di lavoro dei clienti vengono chiamati ruoli di lavoro. Tutti i ruoli in una distribuzione di servizio App esiste in una rete multi-tenant. Poiché sono presenti molti clienti diversi nella stessa unità di scala del servizio App, è possibile connettersi la rete del servizio App direttamente alla rete. Invece di connettersi le reti, abbiamo bisogno di funzionalità per gestire i diversi aspetti della comunicazione dell'applicazione. Le funzionalità che gestiscono le richieste all'App non sono utilizzabile per risolvere i problemi quando si effettuano chiamate dall'app. Analogamente, le funzionalità di risoluzione dei problemi per le chiamate dall'app non sono utilizzabile per risolvere i problemi nell'app.  

| Funzionalità in ingresso | Funzionalità in uscita |
|---------------------|-------------------|
| Indirizzi assegnati alle App | connessioni ibride |
| Restrizioni di accesso | Gateway richiesto integrazione rete virtuale |
| Endpoint servizio | Integrazione rete virtuale (anteprima) |

Se non indicato diversamente, tutte le funzionalità possono essere usate insieme. È possibile combinare le funzionalità per i vari problemi.

## <a name="use-case-and-features"></a>Caso d'uso e funzionalità

Per qualsiasi caso d'uso specifico, possono essere presenti alcuni modi per risolvere il problema.  La funzionalità corretta da usare è a volte a causa di motivi non sotto semplicemente il caso di utilizzo. I casi di utilizzo in ingresso seguenti indicano come Usa le funzionalità di rete del servizio App per risolvere problemi su come controllare il traffico diretto all'app. 
 
| Casi di utilizzo in ingresso | Funzionalità |
|---------------------|-------------------|
| Supportare le esigenze SSL basato su IP per l'app | Indirizzi assegnati alle App |
| Non condiviso, indirizzi in ingresso dedicato per l'app | Indirizzi assegnati alle App |
| Limitare l'accesso all'app da un set di indirizzi ben definiti | Restrizioni di accesso |
| Esporre l'app su indirizzi IP privati nella rete virtuale | Ambiente del servizio app con bilanciamento del carico interno </br> Gateway applicazione con gli endpoint di servizio |
| Limitare l'accesso alla mia app dalle risorse in una rete virtuale | Endpoint servizio </br> Ambiente del servizio app con bilanciamento del carico interno |
| Esporre l'app su un indirizzo IP privato nella rete virtuale | Ambiente del servizio app con bilanciamento del carico interno </br> indirizzo IP privato per connessioni in entrata in un Gateway applicazione con gli endpoint di servizio |
| Proteggere l'app con un WAF | Il Gateway applicazione e ambiente del servizio App ILB </br> Gateway applicazione con gli endpoint di servizio </br> Azure ingresso principale con restrizioni di accesso |
| Bilanciare il carico del traffico verso le App in aree diverse | Azure ingresso principale con restrizioni di accesso | 
| Bilanciare il traffico nella stessa area | Gateway applicazione con gli endpoint di servizio | 

I casi di utilizzo in uscita seguenti indicano come usare le funzionalità di rete del servizio App per risolvere esigenze di accesso in uscita per l'app. 

| Casi di utilizzo in uscita | Funzionalità |
|---------------------|-------------------|
| Accedere alle risorse in una rete virtuale di Azure nella stessa area | Integrazione rete virtuale </br> ASE |
| Accedere alle risorse in una rete virtuale di Azure in un'area diversa | Gateway richiesto integrazione rete virtuale </br> Ambiente del servizio App e il peering reti virtuali |
| Accedere alle risorse protette con gli endpoint di servizio | Integrazione rete virtuale </br> ASE |
| Accedere alle risorse in una rete privata non connessi ad Azure | connessioni ibride |
| Accedere alle risorse attraverso i circuiti ExpressRoute | Integrazione rete virtuale (con restrizioni per gli indirizzi RFC 1918 per ora) </br> ASE | 


### <a name="default-networking-behavior"></a>Comportamento di rete predefinito

Le unità di scala del servizio App di Azure supportano molti clienti in ogni distribuzione. I piani gratuito e condiviso SKU ospitano carichi di lavoro dei clienti nei ruoli di lavoro multi-tenant. Basic e versioni successive i piani host carichi di lavoro dedicati a un solo piano di servizio App (ASP). Se si disponeva di un piano di servizio App Standard, tutte le App in questo piano verrà eseguito nel ruolo di lavoro stesso. Se è scalare orizzontalmente il ruolo di lavoro, quindi tutte le App in tale piano ASP verrà replicate in un nuovo ruolo di lavoro per ogni istanza di ASP. I ruoli di lavoro che vengono usati per Premiumv2 sono diversi dai ruoli di lavoro utilizzate per i piani di altri. Ogni distribuzione del servizio App ha un indirizzo IP usato per tutto il traffico in ingresso per le App in tale distribuzione del servizio App. Esistono tuttavia ovunque da 4 a 11 indirizzi usati per effettuare chiamate in uscita. Questi indirizzi sono condivise da tutte le App in tale distribuzione del servizio App. Gli indirizzi in uscita sono diversi in base ai tipi di ruolo di lavoro diverso. Ciò significa che gli indirizzi usati da Free, Shared, Basic, Standard e Premium ASP siano diversi dagli indirizzi usati per le chiamate in uscita da ASP Premiumv2. Se si osserva le proprietà per l'app, è possibile visualizzare gli indirizzi in ingresso e in uscita che vengono usati dall'app. Se si desidera bloccare una dipendenza con un ACL di IP, usare il possibleOutboundAddresses. 

![Proprietà dell'App](media/networking-features/app-properties.png)

Servizio App ha un numero di endpoint ai quali vengono usati per gestire il servizio.  Tali indirizzi vengono pubblicati in un documento separato e sono anche nel tag service AppServiceManagement IP. Il tag AppServiceManagement viene usato solo con un'App (ambiente del servizio) in cui è necessario per consentire questo traffico. Il servizio App di indirizzi in ingresso vengono rilevati nel tag di servizio AppService IP. Non vi è alcun tag di servizio IP che contiene gli indirizzi in uscita usati dal servizio App. 

![Diagramma in ingresso e in uscita del servizio App](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Indirizzi assegnati alle App 

La funzionalità delle app all'indirizzo assegnato è un offshoot della funzionalità SSL basato su IP ed è accessibile tramite l'impostazione di SSL con l'app. Questa funzionalità può essere utilizzata per le chiamate a SSL basato su IP, ma può essere utilizzato anche per assegnare un indirizzo che ha solo all'app. 

![Diagramma di indirizzi assegnati alle App](media/networking-features/app-assigned-address.png)

Quando si usa un'app assegnata indirizzo, il traffico passa comunque attraverso gli stessi ruoli front-end in unità di scala di servizio App di gestire tutto il traffico in ingresso. L'indirizzo assegnato all'App, tuttavia, viene utilizzato solo dall'app. I casi d'uso per questa funzionalità sono:

* Supportare le esigenze SSL basato su IP per l'app
* Impostare un indirizzo dedicato per l'app che non viene condivisa con altri elementi

È possibile imparare a impostare un indirizzo nella tua app con l'esercitazione su [SSL basato su IP configurazione][appassignedaddress]. 

### <a name="access-restrictions"></a>Restrizioni di accesso 

Consente di funzionalità le restrizioni di accesso filtrare **in ingresso** le richieste basate sull'indirizzo IP di origine. Azione di filtro viene eseguita nei ruoli front-end che sono upstream da esegue il rollback il ruolo di lavoro in cui sono in esecuzione le tue app. Poiché i ruoli front-end sono upstream da ruoli di lavoro, la funzionalità di restrizioni di accesso può essere considerata come la protezione a livello di rete per le app. La funzionalità consente di compilare un elenco di blocchi di indirizzi che vengono valutati in ordine di priorità di negazione e assenso. È simile alla funzionalità gruppo di sicurezza di rete (NSG) che esiste nella rete di Azure.  È possibile usare questa funzionalità in un ambiente del servizio App o nel servizio multi-tenant. Se usato con un ambiente del servizio App ILB, è possibile limitare l'accesso da blocchi di indirizzi privato.

![Restrizioni di accesso](media/networking-features/access-restrictions.png)

La funzionalità relativa alle restrizioni di accesso consente in scenari in cui si desidera limitare gli indirizzi IP che possono essere utilizzati per raggiungere l'app. Tra l'uso case per questa funzionalità sono:

* Limitare l'accesso all'app da un set di indirizzi ben definiti 
* Limitare l'accesso tramite un servizio di bilanciamento del carico, ad esempio l'ingresso principale di Azure. Se si vuole bloccare il traffico in ingresso alla porta di ingresso di Azure, creare regole per consentire il traffico da 147.243.0.0/16 e 2a01:111:2050::/ / 44. 

![Restrizioni di accesso con l'ingresso principale](media/networking-features/access-restrictions-afd.png)

Se si vuole bloccare l'accesso all'App in modo che può essere raggiunto solo dalle risorse di rete virtuale di Azure (VNet), è necessario un indirizzo statico pubblico in qualunque sia l'origine è nella rete virtuale. Se le risorse non è un indirizzo pubblico, si deve usare invece la funzionalità di endpoint del servizio. Informazioni su come abilitare questa funzionalità con l'esercitazione sui [configurazione di restrizioni di accesso][iprestrictions].

### <a name="service-endpoints"></a>Endpoint di servizio

Gli endpoint di servizio consente di bloccare **in ingresso** accedere all'App in modo che l'indirizzo di origine deve provenire da un set di subnet selezionato. Questa funzionalità funziona in combinazione con le restrizioni di accesso IP. Gli endpoint di servizio sono impostati nell'esperienza dell'utente stesso come le restrizioni di accesso IP. È possibile compilare un elenco Consenti/Nega delle regole di accesso che include gli indirizzi pubblici, nonché subnet nelle reti virtuali. Questa funzionalità supporta scenari, ad esempio:

![Endpoint del servizio](media/networking-features/service-endpoints.png)

* Configurazione di un Gateway applicazione con l'app per bloccare il traffico in ingresso all'App
* Limitare l'accesso all'App per le risorse nella rete virtuale. Ciò può includere macchine virtuali, ambienti del servizio App o anche altre App che usano l'integrazione rete virtuale 

![endpoint di servizio con il gateway applicazione](media/networking-features/service-endpoints-appgw.png)

Altre informazioni sulla configurazione degli endpoint di servizio con l'app nell'esercitazione su [la configurazione di restrizioni di accesso dell'Endpoint servizio][serviceendpoints]
 
### <a name="hybrid-connections"></a>connessioni ibride

Connessioni ibride del servizio App consente alle App rendere **in uscita** chiamate a endpoint TCP specificati. L'endpoint può trovarsi in locale, in una rete virtuale o in qualsiasi punto che consente il traffico in uscita in Azure sulla porta 443. La funzionalità richiede l'installazione di un agente di inoltro denominato connessione Manager (ibrida) in un host più recenti o Windows Server 2012. Gestione connessione ibrida deve essere in grado di raggiungere l'inoltro di Azure sulla porta 443. Gestione connessione ibrida può essere scaricato dall'App del servizio ibrido connessioni interfaccia utente nel portale. 

![Flusso di rete di connessioni ibride](media/networking-features/hybrid-connections.png)

La funzionalità connessioni ibride del servizio App si basa sulla funzionalità connessioni ibride di inoltro di Azure. Servizio App Usa un tipo speciale di funzionalità che supporta solo le chiamate in uscita dall'app a un host TCP e la porta. In questo host e porta sufficiente a risolversi nell'host in cui è installato Gestione connessione ibrida. Quando l'app, nel servizio App, esegue una ricerca DNS nell'host e porta definita nella connessione ibrida, il traffico viene reindirizzato automaticamente per passare attraverso la connessione ibrida e out Hybrid Connection Manager. Per altre informazioni sulle connessioni ibride, leggere la documentazione su [connessioni ibride del servizio App][hybridconn]

Questa funzionalità viene comunemente usata per:

* Accedere alle risorse in reti private che non sono connessi ad Azure con una VPN o ExpressRoute
* Supporta modalità lift- and -shift delle App in locale nel servizio App senza la necessità di spostare anche i database di supporti  
* Fornire in modo sicuro l'accesso a una porta per ogni connessione ibrida e un singolo host. La maggior parte delle funzionalità di rete consente di accedere a una rete e connessioni ibride è solo il singolo host e la porta che è possibile raggiungere.
* Scenari non coperto da altri metodi di connettività in uscita
* Eseguire lo sviluppo nel servizio App in cui le app possono ora usufruire delle risorse locali 

Poiché la funzionalità consente l'accesso alle risorse locali senza un problema di firewall in entrata, è diffuso tra gli sviluppatori. Le altre in uscita del servizio App funzionalità di rete sono molto virtuale rete di Azure correlate. Connessioni ibride non è una dipendenza da passare attraverso una rete virtuale e può essere usato per un'ampia gamma di esigenze di rete. È importante notare che la funzionalità connessioni ibride del servizio App non occupa o conoscere le operazioni su di esso. Ovvero a dire che è possibile usare per accedere a un database, un servizio web o un socket TCP arbitrario su un mainframe. La funzionalità di tunnel essenzialmente pacchetti TCP. 

Mentre le connessioni ibride è più diffusi per lo sviluppo, si utilizza anche anche numerose applicazioni di produzione. È molto utile per l'accesso a un servizio web o un database, ma non è adatto per situazioni che comportano la creazione di un numero di connessioni. 

### <a name="gateway-required-vnet-integration"></a>Gateway richiesto integrazione rete virtuale 

Il gateway necessarie funzionalità integrazione rete virtuale del servizio App consente all'app rendere **in uscita** richieste in una rete virtuale di Azure. La funzionalità di funzioni correttamente connettendosi all'host che l'app in esecuzione in un gateway di rete virtuale in una rete virtuale con una VPN point-to-site. Quando si configura la funzionalità, l'app Ottiene uno degli indirizzi da punto a sito assegnati a ogni istanza. Questa funzionalità consente di accedere alle risorse nel modello di distribuzione classica o Resource Manager VNets in qualsiasi area. 

![Gateway richiesto integrazione rete virtuale](media/networking-features/gw-vnet-integration.png)

Questa funzionalità consente di risolvere il problema dell'accesso alle risorse in altre reti virtuali e può anche essere utilizzata per la connessione tramite una rete virtuale ad altre reti virtuali o addirittura in locale. Non funziona con ExpressRoute connessi reti connesse, reti virtuali, ma non con VPN Site-to-site. È in genere non appropriato usare questa funzionalità da un'app in un ambiente del servizio App (ASE), perché l'ambiente del servizio App è già in una rete virtuale. Casi di utilizzo che consente di risolvere questa funzionalità sono:

* L'accesso alle risorse su indirizzi IP privati in reti virtuali di Azure 
* L'accesso alle risorse locali se non c'è una VPN site-to-site 
* L'accesso alle risorse in reti virtuali con peering 

Quando questa funzionalità è abilitata, l'app userà il server DNS configurato con la rete virtuale di destinazione. Altre informazioni su questa funzionalità, la documentazione sulla [integrazione rete virtuale del servizio App][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>Integrazione rete virtuale

Il gateway obbligatoria funzionalità integrazione rete virtuale è molto utile, ma ancora non è stato risolto accesso alle risorse attraverso ExpressRoute. Sopra la necessità di reach per le connessioni di ExpressRoute, è necessario per le app di essere in grado di effettuare chiamate a endpoint del servizio servizi Web protetti. Per risolvere entrambi tali esigenze aggiuntive, è stata aggiunta un'altra funzionalità integrazione rete virtuale. La nuova funzionalità integrazione rete virtuale consente di posizionare il back-end dell'app in una subnet in una VNet Resource Manager nella stessa area. Questa funzionalità non è disponibile da un ambiente del servizio App, che è già in una rete virtuale. Questa funzionalità offre i vantaggi seguenti:

* Accesso alle risorse di Resource Manager VNets nella stessa area
* L'accesso alle risorse protette con gli endpoint di servizio 
* L'accesso alle risorse che sono accessibili su connessioni VPN o ExpressRoute

![Integrazione rete virtuale](media/networking-features/vnet-integration.png)

Questa funzionalità è disponibile in anteprima e non deve essere utilizzata per i carichi di lavoro di produzione. Per altre informazioni su questa funzionalità, continuare a leggere i documenti [integrazione rete virtuale del servizio App][vnetintegration].

## <a name="app-service-environment"></a>Ambiente del servizio app 

Un ambiente del servizio App (ASE) è una distribuzione a singolo tenant del servizio App di Azure che viene eseguito in una rete virtuale. L'ambiente del servizio App consente a casi d'uso, ad esempio:

* Accedere alle risorse nella rete virtuale
* Accedere alle risorse in ExpressRoute
* Esporre le tue App con un indirizzo privato nella propria rete virtuale 
* Accedere alle risorse attraverso gli endpoint di servizio 

Con un ambiente del servizio App, non devi usare funzionalità quali l'integrazione rete virtuale o gli endpoint di servizio perché l'ambiente del servizio App è già in una rete virtuale. Se si desidera accedere alle risorse, ad esempio SQL o archiviazione tramite gli endpoint di servizio, è possibile abilitare gli endpoint di servizio alla subnet dell'ambiente del servizio app. Se si desidera accedere alle risorse nella rete virtuale, non è richiesta alcuna configurazione aggiuntiva.  Se si desidera accedere alle risorse in ExpressRoute, si ha già nella rete virtuale e non è necessario niente da configurare per l'ambiente del servizio App o le App in essa contenuti. 

Poiché le App in un ambiente del servizio App ILB possono essere esposto in un indirizzo IP privato, è possibile aggiungere con facilità i dispositivi WAF per esporre solo per le app che si desidera internet e protezione il resto. Presta allo sviluppo semplificata di applicazioni multilivello. 

Esistono alcuni aspetti che non sono ancora possibili dal servizio multi-tenant che provengono da un ambiente del servizio app. Sono inclusi elementi quali:

* Esporre le tue App in un indirizzo IP privato
* Proteggere tutto il traffico in uscita con i controlli di rete che non fanno parte dell'app 
* Ospitare le tue App in un servizio di Single-tenant 
* Aumentare il numero di istanze più quanto sia possibile nel servizio multi-tenant 
* Caricare i certificati client di autorità di certificazione privati per l'uso dalle App con autorità di certificazione privata protetta endpoint 
* Forzare TLS 1.1 in tutte le app ospitate nel sistema senza qualsiasi possibilità di disabilitare a livello di app 
* Fornire un indirizzo in uscita dedicato per tutte le app nell'ambiente del servizio App che non viene condivisa con tutti i clienti 

![Ambiente del servizio App in una rete virtuale](media/networking-features/app-service-environment.png)

L'ambiente del servizio App fornisce la storia migliore quanto riguarda l'hosting di app isolato e dedicato, ma viene fornito con alcuni problemi di gestione. Alcuni aspetti da considerare prima di usare un ambiente del servizio App operativa sono:
 
 * Un ambiente del servizio App in esecuzione all'interno della rete virtuale, ma hanno dipendenze esterne alla rete virtuale. Tali dipendenze devono essere consentite. Altre informazioni, vedere [considerazioni sulla rete per un ambiente del servizio App][networkinfo]
 * Un ambiente del servizio App non presenta una scalabilità immediatamente, ad esempio il servizio multi-tenant. È necessario prevedere esigenze di ridimensionamento invece di ridimensionare in modo reattivo. 
 * Un ambiente del servizio App hanno un valore più alto fin dall'inizio costo associato. Per ottenere il massimo dall'ambiente del servizio App, è consigliabile pianificare l'inserimento di molti carichi di lavoro in un ambiente del servizio App piuttosto che hanno usato per attività di piccole dimensioni
 * Le App in un ambiente del servizio App non è possibile limitare l'accesso ad alcune App in un ambiente del servizio App e non altri.
 * L'ambiente del servizio App è in una subnet e tutte le regole di rete si applicano a tutto il traffico da e verso tale ambiente del servizio app. Se si desidera assegnare le regole del traffico in ingresso per una sola app, usare le restrizioni di accesso. 

## <a name="combining-features"></a>La combinazione di funzionalità 

Le funzionalità annotate per il servizio multi-tenant possono essere usate insieme per risolvere i casi d'uso di più elaborato. Due degli usi più comuni sono descritte di seguito, ma sono solo esempi. Grazie alla conoscenza operazioni varie funzionalità, è possibile risolvere quasi tutte le esigenze di architettura di sistema.

### <a name="inject-app-into-a-vnet"></a>Inserire l'app in una rete virtuale

Una richiesta comune è su come inserire l'app in una rete virtuale. Inserire l'app in una rete virtuale significa che gli endpoint in ingresso e in uscita per un'app all'interno di una rete virtuale. L'ambiente del servizio App offre la soluzione migliore per risolvere questo problema, ma è possibile ottenere la maggior parte degli elementi necessari con il servizio multi-tenant mediante la combinazione di funzionalità. Ad esempio, è possibile ospitare applicazioni solo intranet con indirizzi privati in ingresso e in uscita da:

* Creazione di un Gateway applicazione con indirizzi privati in ingresso e in uscita
* Protezione del traffico in ingresso all'App con gli endpoint di servizio 
* Usare la nuova integrazione rete virtuale in modo che il back-end dell'app è in una rete virtuale 

Questo stile di distribuzione potrebbe non fornire un indirizzo dedicato per il traffico in uscita a internet o offrono la possibilità di bloccare tutto il traffico in uscita dall'app.  Questo stile di distribuzione produrrebbe un molto di quello solo in caso contrario, eseguito con un ambiente del servizio app. 

### <a name="create-multi-tier-applications"></a>Creare applicazioni multilivello

Un'applicazione multilivello è un'applicazione in cui l'App per le API back-end è accessibile solo dal livello front-end. Per creare un'applicazione multilivello, è possibile:

* Usare l'integrazione rete virtuale per connettere il back-end dell'app web front-end con una subnet in una rete virtuale
* Usare gli endpoint di servizio per proteggere il traffico in ingresso per app per le API a provenienti solo dalla subnet usata dall'app web front-end

![app multilivello](media/networking-features/multi-tier-app.png)

È possibile avere più front-end di App usano la stessa app per le API tramite l'integrazione rete virtuale da altri endpoint del servizio dall'app per le API con le subnet e le app front-end.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl#bind-your-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
