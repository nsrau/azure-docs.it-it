---
title: Funzionalità di rete
description: Informazioni sulle funzionalità di rete nel servizio app Azure e informazioni sulle funzionalità necessarie per la sicurezza o altre funzionalità.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 10/18/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 5d950598e4a0af86ac37b53722e80eb4ef0a71a4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183057"
---
# <a name="app-service-networking-features"></a>Funzionalità di rete del servizio app

È possibile distribuire le applicazioni nel servizio app Azure in diversi modi. Per impostazione predefinita, le app ospitate nel servizio app sono accessibili direttamente tramite Internet e possono raggiungere solo endpoint ospitati da Internet. Tuttavia, per molte applicazioni, è necessario controllare il traffico di rete in ingresso e in uscita. Nel servizio app sono disponibili diverse funzionalità che consentono di soddisfare queste esigenze. La sfida è conoscere la funzionalità da utilizzare per risolvere un determinato problema. Questo articolo consente di determinare la funzionalità da usare, in base ad alcuni casi d'uso di esempio.

Per app Azure servizio sono disponibili due tipi di distribuzione principali: 
- Il servizio pubblico multi-tenant ospita piani di servizio app negli SKU di prezzo gratuito, condiviso, Basic, standard, Premium, PremiumV2 e PremiumV3. 
- Il ambiente del servizio app single-tenant (ASE) ospita piani di servizio App SKU isolati direttamente nella rete virtuale di Azure. 

Le funzionalità usate variano a seconda che ci si trovi nel servizio multi-tenant o in un ambiente del servizio app. 

## <a name="multitenant-app-service-networking-features"></a>Funzionalità di rete del servizio app multi-tenant 

Il servizio app Azure è un sistema distribuito. I ruoli che gestiscono le richieste HTTP o HTTPS in ingresso sono detti *front-end*. I ruoli che ospitano il carico di lavoro del cliente sono detti thread di *lavoro*. Tutti i ruoli in una distribuzione del servizio app sono presenti in una rete multi-tenant. Poiché nella stessa unità di scala del servizio app sono presenti molti clienti diversi, non è possibile connettere la rete del servizio app direttamente alla rete. 

Anziché connettere le reti, è necessario disporre di funzionalità per gestire i vari aspetti della comunicazione tra le applicazioni. Le funzionalità che gestiscono le richieste *all'* app non possono essere usate per risolvere i problemi quando si effettuano chiamate *dall'* app. Analogamente, le funzionalità che risolvono i problemi per le chiamate dall'app non possono essere usate per risolvere i problemi dell'app.  

| Funzionalità in ingresso | Funzionalità in uscita |
|---------------------|-------------------|
| Indirizzo assegnato dall'app | connessioni ibride |
| Restrizioni di accesso | Integrazione rete virtuale richiesta dal gateway |
| Endpoint di servizio | Integrazione rete virtuale |
| Endpoint privati ||

Oltre alle eccezioni indicate, è possibile utilizzare tutte queste funzionalità insieme. È possibile combinare le funzionalità per risolvere i problemi.

## <a name="use-cases-and-features"></a>Casi d'uso e funzionalità

Per qualsiasi caso d'uso specifico, potrebbero essere disponibili alcuni modi per risolvere il problema. La scelta della funzionalità migliore a volte va oltre il caso d'uso. I seguenti casi di utilizzo in ingresso suggeriscono come usare le funzionalità di rete del servizio app per risolvere i problemi relativi al controllo del traffico verso l'app:
 
| Caso di utilizzo in ingresso | Feature |
|---------------------|-------------------|
| Supportare le esigenze SSL basate su IP per l'app | Indirizzo assegnato dall'app |
| Supporto dell'indirizzo in ingresso dedicato non condiviso per l'app | Indirizzo assegnato dall'app |
| Limitare l'accesso all'app da un set di indirizzi ben definiti | Restrizioni di accesso |
| Limitare l'accesso all'app dalle risorse in una rete virtuale | Endpoint di servizio </br> Ambiente del servizio app con bilanciamento del carico interno </br> Endpoint privati |
| Esporre l'app in un indirizzo IP privato nella rete virtuale | Ambiente del servizio app con bilanciamento del carico interno </br> Endpoint privati </br> IP privato per il traffico in ingresso in un'istanza del gateway applicazione con endpoint di servizio |
| Proteggi la tua app con un web application firewall (WAF) | Gateway applicazione e ambiente del servizio app ILB </br> Gateway applicazione con endpoint privati </br> Gateway applicazione con endpoint di servizio </br> Sportello anteriore di Azure con restrizioni di accesso |
| Bilanciare il carico del traffico verso le app in aree diverse | Sportello anteriore di Azure con restrizioni di accesso | 
| Bilanciare il carico del traffico nella stessa area | [Gateway applicazione con endpoint di servizio][appgwserviceendpoints] | 

I seguenti casi di utilizzo in uscita suggeriscono come usare le funzionalità di rete del servizio app per risolvere le esigenze di accesso in uscita per l'app:

| Caso di utilizzo in uscita | Feature |
|---------------------|-------------------|
| Accedere alle risorse in una rete virtuale di Azure nella stessa area | Integrazione rete virtuale </br> ASE |
| Accedere alle risorse in una rete virtuale di Azure in un'area diversa | Integrazione rete virtuale richiesta dal gateway </br> Ambiente del servizio app e peering di rete virtuale |
| Accedere alle risorse protette con gli endpoint di servizio | Integrazione rete virtuale </br> ASE |
| Accedere alle risorse in una rete privata non connessa ad Azure | connessioni ibride |
| Accedi alle risorse tra circuiti ExpressRoute di Azure | Integrazione rete virtuale </br> ASE | 
| Proteggere il traffico in uscita dall'app Web | Integrazione di VNet e gruppi di sicurezza di rete </br> ASE | 
| Instradare il traffico in uscita dall'app Web | Integrazione di VNet e tabelle di route </br> ASE | 


### <a name="default-networking-behavior"></a>Comportamento di rete predefinito

App Azure unità di scala del servizio supportano molti clienti in ogni distribuzione. I piani di SKU gratuito e condiviso ospitano i carichi di lavoro dei clienti sui ruoli di lavoro multi-tenant. I piani Basic e superiori ospitano i carichi di lavoro dei clienti dedicati a un solo piano di servizio app. Se si dispone di un piano di servizio app standard, tutte le app del piano vengono eseguite nello stesso thread di lavoro. Se si ridimensiona il lavoro, tutte le app nel piano di servizio app verranno replicate in un nuovo ruolo di lavoro per ogni istanza nel piano di servizio app. 

#### <a name="outbound-addresses"></a>Indirizzi in uscita

Le macchine virtuali di lavoro sono suddivise in gran parte dai piani di servizio app. I piani gratuito, condiviso, Basic, standard e Premium utilizzano tutti lo stesso tipo di macchina virtuale del ruolo di lavoro. Il piano PremiumV2 usa un altro tipo di macchina virtuale. PremiumV3 usa ancora un altro tipo di macchina virtuale. Quando si modifica la famiglia di macchine virtuali, si ottiene un set diverso di indirizzi in uscita. Se si esegue la scalabilità da standard a PremiumV2, gli indirizzi in uscita cambieranno. Se si esegue la scalabilità da PremiumV2 a PremiumV3, gli indirizzi in uscita cambieranno. In alcune unità di scala precedenti, entrambi gli indirizzi in ingresso e in uscita cambieranno quando si passa da standard a PremiumV2. 

Esistono diversi indirizzi usati per le chiamate in uscita. Gli indirizzi in uscita usati dall'app per effettuare chiamate in uscita sono elencati nelle proprietà dell'app. Questi indirizzi sono condivisi da tutte le app in esecuzione nella stessa famiglia di macchine virtuali di lavoro nella distribuzione del servizio app. Se si vogliono visualizzare tutti gli indirizzi che l'app può usare in un'unità di scala, è presente una proprietà denominata `possibleOutboundAddresses` che li elenca. 

![Screenshot che mostra le proprietà dell'app.](media/networking-features/app-properties.png)

Il servizio app include diversi endpoint usati per gestire il servizio.  Questi indirizzi vengono pubblicati in un documento separato e sono inclusi anche nel `AppServiceManagement` tag del servizio IP. Il `AppServiceManagement` tag viene usato solo negli ambienti del servizio app in cui è necessario consentire tale traffico. Gli indirizzi in ingresso del servizio app vengono rilevati nel `AppService` tag del servizio IP. Non è presente alcun tag del servizio IP che contiene gli indirizzi in uscita usati dal servizio app. 

![Diagramma che mostra il traffico in ingresso e in uscita del servizio app.](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Indirizzo assegnato dall'app 

La funzionalità di indirizzo assegnato dall'app è una propaggine della funzionalità SSL basata su IP. Per accedervi, configurare SSL con l'app. È possibile usare questa funzionalità per le chiamate SSL basate su IP. È anche possibile usarlo per assegnare all'app un indirizzo che possiede solo. 

![Diagramma che illustra l'indirizzo assegnato dall'app.](media/networking-features/app-assigned-address.png)

Quando si usa un indirizzo assegnato dall'app, il traffico passa ancora attraverso gli stessi ruoli front-end che gestiscono tutto il traffico in ingresso nell'unità di scala del servizio app. Tuttavia, l'indirizzo assegnato all'app viene usato solo dall'app. Casi d'uso per questa funzionalità:

* Supportare le esigenze SSL basate su IP per l'app.
* Impostare un indirizzo dedicato per l'app che non è condivisa.

Per informazioni su come impostare un indirizzo nell'app, vedere [aggiungere un certificato TLS/SSL nel servizio app Azure][appassignedaddress]. 

### <a name="access-restrictions"></a>Restrizioni di accesso 

Le restrizioni di accesso consentono di filtrare le richieste in *ingresso* . L'azione di filtro si verifica nei ruoli front-end che sono upstream dai ruoli di lavoro in cui sono in esecuzione le app. Poiché i ruoli front-end sono upstream dai ruoli di lavoro, è possibile considerare le restrizioni di accesso come protezione a livello di rete per le app. 

Questa funzionalità consente di compilare un elenco di regole di autorizzazione e di negazione che vengono valutate in ordine di priorità. È simile alla funzionalità gruppo di sicurezza di rete (NSG) in rete di Azure. È possibile usare questa funzionalità in un ambiente del servizio app o nel servizio multi-tenant. Quando viene usato con un ambiente del servizio app ILB o un endpoint privato, è possibile limitare l'accesso da blocchi di indirizzi privati.
> [!NOTE]
> È possibile configurare fino a 512 regole di restrizione dell'accesso per ogni app. 

![Diagramma che illustra le restrizioni di accesso.](media/networking-features/access-restrictions.png)

#### <a name="ip-based-access-restriction-rules"></a>Regole di restrizione dell'accesso basato su IP

La funzionalità restrizioni di accesso basato su IP consente di limitare gli indirizzi IP che possono essere usati per raggiungere l'app. IPv4 e IPv6 sono entrambi supportati. Alcuni casi d'uso per questa funzionalità:
* Limitare l'accesso all'app da un set di indirizzi ben definiti. 
* Limitare l'accesso al traffico in arrivo attraverso un servizio di bilanciamento del carico, ad esempio il front-end di Azure. Se si vuole bloccare il traffico in ingresso verso la porta anteriore di Azure, creare regole per consentire il traffico da 147.243.0.0/16 e 2a01:111:2050::/44. 

Per informazioni su come abilitare questa funzionalità, vedere [Configuring Access Restrictions][iprestrictions].

#### <a name="access-restriction-rules-based-on-service-endpoints"></a>Regole di restrizione dell'accesso basate sugli endpoint di servizio 

Gli endpoint di servizio consentono di bloccare l'accesso in *ingresso* all'app in modo che l'indirizzo di origine debba provenire da un set di subnet selezionato. Questa funzionalità funziona insieme alle restrizioni di accesso IP. Gli endpoint di servizio non sono compatibili con il debug remoto. Se si vuole usare il debug remoto con l'app, il client non può trovarsi in una subnet in cui sono abilitati gli endpoint di servizio. Il processo per l'impostazione degli endpoint di servizio è simile al processo di impostazione delle restrizioni di accesso IP. È possibile compilare un elenco Consenti/Nega di regole di accesso che include indirizzi pubblici e subnet nelle reti virtuali. 

Alcuni casi d'uso per questa funzionalità:

* Configurare un gateway applicazione con l'app per bloccare il traffico in ingresso verso l'app.
* Limitare l'accesso all'app alle risorse nella rete virtuale. Queste risorse possono includere VM, gli ambienti o anche altre app che usano l'integrazione con VNet. 

![Diagramma che illustra l'uso degli endpoint di servizio con il gateway applicazione.](media/networking-features/service-endpoints-appgw.png)

Per altre informazioni sulla configurazione degli endpoint di servizio con l'app, vedere [restrizioni di accesso al servizio app Azure][serviceendpoints].

### <a name="private-endpoint"></a>Endpoint privato

L'endpoint privato è un'interfaccia di rete che si connette privatamente e in modo sicuro all'app Web dal collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della rete virtuale, portando in realtà l'app Web nella rete virtuale. Questa funzionalità è solo per i flussi in *ingresso* per l'app Web.
Per altre informazioni, vedere [uso di endpoint privati per l'app Web di Azure][privateendpoints].

Alcuni casi d'uso per questa funzionalità:

* Limitare l'accesso all'app dalle risorse in una rete virtuale. 
* Esporre l'app in un indirizzo IP privato nella rete virtuale. 
* Proteggi la tua app con un WAF.

Gli endpoint privati impediscono il exfiltration dei dati perché l'unica cosa che è possibile raggiungere nell'endpoint privato è l'app con cui è configurata. 
 
### <a name="hybrid-connections"></a>connessioni ibride

Il servizio app Connessioni ibride consente alle app di effettuare chiamate in *uscita* agli endpoint TCP specificati. L'endpoint può trovarsi in locale, in una rete virtuale o in un punto qualsiasi che consente il traffico in uscita verso Azure sulla porta 443. Per utilizzare questa funzionalità, è necessario installare un agente di inoltro denominato Gestione connessione ibrida in un host Windows Server 2012 o versione successiva. Gestione connessione ibrida deve essere in grado di raggiungere il relè di Azure sulla porta 443. È possibile scaricare Gestione connessione ibrida dall'interfaccia utente di Connessioni ibride del servizio app nel portale. 

![Diagramma che mostra il flusso di rete Connessioni ibride.](media/networking-features/hybrid-connections.png)

Il servizio app Connessioni ibride è basato sulla funzionalità Connessioni ibride di inoltro di Azure. Il servizio app usa un modulo specializzato della funzionalità che supporta solo la creazione di chiamate in uscita dall'app a un host e una porta TCP. Questo host e la porta devono essere risolti solo nell'host in cui è installato Gestione connessione ibrida. 

Quando l'app, nel servizio app, esegue una ricerca DNS sull'host e sulla porta definiti nella connessione ibrida, il traffico viene reindirizzato automaticamente per passare attraverso la connessione ibrida e fuori Gestione connessione ibrida. Per altre informazioni, vedere [connessioni ibride del servizio app][hybridconn].

Questa funzionalità viene in genere utilizzata per:

* Accedere alle risorse in reti private non connesse ad Azure con una VPN o ExpressRoute.
* Supportare la migrazione delle app locali al servizio app senza la necessità di spostare i database di supporto.  
* Fornire l'accesso con una maggiore sicurezza a un singolo host e una porta per ogni connessione ibrida. La maggior parte delle funzionalità di rete aprono l'accesso a una rete. Con Connessioni ibride è possibile raggiungere solo il singolo host e la stessa porta.
* Scenari di copertura non inclusi in altri metodi di connettività in uscita.
* Eseguire lo sviluppo nel servizio app in modo da consentire alle app di usare facilmente le risorse locali. 

Poiché questa funzionalità consente l'accesso alle risorse locali senza un foro del firewall in ingresso, è molto popolare con gli sviluppatori. Le altre funzionalità di rete del servizio app in uscita sono correlate alla rete virtuale di Azure. Connessioni ibride non dipende dall'attraversamento di una rete virtuale. Può essere usato per una vasta gamma di esigenze di rete. 

Si noti che il servizio app Connessioni ibride non è a conoscenza di ciò che si sta facendo sopra. Quindi è possibile usarlo per accedere a un database, a un servizio Web o a un socket TCP arbitrario in un mainframe. La funzionalità essenzialmente effettua il tunneling dei pacchetti TCP. 

Connessioni ibride è popolare per lo sviluppo, ma viene usato anche nelle applicazioni di produzione. È ideale per accedere a un servizio Web o a un database, ma non è appropriato per le situazioni che comportano la creazione di molte connessioni. 

### <a name="gateway-required-vnet-integration"></a>Integrazione rete virtuale richiesta dal gateway 

Gateway: il servizio app necessario per l'integrazione di VNet consente all'app di effettuare richieste in *uscita* in una rete virtuale di Azure. La funzionalità funziona connettendo l'host in cui l'app è in esecuzione in un gateway di rete virtuale nella rete virtuale usando una VPN da punto a sito. Quando si configura la funzionalità, l'app ottiene uno degli indirizzi da punto a sito assegnati a ogni istanza. Questa funzionalità consente di accedere alle risorse nelle reti virtuali classiche o Azure Resource Manager in qualsiasi area. 

![Diagramma che illustra l'integrazione VNet necessaria per il gateway.](media/networking-features/gw-vnet-integration.png)

Questa funzionalità consente di risolvere il problema dell'accesso alle risorse in altre reti virtuali. Può anche essere usato per connettersi tramite una rete virtuale ad altre reti virtuali o locali. Non funziona con le reti virtuali connesse a ExpressRoute, ma funziona con le reti connesse VPN da sito a sito. Non è in genere consigliabile usare questa funzionalità da un'app in un ambiente del servizio app (ASE) perché l'ambiente del servizio app è già presente nella rete virtuale. Casi d'uso per questa funzionalità:

* Accedere alle risorse negli indirizzi IP privati nelle reti virtuali di Azure. 
* Accedere alle risorse locali se è presente una VPN da sito a sito. 
* Accedere alle risorse nelle reti virtuali con peering. 

Quando questa funzionalità è abilitata, l'app userà il server DNS con cui è configurata la rete virtuale di destinazione. Per altre informazioni su questa funzionalità, vedere [integrazione del servizio app VNet][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>Integrazione rete virtuale

Gateway: l'integrazione VNet necessaria è utile, ma non risolve il problema dell'accesso alle risorse tra ExpressRoute. Oltre a dover raggiungere le connessioni ExpressRoute, è necessario che le app siano in grado di effettuare chiamate ai servizi protetti dall'endpoint di servizio. Un'altra funzionalità di integrazione VNet può soddisfare queste esigenze. 

La nuova funzionalità di integrazione VNet consente di collocare il back-end dell'app in una subnet in una rete virtuale Gestione risorse nella stessa area dell'app. Questa funzionalità non è disponibile da un ambiente del servizio app, che è già presente in una rete virtuale. Casi d'uso per questa funzionalità:

* Accedere alle risorse in Gestione risorse reti virtuali nella stessa area.
* Accedere alle risorse protette con gli endpoint di servizio. 
* Accedere alle risorse accessibili attraverso connessioni VPN o ExpressRoute.
* Contribuire alla protezione di tutto il traffico in uscita. 
* Forzare il tunneling di tutto il traffico in uscita. 

![Diagramma che illustra l'integrazione di VNet.](media/networking-features/vnet-integration.png)

Per altre informazioni, vedere [integrazione del servizio app VNet][vnetintegration].

### <a name="app-service-environment"></a>Ambiente del servizio app 

Un ambiente del servizio app (ASE) è una distribuzione a tenant singolo del servizio app Azure eseguito nella rete virtuale. Alcuni casi per questa funzionalità:

* Accedere alle risorse nella rete virtuale.
* Accedere alle risorse tra ExpressRoute.
* Esporre le app con un indirizzo privato nella rete virtuale. 
* Accedere alle risorse tra gli endpoint del servizio. 

Con un ambiente del servizio app, non è necessario usare funzionalità come l'integrazione VNet o gli endpoint di servizio perché l'ambiente del servizio app è già presente nella rete virtuale. Se si vuole accedere a risorse come SQL o archiviazione di Azure su endpoint di servizio, abilitare gli endpoint di servizio nella subnet dell'ambiente del servizio app. Se si desidera accedere alle risorse nella rete virtuale, non è necessario eseguire alcuna configurazione aggiuntiva. Se si vuole accedere alle risorse tra ExpressRoute, si è già nella rete virtuale e non è necessario configurare alcun elemento nell'ambiente del servizio app o nelle app al suo interno. 

Poiché le app in un ambiente del servizio app ILB possono essere esposte in un indirizzo IP privato, è possibile aggiungere facilmente i dispositivi WAF per esporre solo le app che si vuole usare in Internet e proteggere il resto. Questa funzionalità consente di semplificare lo sviluppo di applicazioni multilivello. 

Alcune operazioni non sono attualmente possibili dal servizio multi-tenant, ma sono possibili da un ambiente del servizio app. Ecco alcuni esempi:

* Esporre le app in un indirizzo IP privato.
* Proteggi tutto il traffico in uscita con i controlli di rete che non fanno parte dell'app.
* Ospitare le app in un servizio a tenant singolo. 
* Ridimensionare fino a molte più istanze di quelle possibili nel servizio multi-tenant. 
* Caricare i certificati client della CA privata per l'uso da app con endpoint privati protetti da autorità di certificazione.
* Forza TLS 1,1 tra tutte le app ospitate nel sistema senza alcuna possibilità di disabilitarle a livello di app. 
* Fornire un indirizzo in uscita dedicato per tutte le app nell'ambiente del servizio app che non sono condivise con i clienti. 

![Diagramma che illustra un ambiente del servizio app in una rete virtuale.](media/networking-features/app-service-environment.png)

L'ambiente del servizio app offre la migliore soluzione per l'hosting di app dedicate e isolate, ma comporta alcune problemi di gestione. Prima di usare un ambiente del servizio app operativo, è necessario considerare alcuni aspetti:
 
 * Un ambiente del servizio app viene eseguito all'interno della rete virtuale, ma presenta dipendenze esterne alla rete virtuale. Tali dipendenze devono essere consentite. Per ulteriori informazioni, vedere [considerazioni sulla rete per un ambiente del servizio app][networkinfo].
 * Un ambiente del servizio app non viene ridimensionato immediatamente come il servizio multi-tenant. È necessario anticipare le esigenze di scalabilità anziché ridimensionare in modo reattivo. 
 * Un ambiente del servizio app ha un costo iniziale superiore. Per ottenere il massimo dall'ambiente del servizio app, è consigliabile pianificare l'inserimento di molti carichi di lavoro in un ambiente del servizio app, anziché usarlo per piccoli sforzi.
 * Le app in un ambiente del servizio app non possono limitare selettivamente l'accesso ad alcune app nell'ambiente del servizio app e non in altre.
 * Un ambiente del servizio app si trova in una subnet e tutte le regole di rete si applicano a tutto il traffico da e verso tale ambiente del servizio app. Se si vuole assegnare regole di traffico in ingresso per una sola app, usare le restrizioni di accesso. 

## <a name="combining-features"></a>Combinazione di funzionalità 

Le funzionalità indicate per il servizio multi-tenant possono essere usate insieme per risolvere casi d'uso più complessi. Due dei casi d'uso più comuni sono descritti qui, ma sono solo esempi. Grazie alla conoscenza delle diverse funzionalità, è possibile soddisfare quasi tutte le esigenze di architettura del sistema.

### <a name="place-an-app-into-a-virtual-network"></a>Inserire un'app in una rete virtuale

Si potrebbe chiedere come inserire un'app in una rete virtuale. Se si inserisce l'app in una rete virtuale, gli endpoint in ingresso e in uscita per l'app si trovano all'interno della rete virtuale. Un ambiente del servizio app è il modo migliore per risolvere questo problema. È tuttavia possibile soddisfare la maggior parte delle esigenze all'interno del servizio multi-tenant combinando le funzionalità. È ad esempio possibile ospitare applicazioni solo Intranet con indirizzi in ingresso e in uscita privati per:

* Creazione di un gateway applicazione con indirizzi in ingresso e in uscita privati.
* Sicurezza del traffico in ingresso verso l'app con gli endpoint di servizio. 
* Usando la nuova funzionalità di integrazione VNet, il back-end dell'app si trova nella rete virtuale. 

Questo stile di distribuzione non fornisce un indirizzo dedicato per il traffico in uscita verso Internet o la possibilità di bloccare tutto il traffico in uscita dall'app. Fornirà una gran parte di ciò che si otterrebbe in altro modo con un ambiente del servizio app. 

### <a name="create-multitier-applications"></a>Creare applicazioni multilivello

Un'applicazione multilivello è un'applicazione in cui è possibile accedere alle app back-end dell'API solo dal livello front-end. Esistono due modi per creare un'applicazione multilivello. Per iniziare, usare l'integrazione VNet per connettere l'app Web front-end a una subnet in una rete virtuale. Questa operazione consentirà all'app Web di effettuare chiamate alla rete virtuale. Dopo che l'app front-end è connessa alla rete virtuale, è necessario decidere come bloccare l'accesso all'applicazione per le API. È possibile scegliere:

* Ospitare sia il front-end che l'app per le API nello stesso ambiente del servizio app ILB ed esporre l'app front-end a Internet usando un gateway applicazione.
* Ospitare il front-end nel servizio multi-tenant e il back-end in un ambiente del servizio app ILB.
* Ospitare il front-end e l'app per le API nel servizio multi-tenant.

Se il front-end e l'app per le API sono ospitati per un'applicazione multilivello, è possibile:

- Esporre l'applicazione API usando endpoint privati nella rete virtuale:

  ![Diagramma che illustra l'uso di endpoint privati in un'applicazione a due livelli.](media/networking-features/multi-tier-app-private-endpoint.png)

- Usare gli endpoint di servizio per assicurarsi che il traffico in ingresso verso l'app per le API venga usato solo dalla subnet usata dall'app Web front-end:

  ![Diagramma che illustra l'uso degli endpoint di servizio per proteggere un'app.](media/networking-features/multi-tier-app.png)

Di seguito sono riportate alcune considerazioni che consentono di decidere quale metodo utilizzare:

* Quando si usano gli endpoint di servizio, è sufficiente proteggere il traffico verso l'app per le API nella subnet di integrazione. Ciò consente di proteggere l'app per le API, ma è comunque possibile avere dati exfiltration dall'app front-end ad altre app nel servizio app.
* Quando si usano endpoint privati, sono disponibili due subnet, che aumentano la complessità. Inoltre, l'endpoint privato è una risorsa di livello superiore e aggiunge un sovraccarico di gestione. Il vantaggio dell'uso di endpoint privati è che non si ha la possibilità di exfiltration di dati. 

Entrambi i metodi funzioneranno con più front-end. Su scala ridotta, gli endpoint di servizio sono più facili da usare perché si abilitano semplicemente gli endpoint di servizio per l'app per le API nella subnet di integrazione front-end. Quando si aggiungono altre app front-end, è necessario modificare ogni app per le API per includere gli endpoint di servizio con la subnet di integrazione. Quando si usano gli endpoint privati, c'è maggiore complessità, ma non è necessario apportare modifiche alle app per le API dopo aver impostato un endpoint privato. 

### <a name="line-of-business-applications"></a>Applicazioni line-of-business

Le applicazioni line-of-business (LOB) sono applicazioni interne che in genere non sono esposte per l'accesso da Internet. Queste applicazioni vengono chiamate dall'interno di reti aziendali in cui è possibile controllare rigorosamente l'accesso. Se si usa un ambiente del servizio app ILB, è facile ospitare le applicazioni line-of-business. Se si usa il servizio multi-tenant, è possibile usare endpoint privati o usare endpoint di servizio combinati con un gateway applicazione. Esistono due motivi per usare un gateway applicazione con gli endpoint di servizio invece di usare endpoint privati:
* È necessaria la protezione WAF nelle app LOB.
* Si vuole bilanciare il carico in più istanze delle app LOB.

Se nessuna di queste esigenze viene applicata, è preferibile usare endpoint privati. Con gli endpoint privati disponibili nel servizio app, è possibile esporre le app in indirizzi privati nella rete virtuale. L'endpoint privato inserito nella rete virtuale può essere raggiunto attraverso connessioni VPN e ExpressRoute. 

La configurazione degli endpoint privati esporrà le app in un indirizzo privato, ma sarà necessario configurare DNS per raggiungere tale indirizzo dall'ambiente locale. Per eseguire questa configurazione, è necessario inviare la zona privata di DNS di Azure che contiene gli endpoint privati ai server DNS locali. Le zone private di DNS di Azure non supportano l'invio di zone, ma è possibile supportare l'invio di zone usando un server DNS a tale scopo. Il modello di server d' [invio DNS](https://azure.microsoft.com/resources/templates/301-dns-forwarder/) rende più semplice l'invio della zona privata di DNS di Azure ai server DNS locali.

## <a name="app-service-ports"></a>Porte del servizio app

Se si analizza il servizio app, sono disponibili diverse porte esposte per le connessioni in ingresso. Non è possibile bloccare o controllare l'accesso a queste porte nel servizio multi-tenant. Ecco l'elenco delle porte esposte:

| Uso | Porta o porte |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  Gestione | 454, 455 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Debug remoto in Visual Studio  |  4020, 4022, 4024 |
|  Servizio Distribuzione Web | 8172 |
|  Uso dell'infrastruttura | 7654, 1221 |

<!--Links-->
[appassignedaddress]: ./configure-ssl-certificate.md
[iprestrictions]: ./app-service-ip-restrictions.md
[serviceendpoints]: ./app-service-ip-restrictions.md
[hybridconn]: ./app-service-hybrid-connections.md
[vnetintegrationp2s]: ./web-sites-integrate-with-vnet.md
[vnetintegration]: ./web-sites-integrate-with-vnet.md
[networkinfo]: ./environment/network-info.md
[appgwserviceendpoints]: ./networking/app-gateway-with-service-endpoints.md
[privateendpoints]: ./networking/private-endpoint.md