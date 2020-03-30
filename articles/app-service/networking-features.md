---
title: Funzionalità di rete
description: Informazioni sulle funzionalità di rete nel servizio app di Azure e sulle funzionalità necessarie per le esigenze di rete per la sicurezza o le funzionalità.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 03/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: bc3d35830d833c0223a400140c53e583d2f6ed37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475426"
---
# <a name="app-service-networking-features"></a>Funzionalità di rete del servizio app

Le applicazioni nel servizio app di Azure possono essere distribuite in diversi modi. Per impostazione predefinita, le app ospitate dal servizio app sono direttamente accessibili da Internet e possono raggiungere solo endpoint ospitati da Internet.By default, App Service hosted apps are directly internet accessible and can only reach internet hosted endpoints. Molte applicazioni dei clienti devono tuttavia controllare il traffico di rete in ingresso e in uscita. Nel servizio app sono disponibili diverse funzionalità per soddisfare tali esigenze. La sfida è sapere quale funzionalità deve essere utilizzata per risolvere un determinato problema. Questo documento ha lo scopo di aiutare i clienti a determinare quale funzionalità deve essere utilizzata in base ad alcuni casi d'uso di esempio.

Esistono due tipi di distribuzione principali per il servizio app di Azure.There are two primary deployment types for the Azure App Service. È disponibile il servizio pubblico multi-tenant, che ospita i piani del servizio app negli SKU dei prezzi Gratuito, Condiviso, Di base, Standard, Premium e Premiumv2. È quindi disponibile il singolo tenant App Service Environment (ASE), che ospita i piani del servizio app SKU isolato direttamente nella rete virtuale di Azure (VNet). Le funzionalità usate variano se ci si trova nel servizio multi-tenant o in un ambiente del servizio in e così. 

## <a name="multi-tenant-app-service-networking-features"></a>Funzionalità di rete del servizio app multi-tenantMulti-tenant App Service networking features 

Il servizio app di Azure è un sistema distribuito. I ruoli che gestiscono le richieste HTTP/HTTPS in ingresso sono denominati front-end. I ruoli che ospitano il carico di lavoro del cliente sono denominati lavoratori. Tutti i ruoli in una distribuzione del servizio app sono presenti in una rete multi-tenant. Poiché nella stessa unità di scala del servizio app sono presenti molti clienti diversi, non è possibile connettere la rete del servizio app direttamente alla rete. Invece di collegare le reti, abbiamo bisogno di funzionalità per gestire i diversi aspetti della comunicazione delle applicazioni. Le funzionalità che gestiscono le richieste per l'app non possono essere usate per risolvere i problemi durante l'esecuzione di chiamate dall'app. Allo stesso modo, le funzionalità che risolvono i problemi per le chiamate dall'app non possono essere usate per risolvere i problemi dell'app.  

| Funzionalità in entrata | Funzionalità in uscita |
|---------------------|-------------------|
| Indirizzo assegnato all'app | connessioni ibride |
| Restrizioni di accesso | Integrazione della rete virtuale richiesta dal gateway |
| Endpoint servizio | Integrazione rete virtuale |

Se non diversamente specificato, tutte le funzionalità possono essere utilizzate insieme. È possibile mescolare le funzionalità per risolvere i vari problemi.

## <a name="use-case-and-features"></a>Caso d'uso e caratteristiche

Per ogni caso d'uso, ci possono essere alcuni modi per risolvere il problema.  La funzione giusta da utilizzare è talvolta dovuta a motivi che vanno oltre il semplice caso d'uso stesso. I seguenti casi d'uso in ingresso suggeriscono come usare le funzionalità di rete del Servizio app per risolvere i problemi relativi al controllo del traffico diretto all'app. 
 
| Casi d'uso in ingresso | Funzionalità |
|---------------------|-------------------|
| Supportare le esigenze SSL basate su IP per l'appSupport IP-based SSL needs for your app | indirizzo assegnato all'app |
| Indirizzo in ingresso dedicato non condiviso per l'app | indirizzo assegnato all'app |
| Limitare l'accesso all'app da un set di indirizzi ben definiti | Restrizioni di accesso |
| Limitare l'accesso all'app dalle risorse in una rete virtuale | Endpoint servizio </br> Ambiente del servizio app con bilanciamento del carico interno </br> Endpoint privato (anteprima)Private endpoint (Preview) |
| Esporre l'app in un indirizzo IP privato nella rete virtuale | Ambiente del servizio app con bilanciamento del carico interno </br> IP privato per l'ingresso in un gateway applicazione con endpoint di servizio </br> Endpoint servizio (anteprima)Service Endpoint (Preview) |
| Proteggi la mia app con un WAF | Gateway dell'applicazione - ILB ASE </br> Gateway applicazione con endpoint di servizio </br> Azure Front Door with Access Restrictions |
| Bilanciare il carico del traffico verso le app in aree diverse | Azure Front Door with Access Restrictions | 
| Bilanciare il carico del traffico nella stessa area | [Gateway applicazione con endpoint di servizio][appgwserviceendpoints] | 

I seguenti casi d'uso in uscita suggeriscono come usare le funzionalità di rete del servizio app per risolvere le esigenze di accesso in uscita per la tua app. 

| Casi d'uso in uscita | Funzionalità |
|---------------------|-------------------|
| Accedere alle risorse in una rete virtuale di Azure nella stessa areaAccess resources in an Azure Virtual Network in the same region | Integrazione rete virtuale </br> ASE |
| Accedere alle risorse in una rete virtuale di Azure in un'area diversaAccess resources in an Azure Virtual Network in a different region | Integrazione della rete virtuale richiesta dal gateway </br> Peering dell'ambiente del servizio di sicurezza del servizio di sicurezza del servizio del servizio file del servizio utente e della |
| Accedere alle risorse protette con gli endpoint del servizioAccess resources secured with service endpoints | Integrazione rete virtuale </br> ASE |
| Accedere alle risorse in una rete privata non connessa ad AzureAccess resources in a private network not connected to Azure | connessioni ibride |
| Accedere alle risorse nei circuiti ExpressRouteAccess resources across ExpressRoute circuits | Integrazione rete virtuale </br> ASE | 
| Proteggere il traffico in uscita dall'app WebSecure outbound traffic from your web app | Integrazione della rete virtuale e gruppi di sicurezza di rete </br> ASE | 
| Instradare il traffico in uscita dall'app Web | Tabelle di instradamento e integrazione vNet </br> ASE | 


### <a name="default-networking-behavior"></a>Comportamento di rete predefinito

Le unità di scalabilità del servizio app di Azure supportano molti clienti in ogni distribuzione. I piani SKU gratuito e condiviso ospitano i carichi di lavoro dei clienti nei ruoli di lavoro multi-tenant. I piani Basic e versioni precedenti ospitano carichi di lavoro dei clienti dedicati a un solo piano di servizio app (ASP). Se si dispone di un piano di servizio app standard, tutte le app del piano verranno eseguite sullo stesso worker. Se si esegue la scalabilità orizzontale del worker, tutte le app in tale ASP verranno replicate in un nuovo worker per ogni istanza nell'ASP. I lavoratori utilizzati per Premiumv2 sono diversi dai lavoratori utilizzati per gli altri piani. Ogni distribuzione del servizio app ha un indirizzo IP che viene usato per tutto il traffico in ingresso verso le app in tale distribuzione del servizio app. Ci sono tuttavia ovunque da 4 a 11 indirizzi utilizzati per effettuare chiamate in uscita. Questi indirizzi sono condivisi da tutte le app nella distribuzione del servizio app. Gli indirizzi in uscita sono diversi in base ai diversi tipi di lavoratore. Ciò significa che gli indirizzi utilizzati dagli indirizzi ASP Free, Shared, Basic, Standard e Premium sono diversi da quelli utilizzati per le chiamate in uscita dagli ASP Premiumv2. Se si esaminano le proprietà dell'app, è possibile visualizzare gli indirizzi in ingresso e in uscita usati dall'app. Se è necessario bloccare una dipendenza con un ACL IP, utilizzare possibleOutboundAddresses. 

![Proprietà dell'app](media/networking-features/app-properties.png)

Il servizio app ha un numero di endpoint che vengono usati per gestire il servizio.  Tali indirizzi vengono pubblicati in un documento separato e si trovano anche nel tag del servizio IP AppServiceManagement.Those addresses are published in a separate document and are also in the AppServiceManagement IP service tag. Il tag AppServiceManagement viene usato solo con un ambiente del servizio app (ASE) in cui è necessario consentire tale traffico. Gli indirizzi in ingresso del servizio app vengono rilevati nel tag del servizio IP AppService.The App Service inbound addresses are tracked in the AppService IP service tag. Non esiste alcun tag di servizio IP che contiene gli indirizzi in uscita usati dal servizio app. 

![Diagramma in ingresso e in uscita del servizio app](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Indirizzo assegnato all'app 

La funzionalità dell'indirizzo assegnato all'app è un ramo della funzionalità SSL basata su IP ed è accessibile configurando SSL con l'app. Questa funzionalità può essere usata per le chiamate SSL basate su IP, ma può anche essere usata per fornire all'app un indirizzo di cui dispone solo. 

![Diagramma degli indirizzi assegnati all'app](media/networking-features/app-assigned-address.png)

Quando si usa un indirizzo assegnato all'app, il traffico passa comunque attraverso gli stessi ruoli front-end che gestiscono tutto il traffico in ingresso nell'unità di scalabilità del servizio app. Tuttavia, l'indirizzo assegnato all'app viene usato solo dall'app. I casi d'uso per questa funzionalità sono:

* Supportare le esigenze SSL basate su IP per l'appSupport IP-based SSL needs for your app
* Impostare un indirizzo dedicato per l'app che non sia condiviso con altri utenti

Per informazioni su come impostare un indirizzo nell'app, vedere l'esercitazione [Configurazione di SSL basato su IP][appassignedaddress]. 

### <a name="access-restrictions"></a>Restrizioni di accesso 

La funzionalità Restrizioni di accesso consente di filtrare le richieste **in ingresso** in base all'indirizzo IP di origine. L'azione di filtro viene eseguita nei ruoli front-end che si trovano a monte dei ruoli di lavoro in cui sono in esecuzione le app. Poiché i ruoli front-end sono a monte dei ruoli di lavoro, la funzionalità Restrizioni di accesso può essere considerata come protezione a livello di rete per le app. La funzionalità consente di creare un elenco di blocchi di indirizzi allow e deny valutati in ordine di priorità. È simile alla funzionalità del gruppo di sicurezza di rete (NSG) presente in Rete di Azure.It is similar to the Network Security Group (NSG) feature that exists in Azure Networking.  È possibile usare questa funzionalità in un ambiente del servizio simile all'ambiente del servizio simile all'ambiente del servizio simile all'ambiente del servizio windows o al servizio multi-tenant. Se utilizzato con un servizio app ILB, è possibile limitare l'accesso da blocchi di indirizzi privati.

![Restrizioni di accesso](media/networking-features/access-restrictions.png)

La funzionalità Restrizioni di accesso è utile negli scenari in cui si desidera limitare gli indirizzi IP che possono essere usati per raggiungere l'app. Tra i casi d'uso per questa funzione sono:

* Limitare l'accesso all'app da un set di indirizzi ben definiti 
* Limitare l'accesso all'accesso tramite un servizio di bilanciamento del carico, ad esempio Porta frontale di Azure.Restrict access to coming through a load-balancing service, such as Azure Front Door. Se si desidera bloccare il traffico in ingresso verso Azure Front Door, creare regole per consentire il traffico da 147.243.0.0/16 e 2a01:111:2050::/44. 

![Restrizioni di accesso con porta anteriore](media/networking-features/access-restrictions-afd.png)

Se si vuole bloccare l'accesso all'app in modo che sia raggiungibile solo dalle risorse nella rete virtuale di Azure, è necessario un indirizzo pubblico statico in qualsiasi origine sia presente nella rete virtuale. Se le risorse non dispongono di un indirizzo pubblico, è consigliabile usare la funzionalità Endpoint del servizio. Informazioni su come abilitare questa funzionalità con l'esercitazione sulla [configurazione delle restrizioni][iprestrictions]di accesso .

### <a name="service-endpoints"></a>Endpoint di servizio

Gli endpoint del servizio consentono di bloccare l'accesso **in ingresso** all'app in modo che l'indirizzo di origine provenga da un set di subnet selezionate. Questa funzionalità funziona in combinazione con le restrizioni di accesso IP. Gli endpoint del servizio vengono impostati nella stessa esperienza utente delle restrizioni di accesso IP. È possibile creare un elenco consenti/Rifiuta di regole di accesso che includa indirizzi pubblici e subnet nelle reti virtuali. Questa funzionalità supporta scenari quali:This feature supports scenarios such as:

![endpoint di servizio](media/networking-features/service-endpoints.png)

* Configurazione di un gateway applicazione con l'app per bloccare il traffico in ingresso nell'app
* Limitazione dell'accesso all'app alle risorse nella rete virtuale. Questo può includere macchine virtuali, ambiente del servizio app o anche altre app che usano l'integrazione della rete virtuale 

![endpoint di servizio con gateway applicazioneService endpoints with application gateway](media/networking-features/service-endpoints-appgw.png)

Per altre informazioni sulla configurazione degli endpoint del servizio con l'app, vedere l'esercitazione sulla configurazione delle restrizioni di accesso agli endpoint del servizioYou can learn more about configuring service endpoints with your app in the tutorial on [Configuring Service Endpoint Access Restrictions][serviceendpoints]

### <a name="private-endpoint-preview"></a>Endpoint privato (anteprima)Private Endpoint (Preview)

Private Endpoint is a network interface that connects you privately and securely to your Web App by Azure Private Link. L'endpoint privato usa un indirizzo IP privato dalla rete virtuale, portando in modo efficace l'app Web nella rete virtuale. Questa funzionalità è solo per i flussi **in ingresso** nell'app Web.
[Uso di endpoint privati per l'app Web di Azure (anteprima)Using Private Endpoints for Azure Web App (Preview)][privateendpoints]
 
### <a name="hybrid-connections"></a>connessioni ibride

Connessioni ibride del servizio app consente alle app di effettuare chiamate **in uscita** agli endpoint TCP specificati. L'endpoint può essere locale, in una rete virtuale o in qualsiasi punto che consente il traffico in uscita verso Azure sulla porta 443.The endpoint can be on-premises, in a VNet or anywhere that allows outbound traffic to Azure on port 443. La funzionalità richiede l'installazione di un agente di inoltro denominato Gestione connessione ibrida (HCM) in un host Windows Server 2012 o versione più recente. HCM deve essere in grado di raggiungere l'inoltro di Azure sulla porta 443.The HCM needs to be able to reach Azure Relay at port 443. HCM può essere scaricato dall'interfaccia utente Connessioni ibride del servizio app nel portale. 

![Flusso di rete delle connessioni ibrideHybrid Connections network flow](media/networking-features/hybrid-connections.png)

La funzionalità Connessioni ibride del servizio app si basa sulla funzionalità Connessioni ibride di inoltro di Azure.The App Service Hybrid Connections feature is built on the Azure Relay Hybrid Connections capability. Il servizio app usa una forma specializzata della funzionalità che supporta solo l'esecuzione di chiamate in uscita dall'app a un host e una porta TCP. Questo host e la porta devono solo essere risolti nell'host in cui è installato HCM. Quando l'app, in Servizio app, esegue una ricerca DNS sull'host e sulla porta definiti nella connessione ibrida, il traffico viene automaticamente reindirizzato per passare attraverso la connessione ibrida ed eseguire la connessione ibrida. Per altre informazioni sulle connessioni ibride, leggere la documentazione sulle connessioni ibride del [servizio appTo][hybridconn] learn more about Hybrid Connections, read the documentation on App Service Hybrid Connections

Questa funzione è comunemente utilizzata per:

* Accedere alle risorse in reti private non connesse ad Azure con una VPN o ExpressRouteAccess resources in private networks that are not connected to Azure with a VPN or ExpressRoute
* Supporta lo spostamento e lo spostamento delle app locali nel servizio app senza dover spostare anche i database di supporto  
* Fornire in modo sicuro l'accesso a un singolo host e porta per ogni connessione ibrida. La maggior parte delle funzionalità di rete ha accesso aperto a una rete e con connessioni ibride si hanno solo il singolo host e porta che è possibile raggiungere.
* Scenari di copertura non coperti da altri metodi di connettività in uscita
* Eseguire lo sviluppo nel servizio app in cui le app possono facilmente sfruttare le risorse localiPerform development in App Service where the apps can easily leverage on-premises resources 

Poiché la funzionalità consente l'accesso alle risorse locali senza un foro del firewall in ingresso, è popolare tra gli sviluppatori. Le altre funzionalità di rete del servizio app in uscita sono correlate a Rete virtuale di Azure.The other outbound App Service networking features are very Azure Virtual Networking related. Connessioni ibride non ha una dipendenza da passare attraverso una rete virtuale e può essere utilizzato per una più ampia gamma di esigenze di rete. È importante notare che la funzionalità Connessioni ibride del servizio app non si preoccupa o sa cosa si sta facendo su di esso. Vale a dire che è possibile utilizzarlo per accedere a un database, un servizio web o un socket TCP arbitrario su un mainframe. La funzione essenzialmente collega i pacchetti TCP. 

Mentre Hybrid Connections è popolare per lo sviluppo, viene utilizzato anche in numerose applicazioni di produzione. È ideale per accedere a un servizio Web o a un database, ma non è appropriato per le situazioni che comportano la creazione di molte connessioni. 

### <a name="gateway-required-vnet-integration"></a>Integrazione della rete virtuale richiesta dal gateway 

La funzionalità di integrazione della rete virtuale del servizio app necessaria per il gateway consente all'app di effettuare richieste **in uscita** in una rete virtuale di Azure.The gateway required App Service VNet Integration feature enables your app to make outbound requests into an Azure Virtual Network. La funzionalità funziona connettendo l'host su cui è in esecuzione l'app a un gateway di rete virtuale nella rete virtuale con una VPN da punto a sito. Quando configuri la funzionalità, l'app ottiene uno degli indirizzi da punto a sito assegnati a ogni istanza. Questa funzionalità consente di accedere alle risorse nelle reti virtuali classico o in Gestione risorse in qualsiasi area. 

![Integrazione della rete virtuale richiesta dal gateway](media/networking-features/gw-vnet-integration.png)

Questa funzionalità risolve il problema di accedere alle risorse in altre reti virtuali e può anche essere utilizzata per connettersi tramite una rete virtuale ad altre reti virtuali o anche in locale. Non funziona con le reti virtuali connesse ExpressRoute, ma con le reti connesse vpn da sito a sito. In genere non è appropriato usare questa funzionalità da un'app in un ambiente del servizio app (ASE), perché l'ambiente del servizio app è già presente nella rete virtuale. I casi d'uso risolti da questa funzionalità sono:

* Accesso alle risorse su indirizzi IP privati nelle reti virtuali di AzureAccessing resources on private IPs in your Azure virtual networks 
* Accesso alle risorse in locale se è presente una VPN da sito a sito 
* Accesso alle risorse nelle reti virtuali con peeringAccessing resources in peered VNets 

Quando questa funzionalità è abilitata, l'app utilizzerà il server DNS con cui è configurata la rete virtuale di destinazione. Ulteriori informazioni su questa funzionalità sono riportate nella documentazione relativa [all'integrazione della rete virtuale del servizio app.][vnetintegrationp2s] 

### <a name="vnet-integration"></a>Integrazione rete virtuale

La funzionalità di integrazione della rete virtuale richiesta dal gateway è molto utile ma non risolve comunque l'accesso alle risorse in ExpressRoute.The gateway required VNet Integration feature is very useful but still does not solve accessing resources across ExpressRoute. Oltre a dover raggiungere le connessioni ExpressRoute, è necessario che le app siano in grado di effettuare chiamate a servizi protetti dall'endpoint del servizio. Per risolvere entrambe queste esigenze aggiuntive, è stata aggiunta un'altra funzionalità di integrazione della rete virtuale. La nuova funzionalità di integrazione della rete virtuale consente di inserire il back-end dell'app in una subnet in una rete virtuale di Resource Manager nella stessa area. Questa funzionalità non è disponibile da un ambiente del servizio app, che si trova già in una rete virtuale. Questa funzione consente di:

* Accesso alle risorse nelle reti virtuali di Resource Manager nella stessa areaAccessing resources in Resource Manager VNets in the same region
* Accesso alle risorse protette con gli endpoint del servizioAccessing resources that are secured with service endpoints 
* Accesso alle risorse accessibili tramite ExpressRoute o connessioni VPNAccessing resources that are accessible across ExpressRoute or VPN connections
* Protezione di tutto il traffico in uscita 
* Forzare il tunneling di tutto il traffico in uscita. 

![Integrazione rete virtuale](media/networking-features/vnet-integration.png)

Per altre informazioni su questa funzionalità, leggere la documentazione [sull'integrazione della rete virtuale][vnetintegration]del servizio app .

## <a name="app-service-environment"></a>Ambiente del servizio app 

Un ambiente del servizio app (AS) è una distribuzione tenant singolo del servizio app di Azure in esecuzione nella rete virtuale. L'ASE abilita casi d'uso come:

* Accedere alle risorse nella rete virtualeAccess resources in your VNet
* Accedere alle risorse in ExpressRouteAccess resources across ExpressRoute
* Esporre le app con un indirizzo privato nella rete virtuale 
* Accedere alle risorse tra gli endpoint del servizioAccess resources across service endpoints 

Con un ambiente del servizio all'utente, non è necessario usare funzionalità come l'integrazione della rete virtuale o gli endpoint del servizio perché l'ambiente del servizio in linea di rete è già presente nella rete virtuale. Se si desidera accedere a risorse come SQL o Archiviazione sugli endpoint del servizio, abilitare gli endpoint del servizio nella subnet del servizio app-AS. Se si desidera accedere alle risorse nella rete virtuale, non è necessaria alcuna configurazione aggiuntiva.  Se si vuole accedere alle risorse in ExpressRoute, si è già nella rete virtuale e non è necessario configurare nulla nell'app dell'app o nelle app in essa al suo interno. 

Poiché le app in un ambiente del servizio app ILB possono essere esposte su un indirizzo IP privato, è possibile aggiungere facilmente dispositivi WAF per esporre solo le app che si desidera a Internet e mantenere il resto sicuro. Si presta a un facile sviluppo di applicazioni multilivello. 

Esistono alcune operazioni che non sono ancora possibili dal servizio multi-tenant che provengono da un ambiente del servizio app. Questi includono cose come:

* Esporre le app su un indirizzo IP privato
* Proteggi tutto il traffico in uscita con controlli di rete che non fanno parte della tua app 
* Ospitare le app in un singolo servizio tenant 
* Scalabilità verticale fino a molte più istanze di quelle possibili nel servizio multi-tenant 
* Caricare i certificati client CA privati per l'utilizzo da parte delle app con endpoint protetti da CA privata 
* Forza TLS 1.1 in tutte le app ospitate nel sistema senza alcuna possibilità di disabilitazione a livello di app 
* Fornire un indirizzo in uscita dedicato per tutte le app nell'app che non sono condivise con i clienti 

![ASE in a VNet](media/networking-features/app-service-environment.png)

L'ambiente del servizio app offre la storia migliore per l'hosting di app isolate e dedicate, ma offre alcune sfide di gestione. Alcuni aspetti da considerare prima di usare un ambiente del servizio all'utente compatibile con le unità di servizio operative sono:Some things to consider before using an operational ASE are:
 
 * Un servizio app viene eseguito all'interno della rete virtuale ma ha dipendenze all'esterno della rete virtuale. Tali dipendenze devono essere consentite. Per altre informazioni, vedere Considerazioni sulla rete per un ambiente del [servizio appRead][networkinfo] more in Networking considerations for an App Service Environment
 * Un ambiente del servizio il com>, non viene ridimensionato immediatamente come il servizio multi-tenant. È necessario anticipare le esigenze di scalabilità anziché ridimensionare in modo reattivo. 
 * Un ase ha un costo iniziale più alto associato ad esso. Per ottenere il massimo dall'app, è consigliabile pianificare l'inserimento di molti carichi di lavoro in un'unica app anziché utilizzarlo per piccoli sforzi
 * Le app in un servizio app non possono limitare l'accesso ad alcune app in un servizio app e non ad altre.
 * L'ambiente del servizio app si trova in una subnet e tutte le regole di rete si applicano a tutto il traffico da e verso tale ambiente del servizio app. Se si desidera assegnare regole di traffico in ingresso per una sola app, usare Restrizioni di accesso. 

## <a name="combining-features"></a>Combinazione di funzionalità 

Le funzionalità note per il servizio multi-tenant possono essere utilizzate insieme per risolvere casi d'uso più elaborati. Due dei casi d'uso più comuni sono descritti qui, ma sono solo esempi. Comprendendo le operazioni eseguite dalle varie funzionalità, è possibile risolvere quasi tutte le esigenze dell'architettura di sistema.

### <a name="inject-app-into-a-vnet"></a>Inserire l'app in una rete virtualeInject app into a VNet

Una richiesta comune riguarda come inserire l'app in una rete virtuale. L'inserimento dell'app in una rete virtuale significa che gli endpoint in ingresso e in uscita per un'app si trovano all'interno di una rete virtuale. L'ambiente del servizio app offre la soluzione migliore per risolvere questo problema, ma è possibile ottenere la maggior parte di ciò che è necessario nel servizio multi-tenant combinando le funzionalità. Ad esempio, è possibile ospitare solo applicazioni Intranet con indirizzi privati in ingresso e in uscita:

* Creazione di un gateway applicazione con indirizzo privato in ingresso e in uscitaCreating an Application Gateway with private inbound and outbound address
* Protezione del traffico in ingresso verso l'app con gli endpoint del servizioSecuring inbound traffic to your app with service endpoints 
* Usare la nuova integrazione della rete virtuale in modo che il back-end dell'app sia nella rete virtualeUse the new VNet Integration so the backend of your app is in your VNet 

Questo stile di distribuzione non fornisce un indirizzo dedicato per il traffico in uscita verso Internet né offre la possibilità di bloccare tutto il traffico in uscita dall'app.  Questo stile di distribuzione ti darebbe molto di ciò che altrimenti otterresti solo con un'insieme di informazioni del genere. 

### <a name="create-multi-tier-applications"></a>Creare applicazioni multilivello

Un'applicazione multilivello è un'applicazione in cui è possibile accedere alle app back-end API solo dal livello front-end. Per creare un'applicazione multilivello, è possibile:To create a multi-tier application, you can:

* Usare l'integrazione della rete virtuale per connettere il back-end dell'app Web front-end a una subnet in una rete virtualeUse VNet Integration to connect the back-end of your front-end web app with a subnet in a VNet
* Usare gli endpoint del servizio per proteggere il traffico in ingresso verso l'app API solo dalla subnet usata dall'app Web front-endUse service endpoints to secure inbound traffic to your API app to only coming from the subnet used by your front-end web app

![app multilivello](media/networking-features/multi-tier-app.png)

È possibile avere più app front-end usare la stessa app API usando l'integrazione della rete virtuale dalle altre app front-end e gli endpoint del servizio dall'app API con le relative subnet.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/configure-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
[appgwserviceendpoints]: https://docs.microsoft.com/azure/app-service/networking/app-gateway-with-service-endpoints
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
