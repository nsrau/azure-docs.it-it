---
title: Funzionalità di distribuzione di rete-servizio app Azure | Microsoft Docs
description: Come usare le varie funzionalità di rete del servizio app
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 950818d08cb654bad969deaede24231cab9bcbe2
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098564"
---
# <a name="app-service-networking-features"></a>Funzionalità di rete del servizio app

Le applicazioni nel servizio app Azure possono essere distribuite in diversi modi. Per impostazione predefinita, le app ospitate dal servizio app sono direttamente accessibili a Internet e possono raggiungere solo endpoint ospitati da Internet. Molte applicazioni dei clienti, tuttavia, devono controllare il traffico di rete in ingresso e in uscita. Nel servizio app sono disponibili diverse funzionalità per soddisfare queste esigenze. La sfida è conoscere la funzionalità da utilizzare per risolvere un determinato problema. Questo documento è destinato a consentire ai clienti di determinare la funzionalità da usare in base ad alcuni casi d'uso di esempio.

Esistono due tipi di distribuzione principali per il servizio app Azure. È disponibile il servizio pubblico multi-tenant che ospita i piani di servizio app negli SKU di prezzo gratuito, condiviso, Basic, standard, Premium e Premiumv2. A questo punto, è presente la ambiente del servizio app del singolo tenant, che ospita i piani di servizio App SKU isolati direttamente nella rete virtuale di Azure (VNet). Le funzionalità usate variano a seconda che si trovino in un servizio multi-tenant o in un ambiente del servizio app. 

## <a name="multi-tenant-app-service-networking-features"></a>Funzionalità di rete del servizio app multi-tenant 

Il servizio app Azure è un sistema distribuito. I ruoli che gestiscono le richieste HTTP/HTTPS in ingresso sono detti front-end. I ruoli che ospitano il carico di lavoro del cliente sono detti thread di lavoro. Tutti i ruoli in una distribuzione del servizio app sono presenti in una rete multi-tenant. Poiché nella stessa unità di scala del servizio app sono presenti molti clienti diversi, non è possibile connettere la rete del servizio app direttamente alla rete. Anziché connettere le reti, sono necessarie funzionalità per gestire i diversi aspetti della comunicazione delle applicazioni. Le funzionalità che gestiscono le richieste all'app non possono essere usate per risolvere i problemi quando si effettuano chiamate dall'app. Analogamente, le funzionalità che risolvono i problemi per le chiamate dall'app non possono essere usate per risolvere i problemi dell'app.  

| Funzionalità in ingresso | Funzionalità in uscita |
|---------------------|-------------------|
| Indirizzo assegnato dall'app | Connessioni ibride |
| Restrizioni di accesso | Integrazione VNet necessaria per il gateway |
| Endpoint di servizio | Integrazione di VNet (anteprima) |

Se non diversamente specificato, tutte le funzionalità possono essere usate insieme. È possibile combinare le funzionalità per risolvere i diversi problemi.

## <a name="use-case-and-features"></a>Casi d'uso e funzionalità

Per ogni caso di utilizzo specifico, è possibile risolvere il problema in alcuni modi.  La funzionalità giusta da usare è a volte dovuta a motivi oltre al solo caso d'uso. I seguenti casi di utilizzo in ingresso suggeriscono come usare le funzionalità di rete del servizio app per risolvere i problemi relativi al controllo del traffico verso l'app. 
 
| Casi di utilizzo in ingresso | Funzionalità |
|---------------------|-------------------|
| Supportare le esigenze SSL basate su IP per l'app | indirizzo assegnato dall'app |
| Indirizzo in ingresso non condiviso e dedicato per l'app | indirizzo assegnato dall'app |
| Limitare l'accesso all'app da un set di indirizzi ben definiti | Restrizioni di accesso |
| Esporre l'app in indirizzi IP privati nella VNet | Ambiente del servizio app con bilanciamento del carico interno </br> Gateway applicazione con endpoint di servizio |
| Limitare l'accesso all'app dalle risorse in una VNet | Endpoint di servizio </br> Ambiente del servizio app con bilanciamento del carico interno |
| Esporre l'app in un indirizzo IP privato nella VNet | Ambiente del servizio app con bilanciamento del carico interno </br> IP privato per il traffico in ingresso in un gateway applicazione con endpoint di servizio |
| Proteggi l'app con un WAF | Gateway applicazione + ambiente del servizio app ILB </br> Gateway applicazione con endpoint di servizio </br> Sportello anteriore di Azure con restrizioni di accesso |
| Bilanciare il carico del traffico verso le app personali in aree diverse | Sportello anteriore di Azure con restrizioni di accesso | 
| Bilanciare il carico del traffico nella stessa area | Gateway applicazione con endpoint di servizio | 

I seguenti casi di utilizzo in uscita suggeriscono come usare le funzionalità di rete del servizio app per risolvere le esigenze di accesso in uscita per l'app. 

| Casi di utilizzo in uscita | Funzionalità |
|---------------------|-------------------|
| Accedere alle risorse in una Rete in ingresso virtuale di Azure nella stessa area | Integrazione rete virtuale </br> ASE |
| Accedere alle risorse in una Rete in ingresso virtuale di Azure in un'area diversa | Integrazione VNet necessaria per il gateway </br> Peering ASE e VNet |
| Accedere alle risorse protette con gli endpoint di servizio | Integrazione rete virtuale </br> ASE |
| Accedere alle risorse in una rete privata non connessa ad Azure | Connessioni ibride |
| Accedere alle risorse tra circuiti ExpressRoute | Integrazione di VNet (limitata agli indirizzi RFC 1918 per il momento) </br> ASE | 


### <a name="default-networking-behavior"></a>Comportamento di rete predefinito

Le unità di scala del servizio app Azure supportano molti clienti in ogni distribuzione. I piani di SKU gratuito e condiviso ospitano i carichi di lavoro dei clienti sui ruoli di lavoro multi-tenant. I piani Basic e superiori ospitano i carichi di lavoro dei clienti dedicati a un solo piano di servizio app (ASP). Se si dispone di un piano di servizio app standard, tutte le app del piano vengono eseguite nello stesso thread di lavoro. Se il ruolo di lavoro viene scalato in orizzontale, tutte le app in tale ASP verranno replicate in un nuovo ruolo di lavoro per ogni istanza di ASP. I thread di lavoro usati per Premiumv2 sono diversi da quelli usati per gli altri piani. Ogni distribuzione del servizio app ha un indirizzo IP usato per tutto il traffico in ingresso per le app nella distribuzione del servizio app. Esistono tuttavia da 4 a 11 indirizzi usati per eseguire chiamate in uscita. Questi indirizzi sono condivisi da tutte le app presenti nella distribuzione del servizio app. Gli indirizzi in uscita sono diversi in base ai diversi tipi di lavoro. Questo significa che gli indirizzi usati dagli ASP gratuito, condiviso, Basic, standard e Premium sono diversi dagli indirizzi usati per le chiamate in uscita da Premiumv2 ASP. Se si osservano le proprietà dell'app, è possibile visualizzare gli indirizzi in ingresso e in uscita usati dall'app. Se è necessario bloccare una dipendenza con un ACL IP, usare possibleOutboundAddresses. 

![Proprietà dell'app](media/networking-features/app-properties.png)

Il servizio app include diversi endpoint usati per gestire il servizio.  Questi indirizzi vengono pubblicati in un documento separato e si trovano anche nel tag del servizio IP AppServiceManagement. Il tag AppServiceManagement viene usato solo con un ambiente del servizio app (ASE) in cui è necessario consentire tale traffico. Gli indirizzi in ingresso del servizio app vengono rilevati nel tag del servizio IP di AppService. Non è presente alcun tag del servizio IP che contiene gli indirizzi in uscita usati dal servizio app. 

![Diagramma in ingresso e in uscita del servizio app](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Indirizzo assegnato dall'app 

La funzionalità di indirizzo assegnato dall'app è una propaggine della funzionalità SSL basata su IP ed è accessibile tramite la configurazione di SSL con l'app. Questa funzionalità può essere usata per le chiamate SSL basate su IP, ma può essere usata anche per fornire all'app un indirizzo che possiede solo. 

![Diagramma degli indirizzi assegnati all'app](media/networking-features/app-assigned-address.png)

Quando si usa un indirizzo assegnato dall'app, il traffico passa ancora attraverso gli stessi ruoli front-end che gestiscono tutto il traffico in ingresso nell'unità di scala del servizio app. L'indirizzo assegnato all'app, tuttavia, viene usato solo dall'app. I casi d'uso per questa funzionalità sono i seguenti:

* Supportare le esigenze SSL basate su IP per l'app
* Imposta un indirizzo dedicato per l'app che non è condiviso con altro

Per informazioni su come impostare un indirizzo nell'app, vedere l'esercitazione sulla [configurazione di SSL basato su IP][appassignedaddress]. 

### <a name="access-restrictions"></a>Restrizioni di accesso 

La funzionalità restrizioni di accesso consente di filtrare le richieste in **ingresso** in base all'indirizzo IP di origine. L'azione di filtro si verifica nei ruoli front-end che sono upstream dai rotoli di lavoro in cui sono in esecuzione le app. Poiché i ruoli front-end sono upstream dai ruoli di lavoro, la funzionalità restrizioni di accesso può essere considerata come protezione a livello di rete per le app. La funzionalità consente di compilare un elenco di blocchi di indirizzi allow e Deny che vengono valutati in ordine di priorità. È simile alla funzionalità del gruppo di sicurezza di rete (NSG) presente nella rete di Azure.  È possibile usare questa funzionalità in un ambiente del servizio app o nel servizio multi-tenant. Quando viene usato con un ambiente del servizio app ILB, è possibile limitare l'accesso da blocchi di indirizzi privati.

![Restrizioni di accesso](media/networking-features/access-restrictions.png)

La funzionalità restrizioni di accesso è utile negli scenari in cui si vuole limitare gli indirizzi IP che possono essere usati per raggiungere l'app. Tra i casi d'uso per questa funzionalità:

* Limitare l'accesso all'app da un set di indirizzi ben definiti 
* Limitare l'accesso in modo che arrivi attraverso un servizio di bilanciamento del carico, ad esempio front door di Azure. Se si vuole bloccare il traffico in ingresso verso la porta anteriore di Azure, creare regole per consentire il traffico da 147.243.0.0/16 e 2a01:111:2050::/44. 

![Limitazioni di accesso con sportello anteriore](media/networking-features/access-restrictions-afd.png)

Se si vuole bloccare l'accesso all'app in modo che possa essere raggiunto solo dalle risorse nella rete virtuale di Azure (VNet), è necessario un indirizzo pubblico statico in qualunque sia l'origine nel VNet. Se le risorse non dispongono di un indirizzo pubblico, è consigliabile utilizzare la funzionalità endpoint del servizio. Informazioni su come abilitare questa funzionalità con l'esercitazione sulla [configurazione delle restrizioni di accesso][iprestrictions].

### <a name="service-endpoints"></a>Endpoint di servizio

Gli endpoint di servizio consentono di bloccare l'accesso in **ingresso** all'app in modo che l'indirizzo di origine debba provenire da un set di subnet selezionato. Questa funzionalità funziona insieme alle restrizioni di accesso IP. Gli endpoint di servizio vengono impostati nella stessa esperienza utente delle restrizioni di accesso IP. È possibile compilare un elenco Consenti/Nega di regole di accesso che include indirizzi pubblici e subnet nella reti virtuali. Questa funzionalità supporta scenari come:

![Endpoint del servizio](media/networking-features/service-endpoints.png)

* Configurazione di un gateway applicazione con l'app per bloccare il traffico in ingresso verso l'app
* Limitazione dell'accesso all'app alle risorse nella VNet. Questo può includere VM, gli ambienti o anche altre app che usano l'integrazione VNet 

![endpoint di servizio con il gateway applicazione](media/networking-features/service-endpoints-appgw.png)

Per altre informazioni sulla configurazione degli endpoint di servizio con l'app, vedere l'esercitazione sulla [configurazione delle restrizioni di accesso agli endpoint di servizio][serviceendpoints]
 
### <a name="hybrid-connections"></a>Connessioni ibride

Il servizio app Connessioni ibride consente alle app di effettuare chiamate in **uscita** agli endpoint TCP specificati. L'endpoint può trovarsi in locale, in una VNet o ovunque, che consente il traffico in uscita verso Azure sulla porta 443. La funzionalità richiede l'installazione di un agente di inoltro denominato Gestione connessione ibrida (HCM) in un host Windows Server 2012 o versione successiva. Gestione connessione ibrida deve essere in grado di raggiungere il relè di Azure sulla porta 443. È possibile scaricare gestione connessione ibrida dal servizio app Connessioni ibride interfaccia utente nel portale. 

![Flusso di rete Connessioni ibride](media/networking-features/hybrid-connections.png)

Il servizio app Connessioni ibride funzionalità è basato sulla funzionalità Connessioni ibride di inoltro di Azure. Il servizio app usa un modulo specializzato della funzionalità che supporta solo la creazione di chiamate in uscita dall'app a un host e una porta TCP. Questo host e la porta devono essere risolti solo nell'host in cui è installato Gestione connessione ibrida. Quando l'app, nel servizio app, esegue una ricerca DNS sull'host e sulla porta definiti nella connessione ibrida, il traffico viene reindirizzato automaticamente per passare attraverso la connessione ibrida e il Gestione connessione ibrida. Per altre informazioni su Connessioni ibride, vedere la documentazione sul [servizio App connessioni ibride][hybridconn]

Questa funzionalità viene in genere utilizzata per:

* Accedere alle risorse in reti private non connesse ad Azure con una VPN o ExpressRoute
* Supporto del lift-and-Shift delle app locali al servizio app senza dover spostare anche i database di supporto  
* Fornire in modo sicuro l'accesso a un singolo host e a una porta per ogni connessione ibrida. La maggior parte delle funzionalità di rete aprono l'accesso a una rete e con Connessioni ibride si dispone solo dell'host e della porta che è possibile raggiungere.
* Scenari di copertura non inclusi in altri metodi di connettività in uscita
* Eseguire lo sviluppo nel servizio app in cui le app possono sfruttare facilmente le risorse locali 

Poiché la funzionalità consente l'accesso alle risorse locali senza un foro del firewall in ingresso, è popolare con gli sviluppatori. Le altre funzionalità di rete del servizio app in uscita sono correlate alla rete virtuale di Azure. Connessioni ibride non ha una dipendenza da un VNet e può essere usato per una più ampia gamma di esigenze di rete. È importante notare che il servizio app Connessioni ibride funzionalità non è importante o non è in grado di capire cosa si sta facendo sopra. Ciò significa che è possibile usarlo per accedere a un database, a un servizio Web o a un socket TCP arbitrario in un mainframe. La funzionalità essenzialmente effettua il tunneling dei pacchetti TCP. 

Sebbene Connessioni ibride sia popolare per lo sviluppo, viene usato anche in numerose applicazioni di produzione. È ideale per accedere a un servizio Web o a un database, ma non è appropriato per le situazioni che coinvolgono la creazione di molte connessioni. 

### <a name="gateway-required-vnet-integration"></a>Integrazione VNet necessaria per il gateway 

La funzionalità di integrazione VNet del servizio app del gateway consente all'app di effettuare richieste in **uscita** in una rete virtuale di Azure. La funzionalità funziona connettendo l'host in cui l'app è in esecuzione in un gateway di rete virtuale nella VNet con una VPN da punto a sito. Quando si configura la funzionalità, l'app ottiene uno degli indirizzi da punto a sito assegnati a ogni istanza. Questa funzionalità consente di accedere alle risorse in una reti virtuali classica o Gestione risorse in qualsiasi area. 

![Integrazione VNet necessaria per il gateway](media/networking-features/gw-vnet-integration.png)

Questa funzionalità consente di risolvere il problema dell'accesso alle risorse in altri reti virtuali e può anche essere usato per connettersi tramite un VNet a un altro reti virtuali o anche in locale. Non funziona con ExpressRoute connesso reti virtuali ma con le reti connesse VPN da sito a sito. Non è in genere consigliabile usare questa funzionalità da un'app in un ambiente del servizio app (ASE), perché l'ambiente del servizio app si trova già nella VNet. I casi di utilizzo che questa funzionalità risolve sono:

* Accesso alle risorse negli indirizzi IP privati nelle reti virtuali di Azure 
* Accesso alle risorse locali se è presente una VPN da sito a sito 
* Accesso alle risorse in reti virtuali con peering 

Quando questa funzionalità è abilitata, l'app userà il server DNS con cui è configurata la VNet di destinazione. Per altre informazioni su questa funzionalità, vedere la documentazione relativa all' [integrazione di VNet nel servizio app][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>Integrazione rete virtuale

La funzionalità di integrazione VNet necessaria per il gateway è molto utile, ma non risolve l'accesso alle risorse tra ExpressRoute. Oltre a dover raggiungere le connessioni ExpressRoute, è necessario che le app siano in grado di effettuare chiamate a servizi protetti con endpoint di servizio. Per risolvere entrambe le esigenze aggiuntive, è stata aggiunta un'altra funzionalità di integrazione VNet. La nuova funzionalità di integrazione VNet consente di collocare il back-end dell'app in una subnet in un Gestione risorse VNet nella stessa area. Questa funzionalità non è disponibile da un ambiente del servizio app, che è già presente in una VNet. Questa funzionalità offre i vantaggi seguenti:

* Accesso alle risorse in Gestione risorse reti virtuali nella stessa area
* Accesso alle risorse protette con gli endpoint di servizio 
* Accesso alle risorse accessibili attraverso connessioni VPN o ExpressRoute

![Integrazione rete virtuale](media/networking-features/vnet-integration.png)

Questa funzionalità è in anteprima e non deve essere usata per i carichi di lavoro di produzione. Per altre informazioni su questa funzionalità, leggere la documentazione relativa all' [integrazione di VNet nel servizio app][vnetintegration].

## <a name="app-service-environment"></a>Ambiente del servizio app 

Un ambiente del servizio app (ASE) è una distribuzione a tenant singolo del servizio app Azure eseguito nella VNet. L'ambiente del servizio app consente casi d'uso, ad esempio:

* Accedere alle risorse nella VNet
* Accedere alle risorse tra ExpressRoute
* Esporre le app con un indirizzo privato nella VNet 
* Accedere alle risorse tra gli endpoint del servizio 

Con un ambiente del servizio app, non è necessario usare funzionalità come l'integrazione VNet o gli endpoint di servizio perché l'ambiente del servizio app è già presente nella VNet. Se si vuole accedere a risorse come SQL o archiviazione su endpoint di servizio, abilitare gli endpoint di servizio nella subnet dell'ambiente del servizio app. Se si desidera accedere alle risorse in VNet, non è necessaria alcuna configurazione aggiuntiva.  Se si vuole accedere alle risorse tra ExpressRoute, si è già in VNet e non è necessario configurare nulla nell'ambiente del servizio app o nelle app al suo interno. 

Poiché le app in un ambiente del servizio app ILB possono essere esposte in un indirizzo IP privato, è possibile aggiungere facilmente i dispositivi WAF per esporre solo le app che si desidera a Internet e proteggere il resto. Si presta allo sviluppo semplificato di applicazioni multilivello. 

Ci sono alcuni elementi che non sono ancora possibili dal servizio multi-tenant proveniente da un ambiente del servizio app. Che includono:

* Esporre le app in un indirizzo IP privato
* Proteggere tutto il traffico in uscita con controlli di rete che non fanno parte dell'app 
* Ospitare le app in un singolo servizio tenant 
* Scalabilità fino a molte più istanze di quelle possibili nel servizio multi-tenant 
* Caricare i certificati client della CA privata per l'uso da app con endpoint protetti da un'autorità di certificazione privata 
* Forza TLS 1,1 tra tutte le app ospitate nel sistema senza alcuna possibilità di disabilitare a livello di app 
* Fornire un indirizzo in uscita dedicato per tutte le app nell'ambiente del servizio app non condivise con nessun cliente 

![Ambiente del servizio app in una VNet](media/networking-features/app-service-environment.png)

L'ambiente del servizio app offre la migliore soluzione per l'hosting di app isolate e dedicate, ma presenta alcuni problemi di gestione. Alcuni aspetti da considerare prima di usare un ambiente del servizio app operativo sono:
 
 * Un ambiente del servizio app viene eseguito all'interno della VNet, ma ha dipendenze esterne al VNet. Tali dipendenze devono essere consentite. Per altre informazioni, vedere [considerazioni sulla rete per un ambiente del servizio app][networkinfo]
 * Un ambiente del servizio app non si ridimensiona immediatamente come il servizio multi-tenant. È necessario anticipare le esigenze di scalabilità anziché ridimensionare in modo reattivo. 
 * A un ambiente del servizio app è associato un costo superiore. Per ottenere il massimo dall'ambiente del servizio app, è consigliabile pianificare l'inserimento di molti carichi di lavoro in un ambiente del servizio app piuttosto che utilizzarlo per piccoli sforzi.
 * Le app in un ambiente del servizio app non possono limitare l'accesso ad alcune app in un ambiente del servizio app e non in altre.
 * L'ambiente del servizio app si trova in una subnet e tutte le regole di rete vengono applicate a tutto il traffico da e verso l'ambiente del servizio app. Se si vuole assegnare regole di traffico in ingresso per una sola app, usare le restrizioni di accesso. 

## <a name="combining-features"></a>Combinazione di funzionalità 

Le funzionalità indicate per il servizio multi-tenant possono essere usate insieme per risolvere casi d'uso più complessi. Due dei casi d'uso più comuni sono descritti qui, ma sono solo esempi. Grazie alla conoscenza delle diverse funzionalità, è possibile risolvere quasi tutte le esigenze di architettura del sistema.

### <a name="inject-app-into-a-vnet"></a>Inserire l'app in una VNet

Una richiesta comune riguarda come inserire l'app in una VNet. L'inserimento dell'app in una VNet significa che gli endpoint in ingresso e in uscita per un'app si trovano all'interno di una VNet. L'ambiente del servizio app offre la soluzione migliore per risolvere questo problema, ma è possibile ottenere la maggior parte degli elementi necessari con nel servizio multi-tenant combinando le funzionalità. È ad esempio possibile ospitare applicazioni Intranet solo con indirizzi in ingresso e in uscita privati per:

* Creazione di un gateway applicazione con indirizzo privato in entrata e in uscita
* Sicurezza del traffico in ingresso verso l'app con gli endpoint di servizio 
* Usare la nuova integrazione di VNet per fare in modo che il back-end dell'app si trovi nella VNet 

Questo stile di distribuzione non fornisce un indirizzo dedicato per il traffico in uscita verso Internet o consente di bloccare tutto il traffico in uscita dall'app.  Questo stile di distribuzione fornirebbe una gran parte delle operazioni che verrebbero altrimenti apportate con un ambiente del servizio app. 

### <a name="create-multi-tier-applications"></a>Creazione di applicazioni multilivello

Un'applicazione multilivello è un'applicazione in cui è possibile accedere alle app back-end dell'API solo dal livello front-end. Per creare un'applicazione multilivello, è possibile:

* Usare l'integrazione di VNet per connettere il back-end dell'app Web front-end a una subnet in una VNet
* Usare gli endpoint di servizio per proteggere il traffico in ingresso verso l'app per le API in modo che provenga solo dalla subnet usata dall'app Web front-end

![app multilivello](media/networking-features/multi-tier-app.png)

È possibile avere più app front-end che usano la stessa app per le API usando l'integrazione VNet dalle altre app front-end e gli endpoint di servizio dall'app per le API con le rispettive subnet.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
