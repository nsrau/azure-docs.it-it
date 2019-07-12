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
ms.devlang: na
ms.topic: article
ms.date: 07/09/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 940163d01e562d5a7d9107e8d893ba981fa0f84a
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795932"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrare un'app in una rete virtuale di Azure
Questo documento descrive la funzionalità integrazione rete virtuale di Azure App Service e come configurarlo con le app di [servizio App di Azure](https://go.microsoft.com/fwlink/?LinkId=529714). [Reti virtuali di Azure][VNETOverview] (Vnet) consentono di posizionare molte delle risorse di Azure in una rete instradabile non internet.  

Servizio App di Azure presenta due varianti. 

1. I sistemi multi-tenant che supportano l'intera gamma dei piani tariffari, ad eccezione di Isolated
2. L'App (ambiente del servizio), che consente di distribuire in una rete virtuale e supporta le app di piano di prezzi isolato

Questo documento tratta le due funzionalità integrazione rete virtuale, che viene usata in servizio App multi-tenant. Se l'app rimane [ambiente del servizio App][ASEintro], quindi è già in una rete virtuale e non richiede l'uso della funzionalità integrazione rete virtuale per raggiungere risorse nella stessa rete virtuale. Per informazioni dettagliate su tutte le funzionalità di rete del servizio App, vedere [rete le funzionalità del servizio App](networking-features.md)

Esistono due formati per la funzionalità integrazione rete virtuale

1. Una versione consente l'integrazione con le reti virtuali nella stessa area. Questo form della funzionalità richiede una subnet in una rete virtuale nella stessa area. Questa funzionalità è ancora in anteprima, ma è supportata per i carichi di produzione di app di Windows con alcuni avvertimenti indicati di seguito.
2. L'altra versione consente l'integrazione con le reti virtuali in altre aree o con reti virtuali classiche. Questa versione della funzionalità richiede la distribuzione di un Gateway di rete virtuale nella rete virtuale. Questa è la funzionalità basate su VPN da punto a sito ed è supportata solo con le app di Windows.

Un'app può usare solo una forma della funzionalità integrazione rete virtuale alla volta. La domanda è quale funzionalità usare. È possibile usare per molte operazioni. Tuttavia sono la ripetitività chiaro:

| Problema  | Soluzione | 
|----------|----------|
| Vuole raggiungere un indirizzi RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) nella stessa area | Integrazione di reti virtuali regionali |
| Per poter raggiungere le risorse in una rete virtuale classica o una rete virtuale in un'altra area | Gateway richiesto integrazione rete virtuale |
| Per raggiungere gli endpoint di RFC 1918 in ExpressRoute | Integrazione di reti virtuali regionali |
| Per poter raggiungere le risorse tra gli endpoint di servizio | Integrazione di reti virtuali regionali |

Nessuna delle due funzionalità consentirà di raggiungere gli indirizzi non RFC 1918 attraverso ExpressRoute. Per tale scopo, è necessario usare un ambiente del servizio App per il momento.

Tramite l'integrazione rete virtuale regionale non connettere la rete virtuale in locale o configurare gli endpoint di servizio. Che è separato configurazione della rete. L'integrazione rete virtuale regionale consente semplicemente all'app effettuare chiamate tra i tipi di connessione.

Indipendentemente dalla versione usata, integrazione rete virtuale consente all'app l'accesso web alle risorse nella rete virtuale ma non concede l'accesso in ingresso privato all'App web dalla rete virtuale. Per accesso privato s'intende la possibilità di rendere l'app accessibile soltanto da una rete privata, ad esempio all'interno di una rete virtuale di Azure. Integrazione rete virtuale è solo per chiamate in uscita dall'app alla propria rete virtuale. 

La funzionalità Integrazione rete virtuale:

* richiede un piano tariffario Standard, Premium o PremiumV2 
* supporta TCP e UDP
* funziona con le app di servizio App e App per le funzioni

Alcune operazioni non sono supportate da Integrazione rete virtuale:

* montaggio di un'unità
* integrazione di AD 
* NetBios

## <a name="regional-vnet-integration"></a>Integrazione di reti virtuali regionali 

Integrazione rete virtuale viene usato con le reti virtuali nella stessa area dell'App, richiede l'uso di una subnet con almeno 32 indirizzi in esso delegata. La subnet non è utilizzabile per qualsiasi altra esigenza. Le chiamate in uscita effettuate dall'app verranno eseguite dagli indirizzi nella subnet di delegato. Quando si usa questa versione di integrazione rete virtuale, le chiamate da indirizzi nella rete virtuale. Uso di indirizzi della rete virtuale consente all'app per:

* effettuare chiamate a endpoint del servizio servizi Web protetti
* accedere alle risorse per le connessioni di ExpressRoute
* accedere alle risorse nella rete virtuale si è connessi a
* accedere alle risorse per le connessioni di peering tra cui le connessioni ExpressRoute

Questa funzionalità è disponibile in anteprima, ma è supportata per i carichi di produzione di app Windows con le limitazioni seguenti:

* è possibile raggiungere solo gli indirizzi inclusi nell'intervallo di RFC 1918. Questi sono gli indirizzi in 10.0.0.0/8, 172.16.0.0/12, blocchi di indirizzi 192.168.0.0/16.
* non è possibile raggiungere le risorse tra le connessioni peering globale
* è possibile impostare le route per il traffico proveniente dall'app alla propria rete virtuale
* la funzionalità è disponibile solo dalla più recente unità di scala del servizio App che supportano i piani di servizio App di PremiumV2.
* La subnet di integrazione può essere usata solo da un solo piano di servizio App
* la funzionalità non può essere usata dall'App piano isolato in un ambiente del servizio App
* La funzionalità richiede una subnet inutilizzata sia/27 con 32 indirizzi o più in VNet di Resource Manager
* L'app e la rete virtuale devono essere nella stessa area
* Non è possibile eliminare una rete virtuale con un'app integrata. È necessario innanzitutto rimuovere l'integrazione 
* È possibile avere un solo integrazione rete virtuale a livello di area per ogni piano di servizio App. Più App nello stesso piano di servizio App possono usare la stessa rete virtuale. 

Viene usato un indirizzo per ogni istanza del piano di servizio app. Se si tornasse a 5 istanze, l'app che è di 5 indirizzi. Poiché le dimensioni della subnet non possono essere modificata dopo l'assegnazione, è necessario usare una subnet sufficientemente grande da contenere qualsiasi scala può raggiungere l'app. Con 32 indirizzi/27 è la dimensione consigliata come che potrebbe contenere un piano di servizio App Premium viene ridotto fino a 20 istanze.

La funzionalità è disponibile in anteprima per Linux. Per usare la funzionalità integrazione rete virtuale con una VNet Resource Manager nella stessa area:

1. Passare all'interfaccia utente di Rete nel portale. Se l'app è in grado di usare la nuova funzionalità, si vedranno un'opzione per aggiungere rete virtuale (anteprima).  

   ![Selezionare integrazione rete virtuale][6]

1. Selezionare **Aggiungi rete virtuale (anteprima)** .  

1. Selezionare la rete virtuale di Resource Manager da integrare e creare una nuova subnet o sceglierne una vuota esistente. Il completamento dell'integrazione richiede meno di un minuto. Durante l'integrazione, l'app viene riavviata.  Al termine dell'integrazione verranno visualizzati i dettagli della rete virtuale da integrare e un banner in alto indicante che la funzionalità è in anteprima.

   ![Selezionare la rete virtuale e la subnet][7]

Dopo che l'app è integrata con la rete virtuale, userà lo stesso server DNS configurato con la rete virtuale. 

Per disconnettere l'app dalla rete virtuale, selezionare **Disconnetti**. L'app Web verrà riavviata. 


#### <a name="web-app-for-containers"></a>App Web per contenitori

Se si usa servizio App in Linux con immagini predefinite, la funzionalità integrazione rete virtuale regionale funziona senza ulteriori modifiche. Se si usa App Web per contenitori, è necessario modificare l'immagine docker per usare l'integrazione rete virtuale. Nell'immagine docker, usare la variabile di ambiente PORT come porta di ascolto del server web principale, invece di usare un numero di porta hardcoded. La variabile di ambiente PORT viene impostata automaticamente dalla piattaforma servizio App in fase di avvio il contenitore.

### <a name="service-endpoints"></a>Endpoint servizio

La nuova funzionalità Integrazione rete virtuale consente di usare gli endpoint servizio.  Per usarli con l'app, usare la nuova funzionalità Integrazione rete virtuale per connettersi a una rete virtuale selezionata e configurare gli endpoint servizio nella subnet usata per l'integrazione. 


### <a name="how-vnet-integration-works"></a>Funzionamento di Integrazione rete virtuale

Le app nel servizio App sono ospitate nei ruoli di lavoro. La base e versioni successive ai piani tariffari sono dedicate i piani di hosting in cui non sono presenti nessun altro carico di clienti in esecuzione in ruoli di lavoro stesso. Integrazione rete virtuale funziona eseguendo il mounting virtuale interfacce con indirizzi nella subnet di delegato. Poiché dall'indirizzo è nella rete virtuale, dispone dell'accesso per la maggior parte delle operazioni o attraverso la rete virtuale come farebbe una macchina virtuale nella rete virtuale. L'implementazione di rete è diversa rispetto all'esecuzione di una macchina virtuale in una rete virtuale e che è il motivo per cui alcune funzionalità di rete non sono ancora disponibili quando si usa questa funzionalità.

![Integrazione rete virtuale](media/web-sites-integrate-with-vnet/vnet-integration.png)

Quando è abilitata l'integrazione rete virtuale, l'app verrà comunque effettuare chiamate in uscita a internet tramite gli stessi canali come di consueto. Gli indirizzi in uscita che sono elencati nel portale di proprietà dell'app sono comunque gli indirizzi usati dall'app. Quali sono le modifiche per l'app, le chiamate a endpoint di servizio protetto di servizi o gli indirizzi RFC 1918 entra in una rete virtuale. 

La funzionalità supporta solo un'interfaccia virtuale per ogni ruolo di lavoro.  Un'interfaccia virtuale per ogni ruolo di lavoro indica una integrazione rete virtuale a livello di area per ogni piano di servizio App. Tutte le app nello stesso piano di servizio App può usare l'integrazione rete virtuale stessa, ma se è necessaria un'app per la connessione a una rete virtuale aggiuntiva, è necessario creare un altro piano di servizio App. L'interfaccia virtuale usato non è una risorsa che i clienti hanno accesso diretto a.

A causa della natura del funzionamento di questa tecnologia, il traffico che viene usato con integrazione rete virtuale non viene visualizzato nei log dei flussi di Network Watcher o sicurezza di rete.  

## <a name="gateway-required-vnet-integration"></a>Gateway richiesto integrazione rete virtuale 

Il Gateway necessarie funzionalità integrazione rete virtuale:

* può essere usato per connettersi alle reti virtuali in tutte le aree siano essi le reti virtuali classiche o Resource Manager
* consente a un'app per la connessione a 1 sola rete virtuale alla volta
* Consente di integrare in un piano di servizio App con reti virtuali fino a cinque 
* consente la stessa rete virtuale essere usati da più App in un piano di servizio App senza conseguenze per il numero totale che può essere usato da un piano di servizio App.  Se si dispone di 6 le app usano la stessa rete virtuale nello stesso piano di servizio App, che viene conteggiata come 1 rete virtuale in uso. 
* richiede un Gateway di rete virtuale configurata con punto a sito VPN
* Non è supportata per l'uso con le app Linux
* Supporta un contratto di servizio del 99,9% a causa di un contratto di servizio nel gateway

Questa funzionalità non supporta:

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

1. [Creare una subnet del gateway][creategatewaysubnet] nella rete virtuale.  

1. [Creare il gateway VPN][creategateway]. Selezionare un tipo di VPN basato su route.

1. [Impostare il punto per indirizzi di siti][setp2saddresses]. Se il gateway non si trova nello SKU di base, IKEV2 deve essere disabilitato nella configurazione da punto a sito e SSTP deve essere selezionato. Lo spazio degli indirizzi deve essere nei blocchi di indirizzi RFC 1918, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Se si è sufficiente creare il gateway per usare con integrazione rete virtuale del servizio App, quindi non è necessario caricare un certificato. La creazione del gateway può richiedere 30 minuti. Non sarà possibile integrare l'app con la rete virtuale fino al completamento del provisioning del gateway. 

### <a name="configure-vnet-integration-with-your-app"></a>Configurare Integrazione rete virtuale con l'app 

Per abilitare Integrazione rete virtuale nell'app: 

1. Passare all'app nel portale di Azure, aprire le impostazioni e selezionare Rete > Integrazione rete virtuale. Il piano ASP deve essere in uno SKU Standard o è preferibile per usare tali funzionalità integrazione rete virtuale. 
 ![Interfaccia utente di Integrazione rete virtuale][1]

1. Selezionare **Add VNet** (Aggiungi rete virtuale). 
 ![Aggiungere Integrazione rete virtuale][2]

1. Selezionare la rete virtuale. 
  ![Selezionare la rete virtuale][8]
  
L'app verrà riavviata dopo quest'ultimo passaggio.  

### <a name="how-the-gateway-required-vnet-integration-feature-works"></a>Funziona come il gateway necessario funzionalità integrazione rete virtuale

Funzionalità integrazione rete virtuale è basato sulla tecnologia VPN da punto a sito è necessario il gateway. La tecnologia da punto a sito limita l'accesso alla rete alla sola macchina virtuale che ospita l'app. Le app possono esclusivamente inviare traffico in Internet tramite Connessioni ibride o Integrazione rete virtuale. 

![Funzionamento di Integrazione rete virtuale][3]

## <a name="managing-vnet-integration"></a>Gestione dell'integrazione rete virtuale
La possibilità di connettersi e disconnettersi da una rete virtuale è a livello di app. Le operazioni che possono influenzare Integrazione rete virtuale tra più app sono a livello del piano di Servizio app. Dall'app > rete > portale di integrazione rete virtuale, è possibile ottenere informazioni dettagliate sulla rete virtuale. È possibile visualizzare informazioni simili a livello di ASP in ASP > rete > portale di integrazione rete virtuale tra cui le app nel piano di servizio App Usa una data integrazione.

 ![Dettagli della rete virtuale][4]

Le informazioni sono disponibili nell'interfaccia utente di integrazione rete virtuale è lo stesso tra l'app e i portali ASP.

* Nome rete virtuale: rimanda all'interfaccia utente della rete virtuale
* Località: indica la località della rete virtuale. L'integrazione con una rete virtuale in un'altra località può causare problemi di latenza per l'app. 
* Stato certificato: indica se i certificati sono sincronizzati tra il piano di Servizio app e la rete virtuale.
* È stato dei gateway: deve essere usando il gateway necessarie integrazione rete virtuale, è possibile visualizzare lo stato del gateway.
* Spazio di indirizzi della rete virtuale: mostra lo spazio di indirizzi IP della rete virtuale. 
* Spazio di indirizzi da punto a sito: mostra lo spazio di indirizzi IP da punto a sito della rete virtuale. Quando si effettuano chiamate in una rete virtuale quando si usa la funzionalità necessari gateway, l'indirizzo del mittente app sarà uno di questi indirizzi. 
* Spazio di indirizzi da sito a sito: è possibile usare una connessione VPN da sito a sito per connettere la rete virtuale alle risorse locali o ad altre reti virtuali. Di seguito sono riportati gli intervalli IP definiti con la connessione VPN.
* Server DNS: mostra i server DNS configurati con la rete virtuale.
* Routing degli indirizzi IP alla rete virtuale: mostra i blocchi di indirizzi instradati usati per guidare traffico nella rete virtuale 

L'unica operazione possibile nella vista app di Integrazione rete virtuale è la disconnessione dell'app dalla rete virtuale a cui è attualmente connessa. Per disconnettere l'app da una rete virtuale, selezionare **Disconnetti**. L'app verrà riavviata quando ci si disconnette da una rete virtuale. La disconnessione non modifica la rete virtuale. La subnet o gateway non viene rimosso. Se quindi si desidera eliminare la rete virtuale, è necessario prima di disconnettere l'app dalla rete virtuale ed eliminare le risorse in esso contenute, ad esempio i gateway. 

Per raggiungere l'interfaccia utente di Integrazione rete virtuale del piano di servizio app, aprire l'interfaccia utente ASP e selezionare **Rete**.  In Integrazione rete virtuale selezionare **Fare clic qui per configurare** per aprire l'interfaccia utente di Stato funzionalità di rete.

![Informazioni di Integrazione rete virtuale del piano di servizio app][5]

L'interfaccia utente di Integrazione rete virtuale del piano di servizio app illustra tutte le reti virtuali usate dalle app nel piano di servizio app. Per visualizzare i dettagli su ogni rete virtuale, fare clic sulla rete virtuale in questione. Qui è possibile eseguire due azioni.

* **Sincronizza rete**. L'operazione di rete di sincronizzazione è solo per la funzionalità integrazione rete virtuale di dipendente dal gateway. Esecuzione di un'operazione di rete di sincronizzazione assicura che i certificati e le informazioni di rete siano sincronizzati. Se si aggiunge o modifica il DNS della rete virtuale, è necessario eseguire un'operazione **Sincronizza rete**. Questa operazione riavvierà qualsiasi app con questa rete virtuale.
* **Aggiungi route**. L'aggiunta di route indirizzerà il traffico in uscita nella rete virtuale.

**Routing**: le route definite in una rete virtuale vengono usate per indirizzare il traffico dall'app alla propria rete virtuale. Se è necessario inviare altro traffico in uscita nella rete virtuale, è possibile aggiungere qui i blocchi di indirizzi. Funzionalità funziona solo con il gateway necessario integrazione rete virtuale.

**I certificati** quando il gateway necessario abilitata l'integrazione rete virtuale, è necessario uno scambio di certificati per garantire la sicurezza della connessione. Con i certificati si ottengono la configurazione DNS, le route e altre informazioni simili che descrivono la rete.
Se vengono modificati i certificati o le informazioni di rete, è necessario fare clic su "Sincronizza rete". Quando si fa clic su "Sincronizza rete", si verifica una breve interruzione della connettività tra l'app e la rete virtuale. L'app non viene riavviata, ma la perdita di connettività potrebbe causare il funzionamento non corretto del sito. 

## <a name="accessing-on-premises-resources"></a>Accesso alle risorse locali
Le app possono accedere alle risorse locali grazie all'integrazione con le reti virtuali che dispongono di connessioni da sito a sito. Se si usa il gateway necessarie integrazione rete virtuale, è necessario aggiornare le route di gateway VPN da sito locale con i blocchi di indirizzi da punto a sito. Quando la connessione VPN da sito a sito viene configurata per la prima volta, gli script usati per la configurazione devono configurare le route in modo appropriato. Se gli indirizzi da punto a sito vengono aggiunti dopo aver creato la VPN da sito a sito, è necessario aggiornare le route manualmente. Le informazioni dettagliate su come eseguire questa operazione variano in base al gateway e non sono descritte in questo documento. Non è possibile avere BGP configurati con una connessione VPN site-to-site.

Non vi è alcuna configurazione aggiuntiva necessaria per la funzionalità integrazione rete virtuale regionale raggiungere tramite la rete virtuale e in locale. È sufficiente connettere la rete virtuale in locale usando una VPN site-to-site o ExpressRoute. 

> [!NOTE]
> Funzionalità integrazione rete virtuale non si integra un'app con una rete virtuale con un ExpressRoute Gateway è necessario il gateway. Anche se il ExpressRoute Gateway è configurato [modalità di coesistenza][VPNERCoex] the VNet Integration doesn't work. If you need to access resources through an ExpressRoute connection, then you can use the regional VNet Integration feature or an [App Service Environment][ASE], che viene eseguito in una rete virtuale. 
> 
> 

## <a name="peering"></a>Peering
Se si usa il peering con l'integrazione rete virtuale regionale, non occorre alcuna configurazione aggiuntiva. 

Se si usa il gateway necessarie integrazione rete virtuale con peering, è necessario configurare alcuni elementi aggiuntivi. Per configurare il peering per interagire con l'app:

1. Aggiungere una connessione di peering nella rete virtuale a cui si connette l'app. Quando si aggiunge la connessione di peering, abilitare **Consenti accesso alla rete virtuale** e selezionare **Consenti traffico inoltrato** e **Consenti transito gateway**.
1. Aggiungere una connessione di peering nella rete virtuale sottoposta a peering alla rete virtuale a cui si è connessi. Quando si aggiunge la connessione di peering nella rete virtuale di destinazione, abilitare **Consenti accesso alla rete virtuale** e selezionare **Consenti traffico inoltrato** e **Usa gateway remoti**.
1. Passare a Piano di servizio app > Rete > interfaccia utente di Integrazione rete virtuale nel portale.  Selezionare la rete virtuale a cui si connette l'app. Nella sezione Routing aggiungere l'intervallo di indirizzi della rete virtuale sottoposta a peering alla rete virtuale a cui è connessa l'app.  


## <a name="pricing-details"></a>Dettagli prezzi
La funzionalità integrazione rete virtuale regionale non dispone costi aggiuntivi per l'uso oltre tariffario addebiti a livello di ASP.

Esistono tre costi correlati all'uso della funzionalità integrazione rete virtuale di gateway necessari:

* Addebiti livello tariffari ASP - le app devono essere in un piano Standard, Premium o piano di servizio App PremiumV2. Per altre informazioni sui costi, vedere [Prezzi del servizio app][ASPricing]. 
* Costi di trasferimento dati - è un addebito per i dati in uscita, anche se la rete virtuale è nello stesso data center. Gli addebiti sono descritti nel [dettagli prezzi dei trasferimenti di dati][DataPricing]. 
* Costi del Gateway VPN - è un costo per il gateway di rete virtuale che è necessario per la connessione VPN point-to-site. I dettagli presenti il [prezzi di Gateway VPN][VNETPricing] pagina.


## <a name="troubleshooting"></a>risoluzione dei problemi
La funzionalità è semplice da configurare, ma possono comunque verificarsi problemi durante l'uso. In caso di problemi di accesso all'endpoint desiderato, sono disponibili varie utilità che permettono di testare la connettività dalla console dell'app. Le console disponibili sono due: la console Kudu e la console nel portale di Azure. Per accedere alla console Kudu dalla propria app, selezionare Strumenti -> Kudu. Questa operazione equivale a visitare [nomesito].scm.azurewebsites.net. Dopo l'apertura, passare alla scheda Console di debug. Per accedere alla console ospitata nel portale di Azure dalla propria app, selezionare Strumenti -> Console. 

#### <a name="tools"></a>Strumenti
Gli strumenti **ping**, **nslookup** e **tracert** non funzionano dalla console a causa di vincoli di sicurezza. Per questo motivo sono stati aggiunti due strumenti separati. Per testare la funzionalità del DNS è stato aggiunto lo strumento nameresolver.exe. La sintassi è:

    nameresolver.exe hostname [optional: DNS Server]

È possibile usare **nameresolver** per controllare i nomi host da cui dipende l'app. In questo modo è possibile verificare se la configurazione del server DNS è corretta e se si ha accesso al server DNS. È possibile visualizzare il server DNS che userà l'app nella console, esaminare le variabili di ambiente WEBSITE_DNS_SERVER e WEBSITE_DNS_ALT_SERVER.

Il secondo strumento permette di verificare la connettività TCP a una combinazione di host e porta. Questo strumento viene chiamato **tcpping** la cui sintassi è:

    tcpping.exe hostname [optional: port]

L'utilità **tcpping** indica se è possibile raggiungere una porta e un host specifici. Essa visualizza il completamento solo se: è presente un'applicazione in ascolto presso la combinazione di host e porta ed è presente un accesso di rete dall'app all'host e alla porta specificati.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Accesso di debug a risorse ospitate su una rete virtuale
L'app potrebbe non riuscire a raggiungere un host e una porta specifici per una serie di motivi. Nella maggior parte dei casi il motivo è uno dei seguenti:

* **L'ostacolo è rappresentato da un firewall**. Se è presente un firewall, si verifica il timeout TCP, che in questo caso è di 21 secondi. Usare lo strumento **tcpping** per testare la connettività. Il timeout TCP può essere dovuto anche ad altri motivi, ma è consigliabile iniziare dal firewall. 
* **Il DNS non è accessibile**. Il timeout DNS è di tre secondi per ogni server DNS. Se si dispone di due server DNS, il timeout è di 6 secondi. Usare nameresolver per verificare il funzionamento del DNS. Si noti che non è possibile usare nslookup, perché non usa il DNS con cui è configurata la rete virtuale. Se è inaccessibile, è possibile avere un firewall o blocco dell'accesso di sicurezza di rete a DNS oppure potrebbe essere inattivo.

Se i problemi di non rispondono a tali elementi, simile al primo per elementi quali: 

**Integrazione di reti virtuali regionali**
* è la destinazione in un indirizzo di RFC 1918
* è previsto un uscita blocco di sicurezza di rete dalla subnet di integrazione
* Se in una rete VPN o ExpressRoute, è il gateway locale configurato per instradare il traffico di backup in Azure? Se è possibile raggiungere gli endpoint in cui la rete virtuale ma non in locale, è consigliabile controllare.

**Gateway richiesto integrazione rete virtuale**
* è l'intervallo di indirizzi da punto a sito negli intervalli RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255)?
* Il gateway viene visualizzato come attivo nel portale? Se il gateway è inattivo, è necessario riattivarlo.
* I certificati vengono visualizzati come sincronizzati o si ritiene che la configurazione di rete è stata modificata?  Se i certificati sono sincronizzati o si sospetta che sia stata apportata una modifica alla configurazione di rete virtuale che non è stata sincronizzata con le pagine ASP, quindi premere "Sincronizzazione della rete".
* Se in una rete VPN o ExpressRoute, è il gateway locale configurato per instradare il traffico di backup in Azure? Se è possibile raggiungere gli endpoint in cui la rete virtuale ma non in locale, è consigliabile controllare.

Debug di problemi di rete è una sfida poiché non esiste è possibile visualizzare ciò che sta bloccando l'accesso a una combinazione host: porta specifica. Tra le cause possibili:

* È presente un firewall sull'host che impedisce l'accesso alla porta dell'applicazione dall'intervallo IP da punto a sito. Il passaggio tra subnet spesso richiede l'accesso pubblico.
* L'host di destinazione è inattivo.
* L'applicazione è inattiva.
* L'IP o il nome host è errato.
* L'applicazione è in ascolto su una porta diversa da quella prevista. È possibile associare l'ID di processo alla porta di ascolto mediante "netstat -aon" nell'host dell'endpoint. 
* I gruppi di sicurezza di rete sono configurati in modo da impedire l'accesso all'host e alla porta dell'applicazione dall'intervallo IP da punto a sito.

Tenere presente che non si conosce quale indirizzo lo usi effettivamente l'app. Potrebbe essere qualsiasi indirizzo dell'intervallo di indirizzi subnet o point-to-site integration, pertanto è necessario consentire l'accesso dall'intervallo di indirizzi. 

Di seguito è riportata la procedura di debug aggiuntiva:

* connettersi a un'altra macchina virtuale nella rete virtuale e provare a raggiungere l'host:porta della risorsa da quel punto. Per verificare l'accesso al protocollo TCP usare il comando di PowerShell **test-netconnection**. La sintassi è:

      test-netconnection hostname [optional: -Port]

* visualizzare un'applicazione in una macchina virtuale e testare l'accesso a tale host e porta dalla console all'app usando **tcpping**

#### <a name="on-premises-resources"></a>Risorse locali ####

Se l'app non riesce a raggiungere una risorsa locale, verificare se è possibile raggiungere la risorsa dalla propria rete virtuale. Usare il comando di PowerShell **test-netconnection** per eseguire controllare l'accesso al protocollo TCP. Se la macchina virtuale non riesce a raggiungere la risorsa locale, la connessione VPN o ExpressRoute potrà non essere configurata correttamente.

Se la macchina virtuale ospitata nella rete virtuale può raggiungere il sistema locale ma l'app non ci riesce, la causa è probabilmente una delle seguenti:

* le route non sono configurate con la subnet o puntare a intervalli di indirizzi del sito nel gateway locale
* i gruppi di sicurezza della rete bloccano l'accesso all'intervallo IP da punto a sito
* i firewall locali bloccano il traffico proveniente dall'intervallo IP da punto a sito
* si sta tentando di raggiungere un indirizzo non RFC 1918 usando la funzionalità integrazione rete virtuale a livello di area


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
