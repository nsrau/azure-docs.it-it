---
title: Integrare l'app con rete virtuale di Azure
description: Integra le app nel servizio app Azure con le reti virtuali di Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 76139716fe11536faa0ff792185ba1643801c641
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649025"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrare un'app in una rete virtuale di Azure
Questo documento descrive la funzionalità di integrazione della rete virtuale del servizio app Azure e come configurarla con le app nel [servizio app Azure](https://go.microsoft.com/fwlink/?LinkId=529714). Le [reti virtuali di Azure][VNETOverview] (reti virtuali) consentono di inserire molte delle risorse di Azure in una rete instradabile non Internet.  

Il servizio app Azure presenta due varianti. 

1. I sistemi multi-tenant che supportano l'intera gamma dei piani tariffari, ad eccezione di Isolated
2. Il ambiente del servizio app (ambiente del servizio app), che distribuisce nella VNet e supporta le app di piano tariffario isolate

Questo documento descrive la funzionalità di integrazione VNet, che può essere usata nel servizio app multi-tenant. Se l'app è in [ambiente del servizio app][ASEintro], si trova già in una VNet e non richiede l'uso della funzionalità di integrazione VNet per raggiungere le risorse nello stesso VNet. Per informazioni dettagliate su tutte le funzionalità di rete del servizio app, vedere [funzionalità di rete del servizio app](networking-features.md)

L'integrazione di VNet consente all'app Web di accedere alle risorse nella rete virtuale, ma non concede l'accesso privato in ingresso all'app Web dalla VNet. Per accesso privato s'intende la possibilità di rendere l'app accessibile soltanto da una rete privata, ad esempio all'interno di una rete virtuale di Azure. L'integrazione di VNet è solo per la creazione di chiamate in uscita dall'app in VNet. La funzionalità di integrazione VNet si comporta in modo diverso quando viene usata con reti virtuali nella stessa area e con reti virtuali in altre aree. La funzionalità di integrazione VNet presenta due varianti.

1. Integrazione VNet a livello di area: quando ci si connette a Gestione risorse reti virtuali nella stessa area, è necessario disporre di una subnet dedicata nel VNet con cui si esegue l'integrazione. 
2. Integrazione VNet necessaria per il gateway: quando ci si connette a reti virtuali in altre aree o a una VNet classica nella stessa area è necessario un gateway di rete virtuale di cui è stato effettuato il provisioning nel VNet di destinazione.

Funzionalità di integrazione di VNet:

* Richiedi un piano tariffario standard, Premium, PremiumV2 o Elastic Premium 
* supportare TCP e UDP
* usare le app del servizio app e le app per le funzioni

Alcune operazioni non sono supportate da Integrazione rete virtuale:

* montaggio di un'unità
* integrazione di AD 
* NetBios

L'integrazione VNet necessaria per il gateway consente solo l'accesso alle risorse nel VNet di destinazione o nelle reti connesse al VNet di destinazione con peering o VPN. L'integrazione VNet necessaria per il gateway non consente l'accesso alle risorse disponibili in connessioni ExpressRoute o funziona con gli endpoint di servizio. 

Indipendentemente dalla versione usata, l'integrazione di VNet consente all'app Web di accedere alle risorse nella rete virtuale, ma non concede l'accesso privato in ingresso all'app Web dalla rete virtuale. Per accesso privato s'intende la possibilità di rendere l'app accessibile soltanto da una rete privata, ad esempio all'interno di una rete virtuale di Azure. L'integrazione di VNet è solo per la creazione di chiamate in uscita dall'app in VNet. 

## <a name="enable-vnet-integration"></a>Abilitare l'integrazione di VNet 

1. Passare all'interfaccia utente di rete nel portale del servizio app. In integrazione VNet selezionare *"fare clic qui per configurare"* . 

1. Selezionare **Add VNet** (Aggiungi rete virtuale).  

   ![Seleziona integrazione VNet][1]

1. L'elenco a discesa conterrà tutti i Gestione risorse reti virtuali nella sottoscrizione nella stessa area e in basso che è un elenco di tutti i Gestione risorse reti virtuali in tutte le altre aree. Selezionare il VNet con cui si vuole eseguire l'integrazione.

   ![Selezionare il VNet][2]

   * Se il VNet si trova nella stessa area, creare una nuova subnet o selezionare una subnet preesistente vuota. 

   * Per selezionare un VNet in un'altra area, è necessario disporre di un gateway di rete virtuale di cui è stato effettuato il provisioning con la funzionalità da punto a sito.

   * Per l'integrazione con una VNet classica, invece di fare clic sull'elenco a discesa VNet, selezionare **fare clic qui per connettersi a un VNet classico**. Selezionare la VNet classica desiderata. Il VNet di destinazione deve avere già un gateway di rete virtuale di cui è stato effettuato il provisioning con da punto a sito abilitato.

    ![Selezione VNet classica][3]
    
Durante l'integrazione, l'app viene riavviata.  Al termine dell'integrazione, verranno visualizzati i dettagli sul VNet con cui si è integrato. 

Quando l'app è integrata con la VNet, userà lo stesso server DNS con cui è configurata la VNet, a meno che non sia Zone private di DNS di Azure. Attualmente non è possibile usare l'integrazione di VNet con Zone private di DNS di Azure.

## <a name="regional-vnet-integration"></a>Integrazione VNet a livello di area

L'uso dell'integrazione VNet a livello di area consente all'app di accedere a:

* risorse in VNet nella stessa area integrata con 
* risorse in reti virtuali con peering al VNet che si trovano nella stessa area
* Servizi protetti endpoint di servizio
* risorse tra connessioni ExpressRoute
* risorse nella VNet a cui si è connessi
* risorse tra connessioni con peering, incluse le connessioni ExpressRoute
* Endpoint privati 

Quando si usa l'integrazione di VNet con reti virtuali nella stessa area, è possibile usare le funzionalità di rete di Azure seguenti:

* Gruppi di sicurezza di rete (gruppi): è possibile bloccare il traffico in uscita con un gruppo di sicurezza di rete che si trova nella subnet di integrazione. Le regole in ingresso non si applicano perché non è possibile usare l'integrazione VNet per fornire l'accesso in ingresso all'app Web.
* Tabelle di route (UDR): è possibile inserire una tabella di route nella subnet di integrazione per inviare il traffico in uscita laddove si vuole. 

Per impostazione predefinita, l'app indirizza il traffico RFC1918 solo nella VNet. Se si vuole instradare tutto il traffico in uscita nella VNet, applicare l'impostazione dell'app WEBSITE_VNET_ROUTE_ALL all'app. Per configurare l'impostazione dell'app:

1. Passare all'interfaccia utente di configurazione nel portale dell'app. Selezionare l' **impostazione nuova applicazione**
1. Digitare **WEBSITE_VNET_ROUTE_ALL** nel campo nome e **1** nel campo valore

   ![Specificare l'impostazione dell'applicazione][4]

1. Selezionare **OK**.
1. Selezionare **Salva**

Se si instrada tutto il traffico in uscita nel VNet, sarà soggetto a gruppi e UdR applicati alla subnet di integrazione. Quando si esegue il routing di tutto il traffico in uscita nella VNet, gli indirizzi in uscita saranno ancora gli indirizzi in uscita elencati nelle proprietà dell'app, a meno che non si forniscano route per inviare il traffico altrove. 

Esistono alcune limitazioni all'uso dell'integrazione di VNet con reti virtuali nella stessa area:

* Non è possibile raggiungere risorse tra connessioni di peering globali
* La funzionalità è disponibile solo dalle più recenti unità di scala del servizio app che supportano i piani di servizio app PremiumV2.
* La subnet di integrazione può essere usata solo da un solo piano di servizio app
* Non è possibile usare la funzionalità da app del piano isolato che si trovano in un ambiente del servizio app
* Per la funzionalità è necessaria una subnet inutilizzata/27 con indirizzi 32 o più grandi in una Gestione risorse VNet
* L'app e la rete virtuale devono essere nella stessa area
* Non è possibile eliminare una rete virtuale con un'app integrata. Rimuovere l'integrazione prima di eliminare il VNet. 
* È possibile eseguire l'integrazione solo con reti virtuali nella stessa sottoscrizione dell'app Web
* È possibile avere una sola integrazione VNet a livello di area per ogni piano di servizio app. Più app nello stesso piano di servizio app possono usare lo stesso VNet. 
* Non è possibile modificare la sottoscrizione di un'app o di un piano di servizio app mentre è presente un'app che usa l'integrazione VNet a livello di area

Viene usato un indirizzo per ogni istanza del piano di servizio app. Se si ridimensiona l'app a cinque istanze, vengono usati cinque indirizzi. Poiché non è possibile modificare le dimensioni della subnet dopo l'assegnazione, è necessario usare una subnet sufficientemente grande da contenere la scala che l'app può raggiungere. Le dimensioni consigliate sono le dimensioni consigliate per/26 con indirizzi 64. Un piano di servizio App Premium a/26 con 64 includerà 30 istanze. Quando si ridimensiona un piano di servizio app verso l'alto o verso il basso, sono necessari due volte il numero di indirizzi per un breve periodo di tempo. 

Se si vuole che le app in un altro piano di servizio app raggiungano una VNet connessa già da app in un altro piano di servizio app, è necessario selezionare una subnet diversa da quella usata dall'integrazione VNet preesistente.  

La funzionalità è in anteprima per Linux. Il formato Linux della funzionalità supporta solo l'esecuzione di chiamate a indirizzi RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web-app-for-containers"></a>App Web per contenitori

Se si usa il servizio app in Linux con le immagini predefinite, l'integrazione VNet a livello di area funziona senza modifiche aggiuntive. Se si usa app Web per contenitori, è necessario modificare l'immagine Docker per usare l'integrazione VNet. Nell'immagine Docker usare la variabile di ambiente PORT come porta di ascolto del server Web principale, anziché usare un numero di porta hardcoded. La variabile di ambiente PORT viene impostata automaticamente dalla piattaforma del servizio app al momento dell'avvio del contenitore. Se si usa SSH, il daemon SSH deve essere configurato in modo da restare in ascolto sul numero di porta specificato dalla variabile di ambiente SSH_PORT quando si usa l'integrazione VNet a livello di area.  Non è disponibile alcun supporto per l'integrazione VNet necessaria per il gateway in Linux. 

### <a name="service-endpoints"></a>Endpoint servizio

L'integrazione VNet a livello di area consente di usare gli endpoint di servizio.  Per usare gli endpoint di servizio con l'app, usare l'integrazione VNet a livello di area per connettersi a un VNet selezionato e quindi configurare gli endpoint di servizio nella subnet usata per l'integrazione. 

### <a name="network-security-groups"></a>Gruppi di sicurezza di rete

I gruppi di sicurezza di rete consentono di bloccare il traffico in ingresso e in uscita verso le risorse in una VNet. Un'app Web che usa l'integrazione VNet a livello di area può usare il [gruppo di sicurezza di rete][VNETnsg] per bloccare il traffico in uscita verso le risorse in VNet o Internet. Per bloccare il traffico verso indirizzi pubblici, è necessario che l'impostazione dell'applicazione WEBSITE_VNET_ROUTE_ALL impostata su 1. Le regole in ingresso in una NSG non si applicano all'app perché l'integrazione con VNet influiscono solo sul traffico in uscita dall'app. Per controllare il traffico in ingresso verso l'app Web, usare la funzionalità restrizioni di accesso. Un NSG applicato alla subnet di integrazione sarà attivo indipendentemente dalle route applicate alla subnet di integrazione. Se WEBSITE_VNET_ROUTE_ALL è stato impostato su 1 e non sono presenti route che influiscono sul traffico degli indirizzi pubblici sulla subnet di integrazione, tutto il traffico in uscita sarà ancora soggetto a gruppi assegnati alla subnet di integrazione. Se WEBSITE_VNET_ROUTE_ALL non è stato impostato, gruppi verrà applicato solo al traffico RFC1918.

### <a name="routes"></a>Route

Le tabelle di route consentono di instradare il traffico in uscita dall'app a qualsiasi posizione. Per impostazione predefinita, le tabelle di route avranno effetto solo sul traffico di destinazione RFC1918.  Se si imposta WEBSITE_VNET_ROUTE_ALL su 1, verranno interessate tutte le chiamate in uscita. Le route impostate nella subnet di integrazione non avranno effetto sulle risposte alle richieste di app in ingresso. Le destinazioni comuni possono includere dispositivi firewall o gateway. Se si vuole instradare tutto il traffico in uscita in locale, è possibile usare una tabella di route per inviare tutto il traffico in uscita al gateway ExpressRoute. Se si esegue il routing del traffico a un gateway, assicurarsi di impostare le route nella rete esterna per restituire le risposte.

Le route Border Gateway Protocol (BGP) influiscono anche sul traffico dell'app. Se si hanno Route BGP da un gateway ExpressRoute, il traffico in uscita dell'app sarà interessato. Per impostazione predefinita, le route BGP avranno effetto solo sul traffico di destinazione RFC1918. Se WEBSITE_VNET_ROUTE_ALL è impostato su 1, tutto il traffico in uscita può essere influenzato dalle route BGP. 

### <a name="how-regional-vnet-integration-works"></a>Funzionamento dell'integrazione VNet a livello di area

Le app nel servizio app sono ospitate in ruoli di lavoro. I piani tariffari di base e superiore sono piani di hosting dedicati in cui non sono presenti altri carichi di lavoro in esecuzione negli stessi thread di lavoro. L'integrazione VNet a livello di area funziona montando interfacce virtuali con indirizzi nella subnet delegata. Poiché l'indirizzo from si trova nel VNet, può accedere alla maggior parte delle operazioni in o tramite il VNet proprio come una VM nel VNet. L'implementazione della rete è diversa rispetto all'esecuzione di una macchina virtuale nella VNet ed è per questo motivo che alcune funzionalità di rete non sono ancora disponibili durante l'uso di questa funzionalità.

![Funzionamento dell'integrazione VNet a livello di area][5]

Quando è abilitata l'integrazione VNet a livello di area, l'app effettuerà comunque chiamate in uscita a Internet tramite gli stessi canali del normale. Gli indirizzi in uscita elencati nel portale delle proprietà dell'app sono ancora gli indirizzi usati dall'app. Quali modifiche sono state apportate all'app, le chiamate a servizi protetti per endpoint di servizio o indirizzi RFC 1918 passano alla VNet. Se WEBSITE_VNET_ROUTE_ALL è impostato su 1, tutto il traffico in uscita può essere inviato a VNet. 

La funzionalità supporta solo un'interfaccia virtuale per ogni thread di lavoro.  Un'interfaccia virtuale per ogni thread di lavoro indica un'integrazione VNet a livello di area per ogni piano di servizio app. Tutte le app nello stesso piano di servizio app possono usare la stessa integrazione di VNet, ma se è necessario che un'app possa connettersi a un VNet aggiuntivo, sarà necessario creare un altro piano di servizio app. L'interfaccia virtuale utilizzata non è una risorsa a cui i clienti hanno accesso diretto.

A causa della natura del funzionamento di questa tecnologia, il traffico usato con l'integrazione di VNet non viene visualizzato nei log di flusso Network Watcher o NSG.  

## <a name="gateway-required-vnet-integration"></a>Integrazione VNet necessaria per il gateway

L'integrazione VNet necessaria per il gateway supporta la connessione a una VNet in un'altra area o a una VNet classica. Integrazione VNet necessaria per il gateway: 

* Consente a un'app di connettersi solo a 1 VNet alla volta
* Consente l'integrazione di un massimo di cinque reti virtuali all'interno di un piano di servizio app 
* Consente l'uso della stessa VNet da parte di più app in un piano di servizio app senza influisca sul numero totale che può essere usato da un piano di servizio app.  Se sono presenti sei app che usano lo stesso VNet nello stesso piano di servizio app, il numero è pari a 1 VNet in uso. 
* Supporta uno SLA del 99,9% a causa del contratto di contratto sul gateway
* Consente alle app di usare il DNS con cui è configurato il VNet
* Richiede un gateway basato su route di rete virtuale configurato con una VPN da punto a sito SSTP prima che possa essere connessa all'app. 

Non è possibile usare l'integrazione VNet necessaria per il gateway:

* Con le app Linux
* Con una VNet connessa a ExpressRoute 
* Per accedere alle risorse protette degli endpoint di servizio
* Con un gateway di coesistenza che supporta le VPN ExpressRoute e da punto a sito/sito a sito

### <a name="set-up-a-gateway-in-your-vnet"></a>Configurare un gateway nella rete virtuale ###

Per creare un gateway:

1. [Creare una subnet del gateway][creategatewaysubnet] nella VNet.  

1. [Creare il gateway VPN][creategateway]. Selezionare un tipo di VPN basato su route.

1. [Impostare gli indirizzi da punto a sito][setp2saddresses]. Se il gateway non si trova nello SKU Basic, IKEV2 deve essere disabilitato nella configurazione da punto a sito ed è necessario selezionare SSTP. Lo spazio di indirizzi da punto a sito deve essere nei blocchi di indirizzi RFC 1918, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Se si sta semplicemente creando il gateway per l'uso con l'integrazione VNet del servizio app, non è necessario caricare un certificato. La creazione del gateway può richiedere 30 minuti. Non sarà possibile integrare l'app con la rete virtuale fino al completamento del provisioning del gateway. 

### <a name="how-gateway-required-vnet-integration-works"></a>Funzionamento dell'integrazione VNet necessaria per il gateway

Il gateway richiede l'integrazione VNet basata sulla tecnologia VPN da punto a sito. Le VPN da punto a sito limitano l'accesso alla rete solo alla macchina virtuale che ospita l'app. Le app possono esclusivamente inviare traffico in Internet tramite Connessioni ibride o Integrazione rete virtuale. Quando l'app è configurata con il portale per l'uso dell'integrazione VNet necessaria per il gateway, per conto di creare e assegnare certificati sul gateway e sul lato applicazione viene gestita una negoziazione complessa. Il risultato finale è che i ruoli di lavoro usati per ospitare le app sono in grado di connettersi direttamente al gateway di rete virtuale nel VNet selezionato. 

![Funzionamento dell'integrazione VNet necessaria per il gateway][6]

### <a name="accessing-on-premises-resources"></a>Accesso alle risorse locali

Le app possono accedere alle risorse locali grazie all'integrazione con le reti virtuali che dispongono di connessioni da sito a sito. Se si usa l'integrazione VNet necessaria per il gateway, è necessario aggiornare le route del gateway VPN locale con i blocchi di indirizzi da punto a sito. Quando la connessione VPN da sito a sito viene configurata per la prima volta, gli script usati per la configurazione devono configurare le route in modo appropriato. Se gli indirizzi da punto a sito vengono aggiunti dopo aver creato la VPN da sito a sito, è necessario aggiornare le route manualmente. Le informazioni dettagliate su come eseguire questa operazione variano in base al gateway e non sono descritte in questo documento. Non è possibile configurare BGP con una connessione VPN da sito a sito.

Non è necessaria alcuna configurazione aggiuntiva per la funzionalità di integrazione VNet a livello di area tramite VNet e in locale. È sufficiente connettere la VNet al sito locale usando ExpressRoute o una VPN da sito a sito. 

> [!NOTE]
> La funzionalità di integrazione VNet necessaria per il gateway non integra un'app con una VNet che dispone di un gateway ExpressRoute. Anche se il gateway ExpressRoute è configurato in [modalità di coesistenza][VPNERCoex] , l'integrazione di VNet non funziona. Se è necessario accedere alle risorse tramite una connessione ExpressRoute, è possibile usare la funzionalità di integrazione VNet a livello di area o un [ambiente del servizio app][ASE], che viene eseguito nel VNet. 
> 
> 

### <a name="peering"></a>Peering

Se si usa il peering con l'integrazione VNet a livello di area, non è necessario eseguire alcuna configurazione aggiuntiva. 

Se si usa il gateway necessario per l'integrazione di VNet con il peering, è necessario configurare alcuni elementi aggiuntivi. Per configurare il peering per interagire con l'app:

1. Aggiungere una connessione di peering nella rete virtuale a cui si connette l'app. Quando si aggiunge la connessione di peering, abilitare **Consenti accesso alla rete virtuale** e selezionare **Consenti traffico inoltrato** e **Consenti transito gateway**.
1. Aggiungere una connessione di peering nella rete virtuale sottoposta a peering alla rete virtuale a cui si è connessi. Quando si aggiunge la connessione di peering nella rete virtuale di destinazione, abilitare **Consenti accesso alla rete virtuale** e selezionare **Consenti traffico inoltrato** e **Usa gateway remoti**.
1. Passare a Piano di servizio app > Rete > interfaccia utente di Integrazione rete virtuale nel portale.  Selezionare la rete virtuale a cui si connette l'app. Nella sezione Routing aggiungere l'intervallo di indirizzi della rete virtuale sottoposta a peering alla rete virtuale a cui è connessa l'app.  

## <a name="managing-vnet-integration"></a>Gestione dell'integrazione di VNet 

La connessione e la disconnessione con un VNet sono a livello di app. Le operazioni che possono influenzare Integrazione rete virtuale tra più app sono a livello del piano di Servizio app. Dall'app > Networking > portale di integrazione VNet, è possibile ottenere informazioni dettagliate sul VNet. È possibile visualizzare informazioni simili a livello di ASP nella rete ASP > > portale di integrazione VNet.

L'unica operazione possibile nella vista app di Integrazione rete virtuale è la disconnessione dell'app dalla rete virtuale a cui è attualmente connessa. Per disconnettere l'app da una rete virtuale, selezionare **Disconnetti**. L'app verrà riavviata quando ci si disconnette da una rete virtuale. La disconnessione non modifica la rete virtuale. La subnet o il gateway non viene rimosso. Se quindi si vuole eliminare la VNet, è necessario prima disconnettere l'app dalla VNet ed eliminare le risorse in esso presenti, ad esempio i gateway. 

L'interfaccia utente di integrazione ASP VNet mostrerà tutte le integrazioni di VNet usate dalle app in ASP. Per visualizzare i dettagli su ogni rete virtuale, fare clic sulla rete virtuale in questione. Esistono due azioni che è possibile eseguire qui per l'integrazione VNet necessaria per il gateway.

* **Sincronizza rete**. L'operazione di sincronizzazione della rete è solo per la funzionalità di integrazione VNet dipendente dal gateway. L'esecuzione di un'operazione di sincronizzazione della rete garantisce che i certificati e le informazioni di rete siano sincronizzati. Se si aggiunge o si modifica il DNS del VNet, è necessario eseguire un'operazione di **sincronizzazione della rete** . Questa operazione riavvierà qualsiasi app con questa rete virtuale.
* **Aggiungi route**. L'aggiunta di route indirizzerà il traffico in uscita nella rete virtuale. 

Il **gateway richiede il routing dell'integrazione VNet** Le route definite in VNet vengono usate per indirizzare il traffico nella VNet dall'app. Se è necessario inviare altro traffico in uscita nella rete virtuale, è possibile aggiungere qui i blocchi di indirizzi. Questa funzionalità funziona solo con l'integrazione VNet obbligatoria del gateway. Le tabelle di route non influiscono sul traffico dell'app quando si usa l'integrazione VNet necessaria del gateway nel modo in cui vengono eseguite con l'integrazione VNet a livello di area.

**Certificati di integrazione VNet necessari** per il gateway Quando il gateway richiede l'integrazione di VNet abilitata, è necessario lo scambio di certificati per garantire la sicurezza della connessione. Con i certificati si ottengono la configurazione DNS, le route e altre informazioni simili che descrivono la rete.
Se vengono modificati i certificati o le informazioni di rete, è necessario fare clic su "Sincronizza rete". Quando si fa clic su "Sincronizza rete", si verifica una breve interruzione della connettività tra l'app e la rete virtuale. L'app non viene riavviata, ma la perdita di connettività potrebbe causare il funzionamento non corretto del sito. 

## <a name="pricing-details"></a>Dettagli prezzi
La funzionalità di integrazione VNet a livello di area non prevede costi aggiuntivi per l'utilizzo oltre l'importo del piano tariffario ASP.

Esistono tre addebiti correlati all'uso della funzionalità di integrazione VNet necessaria per il gateway:

* Costi del piano tariffario ASP: le app devono essere in un piano di servizio app standard, Premium o PremiumV2. Per altri dettagli sui costi, vedere i prezzi del [servizio app][ASPricing]. 
* Costi di trasferimento dei dati: è previsto un addebito per l'uscita dei dati, anche se il VNet è nello stesso data center. Questi addebiti sono descritti in [Dettagli prezzi trasferimento dati][DataPricing]. 
* Costi del gateway VPN: un costo per il gateway VNet richiesto per la VPN da punto a sito. I dettagli sono disponibili nella pagina dei [prezzi del gateway VPN][VNETPricing] .

## <a name="troubleshooting"></a>risoluzione dei problemi
La funzionalità è semplice da configurare, ma possono comunque verificarsi problemi durante l'uso. In caso di problemi di accesso all'endpoint desiderato, sono disponibili varie utilità che permettono di testare la connettività dalla console dell'app. Le console disponibili sono due: la console Kudu e la console nel portale di Azure. Per accedere alla console Kudu dalla propria app, selezionare Strumenti -> Kudu. È anche possibile accedere alla console Kudo in [SiteName]. SCM. azurewebsites. NET. Una volta caricato il sito Web, passare alla scheda Debug Console. Per accedere alla console ospitata portale di Azure quindi dall'app, passare a strumenti-> Console. 

#### <a name="tools"></a>Strumenti
Gli strumenti **ping**, **nslookup**e **tracert** non funzioneranno tramite la console a causa di vincoli di sicurezza. Per riempire il void, sono stati aggiunti due strumenti distinti. Per testare la funzionalità del DNS è stato aggiunto lo strumento nameresolver.exe. La sintassi è:

    nameresolver.exe hostname [optional: DNS Server]

È possibile usare **nameresolver** per controllare i nomi host da cui dipende l'app. In questo modo è possibile verificare se la configurazione del server DNS è corretta e se si ha accesso al server DNS. È possibile visualizzare il server DNS che l'app userà nella console esaminando le variabili di ambiente WEBSITE_DNS_SERVER e WEBSITE_DNS_ALT_SERVER.

Il secondo strumento permette di verificare la connettività TCP a una combinazione di host e porta. Questo strumento viene chiamato **tcpping** la cui sintassi è:

    tcpping.exe hostname [optional: port]

L'utilità **tcpping** indica se è possibile raggiungere una porta e un host specifici. Essa visualizza il completamento solo se: è presente un'applicazione in ascolto presso la combinazione di host e porta ed è presente un accesso di rete dall'app all'host e alla porta specificati.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Accesso di debug a risorse ospitate su una rete virtuale
L'app potrebbe non riuscire a raggiungere un host e una porta specifici per una serie di motivi. Nella maggior parte dei casi il motivo è uno dei seguenti:

* **L'ostacolo è rappresentato da un firewall**. Se è presente un firewall, si verifica il timeout TCP, che in questo caso è di 21 secondi. Usare lo strumento **tcpping** per testare la connettività. Il timeout TCP può essere dovuto anche ad altri motivi, ma è consigliabile iniziare dal firewall. 
* **Il DNS non è accessibile**. Il timeout DNS è di tre secondi per ogni server DNS. Se si dispone di due server DNS, il timeout è di 6 secondi. Usare nameresolver per verificare il funzionamento del DNS. Si noti che non è possibile usare nslookup, perché non usa il DNS con cui è configurata la rete virtuale. Se non è accessibile, è possibile che si disponga di un firewall o di un NSG che blocca l'accesso al DNS o che potrebbe essere inattivo.

Se tali elementi non rispondono ai problemi, cercare prima di tutto, ad esempio: 

**Integrazione VNet a livello di area**
* la destinazione è un indirizzo non RFC1918 e non si dispone di WEBSITE_VNET_ROUTE_ALL impostato su 1
* è presente un blocco NSG in uscita dalla subnet di integrazione
* Se si passa attraverso ExpressRoute o una VPN, il gateway locale è configurato per instradare il traffico al backup in Azure? Se è possibile raggiungere gli endpoint in VNet, ma non in locale, verificare le route.
* si dispone di autorizzazioni sufficienti per impostare la delega sulla subnet di integrazione? Durante la configurazione dell'integrazione VNet a livello di area, la subnet di integrazione verrà delegata a Microsoft. Web. L'interfaccia utente di integrazione di VNet delegherà automaticamente la subnet a Microsoft. Web. Se l'account non dispone di autorizzazioni di rete sufficienti per impostare la delega, sarà necessario un utente che può impostare gli attributi nella subnet di integrazione per delegare la subnet. Per delegare manualmente la subnet di integrazione, passare all'interfaccia utente della subnet della rete virtuale di Azure e impostare la delega per Microsoft. Web. 

**Integrazione VNet necessaria per il gateway**
* l'intervallo di indirizzi da punto a sito è compreso negli intervalli RFC 1918 (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Il gateway viene visualizzato come attivo nel portale? Se il gateway è inattivo, è necessario riattivarlo.
* I certificati vengono visualizzati come sincronizzati o si ritiene che la configurazione di rete sia stata modificata?  Se i certificati non sono sincronizzati o si ritiene che sia stata apportata una modifica alla configurazione di VNet che non è stata sincronizzata con gli ASP, fare clic su "Sincronizza rete".
* Se si passa attraverso una VPN, è il gateway locale configurato per instradare il traffico al backup in Azure? Se è possibile raggiungere gli endpoint in VNet, ma non in locale, verificare le route.
* si sta provando a usare un gateway di coesistenza che supporta sia da punto a sito che da ExpressRoute? I gateway di coesistenza non sono supportati con l'integrazione VNet 

Il debug dei problemi di rete è una sfida perché non è possibile vedere che cosa blocca l'accesso a una combinazione host: porta specifica. Tra le cause possibili:

* È presente un firewall sull'host che impedisce l'accesso alla porta dell'applicazione dall'intervallo IP da punto a sito. Il passaggio tra subnet spesso richiede l'accesso pubblico.
* L'host di destinazione è inattivo.
* L'applicazione è inattiva.
* L'IP o il nome host è errato.
* L'applicazione è in ascolto su una porta diversa da quella prevista. È possibile associare l'ID di processo alla porta di ascolto mediante "netstat -aon" nell'host dell'endpoint. 
* I gruppi di sicurezza di rete sono configurati in modo da impedire l'accesso all'host e alla porta dell'applicazione dall'intervallo IP da punto a sito.

Tenere presente che non si conosce l'indirizzo che l'App utilizzerà effettivamente. Potrebbe trattarsi di qualsiasi indirizzo nell'intervallo di indirizzi da punto a sito o subnet di integrazione, quindi è necessario consentire l'accesso dall'intero intervallo di indirizzi. 

Di seguito è riportata la procedura di debug aggiuntiva:

* connettersi a un'altra macchina virtuale nella rete virtuale e provare a raggiungere l'host:porta della risorsa da quel punto. Per verificare l'accesso al protocollo TCP usare il comando di PowerShell **test-netconnection**. La sintassi è:

      test-netconnection hostname [optional: -Port]

* visualizzare un'applicazione in una macchina virtuale e testare l'accesso all'host e alla porta dalla console dall'app usando **tcpping**

#### <a name="on-premises-resources"></a>Risorse locali ####

Se l'app non riesce a raggiungere una risorsa locale, verificare se è possibile raggiungere la risorsa dalla propria rete virtuale. Usare il comando di PowerShell **test-netconnection** per eseguire controllare l'accesso al protocollo TCP. Se la macchina virtuale non riesce a raggiungere la risorsa locale, è possibile che la connessione VPN o ExpressRoute non sia configurata correttamente.

Se la macchina virtuale ospitata nella rete virtuale può raggiungere il sistema locale ma l'app non ci riesce, la causa è probabilmente una delle seguenti:

* le route non sono configurate con la subnet o gli intervalli di indirizzi del sito nel gateway locale
* i gruppi di sicurezza della rete bloccano l'accesso all'intervallo IP da punto a sito
* i firewall locali bloccano il traffico proveniente dall'intervallo IP da punto a sito
* si sta tentando di raggiungere un indirizzo non RFC 1918 usando la funzionalità di integrazione VNet a livello di area


## <a name="automation"></a>Automazione

È disponibile il supporto dell'interfaccia della riga di comando per l'integrazione VNet regionale. Per accedere ai comandi seguenti, [installare l'interfaccia][installCLI]della riga di comando di Azure. 

        az webapp vnet-integration --help

        Group
            az webapp vnet-integration : Methods that list, add, and remove virtual network integrations
            from a webapp.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            add    : Add a regional virtual network integration to a webapp.
            list   : List the virtual network integrations on a webapp.
            remove : Remove a regional virtual network integration from webapp.

        az appservice vnet-integration --help

        Group
            az appservice vnet-integration : A method that lists the virtual network integrations used in an
            appservice plan.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            list : List the virtual network integrations used in an appservice plan.

Per l'integrazione VNet necessaria per il gateway, è possibile integrare il servizio app con una rete virtuale di Azure usando PowerShell. Per uno script pronto per l'esecuzione, vedere [Connect an app in Azure App Service to an Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3) (Connettere un'app del Servizio app di Azure a una rete virtuale di Azure).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-appsetting.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
