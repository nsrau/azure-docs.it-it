---
title: Integrare un'app in una rete virtuale di Azure - Servizio app di Azure
description: Illustra come connettere un'app del servizio app di Azure a una rete virtuale di Azure nuova o esistente
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/21/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a96c02d1d7d2fae43e0a5915e9233bde842ce621
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066660"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrare un'app in una rete virtuale di Azure
Questo documento descrive la funzionalità di integrazione della rete virtuale del servizio app Azure e come configurarla con le app nel [servizio app Azure](https://go.microsoft.com/fwlink/?LinkId=529714). [Reti virtuali di Azure][VNETOverview] (Reti virtuali) consente di inserire molte delle risorse di Azure in una rete instradabile non Internet.  

Il servizio app Azure presenta due varianti. 

1. I sistemi multi-tenant che supportano l'intera gamma dei piani tariffari, ad eccezione di Isolated
2. Il ambiente del servizio app (ambiente del servizio app), che distribuisce nella VNet e supporta le app di piano tariffario isolate

Questo documento illustra le due funzionalità di integrazione VNet, che è destinata all'uso nel servizio app multi-tenant. Se l'app è in [ambiente del servizio app][ASEintro], si trova già in una VNet e non richiede l'uso della funzionalità di integrazione VNet per raggiungere le risorse nello stesso VNet. Per informazioni dettagliate su tutte le funzionalità di rete del servizio app, vedere [funzionalità di rete del servizio app](networking-features.md)

Esistono due moduli per la funzionalità di integrazione di VNet

1. Una versione consente l'integrazione con reti virtuali nella stessa area. Questo modulo della funzionalità richiede una subnet in un VNet nella stessa area. Questa funzionalità è ancora in anteprima, ma è supportata per i carichi di lavoro di produzione delle app Windows con alcune avvertenze indicate di seguito.
2. L'altra versione consente l'integrazione con reti virtuali in altre aree o con reti virtuali classiche. Questa versione della funzionalità richiede la distribuzione di un gateway di rete virtuale nella VNet. Si tratta della funzionalità basata su VPN da punto a sito ed è supportata solo con le app di Windows.

Un'app può usare solo un modulo della funzionalità di integrazione VNet alla volta. La domanda è la funzionalità da usare. È possibile utilizzare per molti aspetti. Tuttavia, i differenziatori chiari sono:

| Problema  | Soluzione | 
|----------|----------|
| Si desidera raggiungere un indirizzo RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) nella stessa area | Integrazione VNet a livello di area |
| Si vuole raggiungere le risorse in una VNet classica o in una VNet in un'altra area | Integrazione VNet necessaria per il gateway |
| Si vuole raggiungere gli endpoint RFC 1918 tra ExpressRoute | Integrazione VNet a livello di area |
| Desidera raggiungere risorse tra gli endpoint del servizio | Integrazione VNet a livello di area |

Nessuna delle due funzionalità consentirà di raggiungere indirizzi non RFC 1918 tra ExpressRoute. A tale scopo, è necessario usare un ambiente del servizio app per il momento.

L'uso dell'integrazione VNet a livello di area non connette il VNet a locale o configura gli endpoint di servizio. Si tratta di una configurazione di rete separata. L'integrazione VNet regionale consente semplicemente all'app di effettuare chiamate tra i tipi di connessione.

Indipendentemente dalla versione usata, l'integrazione di VNet consente all'app Web di accedere alle risorse nella rete virtuale, ma non concede l'accesso privato in ingresso all'app Web dalla rete virtuale. Per accesso privato s'intende la possibilità di rendere l'app accessibile soltanto da una rete privata, ad esempio all'interno di una rete virtuale di Azure. L'integrazione di VNet è solo per la creazione di chiamate in uscita dall'app in VNet. 

La funzionalità Integrazione rete virtuale:

* richiede un piano tariffario Standard, Premium o PremiumV2 
* supporta TCP e UDP
* funziona con le app del servizio app e con le app per le funzioni

Alcune operazioni non sono supportate da Integrazione rete virtuale:

* montaggio di un'unità
* integrazione di AD 
* NetBios

## <a name="regional-vnet-integration"></a>Integrazione VNet a livello di area 

Quando si usa l'integrazione di VNet con reti virtuali nella stessa area dell'app, è necessario usare una subnet delegata con almeno 32 indirizzi. Non è possibile usare la subnet per altri scopi. Le chiamate in uscita effettuate dall'app verranno effettuate dagli indirizzi nella subnet delegata. Quando si usa questa versione dell'integrazione VNet, le chiamate vengono effettuate dagli indirizzi in VNet. L'uso di indirizzi nella VNet consente all'app di:

* Effettuare chiamate a servizi protetti dell'endpoint di servizio
* Accedere alle risorse tra connessioni ExpressRoute
* Accedere alle risorse nella VNet a cui si è connessi
* Accedere alle risorse tra connessioni con peering, incluse le connessioni ExpressRoute

Questa funzionalità è in anteprima ma è supportata per i carichi di lavoro di produzione delle app Windows con le limitazioni seguenti:

* È possibile raggiungere solo indirizzi compresi nell'intervallo RFC 1918. Si tratta di indirizzi nei blocchi di indirizzi 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16.
* Non è possibile raggiungere risorse tra connessioni di peering globali
* Non è possibile impostare le route per il traffico proveniente dall'app in VNet
* La funzionalità è disponibile solo dalle più recenti unità di scala del servizio app che supportano i piani di servizio app PremiumV2.
* La subnet di integrazione può essere usata solo da un solo piano di servizio app
* Non è possibile usare la funzionalità da app del piano isolato che si trovano in un ambiente del servizio app
* Per la funzionalità è necessaria una subnet inutilizzata/27 con indirizzi 32 o più grandi nel Gestione risorse VNet
* L'app e la rete virtuale devono essere nella stessa area
* Non è possibile eliminare una rete virtuale con un'app integrata. È necessario rimuovere prima l'integrazione 
* È possibile avere una sola integrazione VNet a livello di area per ogni piano di servizio app. Più app nello stesso piano di servizio app possono usare lo stesso VNet. 
* Non è possibile modificare la sottoscrizione di un'app o di un piano di servizio app mentre è presente un'app che usa l'integrazione VNet a livello di area

Viene usato un indirizzo per ogni istanza del piano di servizio app. Se l'app è stata ridimensionata a 5 istanze, verranno usati 5 indirizzi. Poiché non è possibile modificare le dimensioni della subnet dopo l'assegnazione, è necessario usare una subnet sufficientemente grande da contenere la scala che l'app può raggiungere. Le dimensioni consigliate sono le dimensioni consigliate per/26 con indirizzi 64. Se non si modificano le dimensioni del piano di servizio app, a/27 con indirizzi 32 verranno riportate le istanze del piano di servizio App Premium 20. Quando si ridimensiona un piano di servizio app verso l'alto o verso il basso, sono necessari due volte il numero di indirizzi per un breve periodo di tempo. 

Se si vuole che le app in un altro piano di servizio app raggiungano una VNet connessa già da app in un altro piano di servizio app, è necessario selezionare una subnet diversa da quella usata dall'integrazione VNet preesistente.  

La funzionalità è in anteprima anche per Linux. Per usare la funzionalità di integrazione VNet con un Gestione risorse VNet nella stessa area:

1. Passare all'interfaccia utente di Rete nel portale. Se l'app è in grado di usare la nuova funzionalità, verrà visualizzata un'opzione per aggiungere VNet (anteprima).  

   ![Seleziona integrazione VNet][6]

1. Selezionare **Aggiungi rete virtuale (anteprima)** .  

1. Selezionare la rete virtuale di Resource Manager da integrare e creare una nuova subnet o sceglierne una vuota esistente. Il completamento dell'integrazione richiede meno di un minuto. Durante l'integrazione, l'app viene riavviata.  Al termine dell'integrazione verranno visualizzati i dettagli della rete virtuale da integrare e un banner in alto indicante che la funzionalità è in anteprima.

   ![Selezionare la rete virtuale e la subnet][7]

Dopo che l'app è stata integrata con la VNet, userà lo stesso server DNS con cui è configurata la VNet. 

L'integrazione VNet a livello di area richiede la delega della subnet di integrazione a Microsoft. Web.  L'interfaccia utente di integrazione di VNet delegherà automaticamente la subnet a Microsoft. Web. Se l'account non dispone di autorizzazioni di rete sufficienti per impostare questa impostazione, sarà necessario disporre di un utente che può impostare gli attributi nella subnet di integrazione per delegare la subnet. Per delegare manualmente la subnet di integrazione, passare all'interfaccia utente della subnet della rete virtuale di Azure e impostare la delega per Microsoft. Web.

Per disconnettere l'app dalla rete virtuale, selezionare **Disconnetti**. L'app Web verrà riavviata. 


#### <a name="web-app-for-containers"></a>App Web per contenitori

Se si usa il servizio app in Linux con le immagini predefinite, la funzionalità di integrazione VNet a livello di area funziona senza modifiche aggiuntive. Se si usa app Web per contenitori, è necessario modificare l'immagine Docker per usare l'integrazione VNet. Nell'immagine Docker usare la variabile di ambiente PORT come porta di ascolto del server Web principale, anziché usare un numero di porta hardcoded. La variabile di ambiente PORT viene impostata automaticamente dalla piattaforma del servizio app al momento dell'avvio del contenitore.

### <a name="service-endpoints"></a>Endpoint di servizio

La nuova funzionalità Integrazione rete virtuale consente di usare gli endpoint servizio.  Per usarli con l'app, usare la nuova funzionalità Integrazione rete virtuale per connettersi a una rete virtuale selezionata e configurare gli endpoint servizio nella subnet usata per l'integrazione. 


### <a name="how-vnet-integration-works"></a>Funzionamento di Integrazione rete virtuale

Le app nel servizio app sono ospitate in ruoli di lavoro. I piani tariffari di base e superiore sono piani di hosting dedicati in cui non sono presenti altri carichi di lavoro in esecuzione negli stessi thread di lavoro. L'integrazione di VNet funziona mediante l'installazione di interfacce virtuali con indirizzi nella subnet delegata. Poiché l'indirizzo from si trova nel VNet, può accedere alla maggior parte delle cose in o tramite il VNet proprio come una VM nel VNet. L'implementazione della rete è diversa rispetto all'esecuzione di una macchina virtuale nella VNet ed è per questo motivo che alcune funzionalità di rete non sono ancora disponibili durante l'uso di questa funzionalità.

![Integrazione rete virtuale](media/web-sites-integrate-with-vnet/vnet-integration.png)

Quando è abilitata l'integrazione con VNet, l'app effettuerà comunque chiamate in uscita a Internet tramite gli stessi canali del normale. Gli indirizzi in uscita elencati nel portale delle proprietà dell'app sono ancora gli indirizzi usati dall'app. Quali modifiche sono state apportate all'app, le chiamate a servizi protetti per endpoint di servizio o indirizzi RFC 1918 passano alla VNet. 

La funzionalità supporta solo un'interfaccia virtuale per ogni thread di lavoro.  Un'interfaccia virtuale per ogni thread di lavoro indica un'integrazione VNet a livello di area per ogni piano di servizio app. Tutte le app nello stesso piano di servizio app possono usare la stessa integrazione di VNet, ma se è necessario che un'app possa connettersi a un VNet aggiuntivo, sarà necessario creare un altro piano di servizio app. L'interfaccia virtuale utilizzata non è una risorsa a cui i clienti hanno accesso diretto.

A causa della natura del funzionamento di questa tecnologia, il traffico usato con l'integrazione di VNet non viene visualizzato nei log di flusso Network Watcher o NSG.  

## <a name="gateway-required-vnet-integration"></a>Integrazione VNet necessaria per il gateway 

La funzionalità di integrazione VNet necessaria per il gateway:

* Può essere usato per connettersi a reti virtuali in qualsiasi area Gestione risorse o reti virtuali classiche
* Consente a un'app di connettersi solo a 1 VNet alla volta
* Consente l'integrazione di un massimo di cinque reti virtuali con in un piano di servizio app 
* Consente l'uso della stessa VNet da parte di più app in un piano di servizio app senza influisca sul numero totale che può essere usato da un piano di servizio app.  Se sono presenti 6 app che usano lo stesso VNet nello stesso piano di servizio app, il numero è pari a 1 VNet in uso. 
* Richiede un gateway di rete virtuale configurato con una VPN da punto a sito
* Supporta uno SLA del 99,9% a causa del contratto di contratto sul gateway

Questa funzionalità non supporta:
* Usare con le app Linux
* Accesso alle risorse in ExpressRoute 
* Accesso alle risorse negli endpoint servizio 

### <a name="getting-started"></a>Introduzione

Prima di procedere con la connessione della propria app Web a una rete virtuale, è opportuno prendere in considerazione i seguenti aspetti:

* In una rete virtuale di destinazione deve essere abilitata la VPN da punto a sito con un gateway basato su route per poter eseguire la connessione a un'app. 
* La rete virtuale deve essere inclusa nella stessa sottoscrizione del piano di servizio app (ASP).
* Le app che si integrano con una rete virtuale usano il DNS specificato per quella rete virtuale.

### <a name="set-up-a-gateway-in-your-vnet"></a>Configurare un gateway nella rete virtuale ###

Se è già stato configurato un gateway con indirizzi da punto a sito, è possibile passare alla configurazione di Integrazione rete virtuale con l'app.  
Per creare un gateway:

1. [Creare una subnet del gateway][creategatewaysubnet] nella VNet.  

1. [Creare il gateway VPN][creategateway]. Selezionare un tipo di VPN basato su route.

1. [Impostare gli indirizzi da punto a sito][setp2saddresses]. Se il gateway non si trova nello SKU di base, IKEV2 deve essere disabilitato nella configurazione da punto a sito e SSTP deve essere selezionato. Lo spazio degli indirizzi deve essere nei blocchi di indirizzi RFC 1918, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Se si sta semplicemente creando il gateway per l'uso con l'integrazione VNet del servizio app, non è necessario caricare un certificato. La creazione del gateway può richiedere 30 minuti. Non sarà possibile integrare l'app con la rete virtuale fino al completamento del provisioning del gateway. 

### <a name="configure-vnet-integration-with-your-app"></a>Configurare Integrazione rete virtuale con l'app 

Per abilitare Integrazione rete virtuale nell'app: 

1. Passare all'app nel portale di Azure, aprire le impostazioni e selezionare Rete > Integrazione rete virtuale. L'ASP deve essere in uno SKU standard o meglio usare una delle funzionalità di integrazione di VNet. 
 ![Interfaccia utente di Integrazione rete virtuale][1]

1. Selezionare **Add VNet** (Aggiungi rete virtuale). 
 ![Aggiungere Integrazione rete virtuale][2]

1. Selezionare la rete virtuale. 
  ![Selezionare la rete virtuale][8]
  
L'app verrà riavviata dopo quest'ultimo passaggio.  

### <a name="how-the-gateway-required-vnet-integration-feature-works"></a>Funzionamento della funzionalità di integrazione VNet necessaria per il gateway

La funzionalità di integrazione VNet necessaria per il gateway è basata sulla tecnologia VPN da punto a sito. La tecnologia da punto a sito limita l'accesso alla rete alla sola macchina virtuale che ospita l'app. Le app possono esclusivamente inviare traffico in Internet tramite Connessioni ibride o Integrazione rete virtuale. 

![Funzionamento di Integrazione rete virtuale][3]

## <a name="managing-vnet-integration"></a>Gestione dell'integrazione di VNet
La possibilità di connettersi e disconnettersi da una rete virtuale è a livello di app. Le operazioni che possono influenzare Integrazione rete virtuale tra più app sono a livello del piano di Servizio app. Dall'app > Networking > portale di integrazione VNet, è possibile ottenere informazioni dettagliate sul VNet. È possibile visualizzare informazioni simili a livello di ASP nella rete ASP > > portale di integrazione VNet, incluse le app nel piano di servizio app che usano una data integrazione.

 ![Dettagli della rete virtuale][4]

Le informazioni disponibili nell'interfaccia utente di integrazione VNet sono le stesse tra i portali app e ASP.

* Nome rete virtuale: rimanda all'interfaccia utente della rete virtuale
* Località: indica la località della rete virtuale. L'integrazione con una rete virtuale in un'altra località può causare problemi di latenza per l'app. 
* Stato certificato: indica se i certificati sono sincronizzati tra il piano di Servizio app e la rete virtuale.
* Stato del gateway: se si usa l'integrazione VNet necessaria del gateway, è possibile visualizzare lo stato del gateway.
* Spazio di indirizzi della rete virtuale: mostra lo spazio di indirizzi IP della rete virtuale. 
* Spazio di indirizzi da punto a sito: mostra lo spazio di indirizzi IP da punto a sito della rete virtuale. Quando si effettuano chiamate nel VNet quando si usa la funzionalità gateway richiesta, l'app da indirizzo sarà uno di questi indirizzi. 
* Spazio di indirizzi da sito a sito: è possibile usare una connessione VPN da sito a sito per connettere la rete virtuale alle risorse locali o ad altre reti virtuali. Di seguito sono riportati gli intervalli IP definiti con la connessione VPN.
* Server DNS: mostra i server DNS configurati con la rete virtuale.
* Routing degli indirizzi IP alla rete virtuale: mostra i blocchi di indirizzi instradati usati per guidare traffico nella rete virtuale 

L'unica operazione possibile nella vista app di Integrazione rete virtuale è la disconnessione dell'app dalla rete virtuale a cui è attualmente connessa. Per disconnettere l'app da una rete virtuale, selezionare **Disconnetti**. L'app verrà riavviata quando ci si disconnette da una rete virtuale. La disconnessione non modifica la rete virtuale. La subnet o il gateway non viene rimosso. Se quindi si vuole eliminare la VNet, è necessario prima disconnettere l'app dalla VNet ed eliminare le risorse in esso presenti, ad esempio i gateway. 

Per raggiungere l'interfaccia utente di Integrazione rete virtuale del piano di servizio app, aprire l'interfaccia utente ASP e selezionare **Rete**.  In Integrazione rete virtuale selezionare **Fare clic qui per configurare** per aprire l'interfaccia utente di Stato funzionalità di rete.

![Informazioni di Integrazione rete virtuale del piano di servizio app][5]

L'interfaccia utente di Integrazione rete virtuale del piano di servizio app illustra tutte le reti virtuali usate dalle app nel piano di servizio app. Per visualizzare i dettagli su ogni rete virtuale, fare clic sulla rete virtuale in questione. Qui è possibile eseguire due azioni.

* **Sincronizza rete**. L'operazione di sincronizzazione della rete è solo per la funzionalità di integrazione VNet dipendente dal gateway. L'esecuzione di un'operazione di sincronizzazione della rete garantisce che i certificati e le informazioni di rete siano sincronizzati. Se si aggiunge o modifica il DNS della rete virtuale, è necessario eseguire un'operazione **Sincronizza rete**. Questa operazione riavvierà qualsiasi app con questa rete virtuale.
* **Aggiungi route**. L'aggiunta di route indirizzerà il traffico in uscita nella rete virtuale.

**Routing**: le route definite in una rete virtuale vengono usate per indirizzare il traffico dall'app alla propria rete virtuale. Se è necessario inviare altro traffico in uscita nella rete virtuale, è possibile aggiungere qui i blocchi di indirizzi. Questa funzionalità funziona solo con l'integrazione VNet obbligatoria del gateway.

**Certificati** Quando il gateway richiede l'integrazione di VNet abilitata, è necessario lo scambio di certificati per garantire la sicurezza della connessione. Con i certificati si ottengono la configurazione DNS, le route e altre informazioni simili che descrivono la rete.
Se vengono modificati i certificati o le informazioni di rete, è necessario fare clic su "Sincronizza rete". Quando si fa clic su "Sincronizza rete", si verifica una breve interruzione della connettività tra l'app e la rete virtuale. L'app non viene riavviata, ma la perdita di connettività potrebbe causare il funzionamento non corretto del sito. 

## <a name="accessing-on-premises-resources"></a>Accesso alle risorse locali
Le app possono accedere alle risorse locali grazie all'integrazione con le reti virtuali che dispongono di connessioni da sito a sito. Se si usa l'integrazione VNet necessaria per il gateway, è necessario aggiornare le route del gateway VPN locale con i blocchi di indirizzi da punto a sito. Quando la connessione VPN da sito a sito viene configurata per la prima volta, gli script usati per la configurazione devono configurare le route in modo appropriato. Se gli indirizzi da punto a sito vengono aggiunti dopo aver creato la VPN da sito a sito, è necessario aggiornare le route manualmente. Le informazioni dettagliate su come eseguire questa operazione variano in base al gateway e non sono descritte in questo documento. Non è possibile configurare BGP con una connessione VPN da sito a sito.

Non è necessaria alcuna configurazione aggiuntiva per la funzionalità di integrazione VNet a livello di area tramite VNet e in locale. È sufficiente connettere la VNet al sito locale usando ExpressRoute o una VPN da sito a sito. 

> [!NOTE]
> La funzionalità di integrazione VNet necessaria per il gateway non integra un'app con una VNet che dispone di un gateway ExpressRoute. Anche se il gateway ExpressRoute è configurato in [modalità][VPNERCoex] di coesistenza, l'integrazione di VNet non funziona. Se è necessario accedere alle risorse tramite una connessione ExpressRoute, è possibile usare la funzionalità di integrazione VNet a livello di area o un [ambiente del servizio app][ASE], che viene eseguito nel VNet. 
> 
> 

## <a name="peering"></a>Peering
Se si usa il peering con l'integrazione VNet a livello di area, non è necessario eseguire alcuna configurazione aggiuntiva. 

Se si usa il gateway necessario per l'integrazione di VNet con il peering, è necessario configurare alcuni elementi aggiuntivi. Per configurare il peering per interagire con l'app:

1. Aggiungere una connessione di peering nella rete virtuale a cui si connette l'app. Quando si aggiunge la connessione di peering, abilitare **Consenti accesso alla rete virtuale** e selezionare **Consenti traffico inoltrato** e **Consenti transito gateway**.
1. Aggiungere una connessione di peering nella rete virtuale sottoposta a peering alla rete virtuale a cui si è connessi. Quando si aggiunge la connessione di peering nella rete virtuale di destinazione, abilitare **Consenti accesso alla rete virtuale** e selezionare **Consenti traffico inoltrato** e **Usa gateway remoti**.
1. Passare a Piano di servizio app > Rete > interfaccia utente di Integrazione rete virtuale nel portale.  Selezionare la rete virtuale a cui si connette l'app. Nella sezione Routing aggiungere l'intervallo di indirizzi della rete virtuale sottoposta a peering alla rete virtuale a cui è connessa l'app.  


## <a name="pricing-details"></a>Dettagli prezzi
La funzionalità di integrazione VNet a livello di area non prevede costi aggiuntivi per l'utilizzo oltre l'importo del piano tariffario ASP.

Esistono tre addebiti correlati all'uso della funzionalità di integrazione VNet necessaria per il gateway:

* Costi del piano tariffario ASP: le app devono essere in un piano di servizio app standard, Premium o PremiumV2. Per altre informazioni sui costi, vedere [Prezzi del servizio app][ASPricing]. 
* Costi di trasferimento dei dati: è previsto un addebito per l'uscita dei dati, anche se il VNet è nello stesso data center. Questi addebiti sono descritti in [Dettagli prezzi trasferimento dati][DataPricing]. 
* Costi del gateway VPN: un costo per il gateway VNet richiesto per la VPN da punto a sito. I dettagli sono disponibili nella pagina dei [prezzi del gateway VPN][VNETPricing] .


## <a name="troubleshooting"></a>Risoluzione dei problemi
La funzionalità è semplice da configurare, ma possono comunque verificarsi problemi durante l'uso. In caso di problemi di accesso all'endpoint desiderato, sono disponibili varie utilità che permettono di testare la connettività dalla console dell'app. Le console disponibili sono due: la console Kudu e la console nel portale di Azure. Per accedere alla console Kudu dalla propria app, selezionare Strumenti -> Kudu. È anche possibile accedere alla console Kudo in [SiteName]. SCM. azurewebsites. NET. Una volta caricato il sito Web, passare alla scheda Debug Console. Per accedere alla console ospitata nel portale di Azure dalla propria app, selezionare Strumenti -> Console. 

#### <a name="tools"></a>Strumenti
Gli strumenti **ping**, **nslookup** e **tracert** non funzionano dalla console a causa di vincoli di sicurezza. Per questo motivo sono stati aggiunti due strumenti separati. Per testare la funzionalità del DNS è stato aggiunto lo strumento nameresolver.exe. La sintassi è:

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
* è la destinazione un indirizzo RFC 1918
* è presente un blocco NSG in uscita dalla subnet di integrazione
* Se si passa attraverso ExpressRoute o una VPN, è il gateway locale configurato per instradare il traffico al backup in Azure? Se è possibile raggiungere gli endpoint in VNet ma non in locale, è opportuno controllarlo.

**Integrazione VNet necessaria per il gateway**
* l'intervallo di indirizzi da punto a sito è compreso negli intervalli RFC 1918 (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Il gateway viene visualizzato come attivo nel portale? Se il gateway è inattivo, è necessario riattivarlo.
* I certificati vengono visualizzati come sincronizzati o si ritiene che la configurazione di rete sia stata modificata?  Se i certificati non sono sincronizzati o si ritiene che sia stata apportata una modifica alla configurazione di VNet che non è stata sincronizzata con gli ASP, fare clic su "Sincronizza rete".
* Se si passa attraverso ExpressRoute o una VPN, è il gateway locale configurato per instradare il traffico al backup in Azure? Se è possibile raggiungere gli endpoint in VNet ma non in locale, è opportuno controllarlo.

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


## <a name="powershell-automation"></a>Automazione di PowerShell

È possibile integrare Servizio app con una rete virtuale di Azure tramite PowerShell. Per uno script pronto per l'esecuzione, vedere [Connect an app in Azure App Service to an Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3) (Connettere un'app del Servizio app di Azure a una rete virtuale di Azure).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-details.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-aspdetails.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-newvnet.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-newvnetdetails.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-selectvnet.png

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
[creategatewaysubnet]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#gatewaysubnet
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
