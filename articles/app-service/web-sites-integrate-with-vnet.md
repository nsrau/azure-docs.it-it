---
title: Integrazione di un'app in una rete virtuale di Azure
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
ms.date: 11/12/2018
ms.author: ccompy
ms.openlocfilehash: 8a6d7fef902a3bd240b152cb15d6852a5fa0e7c6
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51687307"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrare un'app in una rete virtuale di Azure
Questo documento descrive la funzionalità di integrazione del servizio app di Azure in una rete virtuale, specificando come configurarla con le app del [servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Le [reti virtuali di Azure][VNETOverview] consentono di posizionare molte risorse di Azure in una rete instradabile non Internet. Queste reti possono quindi essere connesse alle reti locali mediante tecnologie VPN. 

Il servizio app di Azure è disponibile in due forme. 

1. I sistemi multi-tenant che supportano l'intera gamma dei piani tariffari, ad eccezione di Isolated
2. L'ambiente del servizio app che consente la distribuzione nella rete virtuale 

Questo documento illustra la funzionalità Integrazione rete virtuale, destinata all'uso nel servizio app multi-tenant.  Se l'app è nell'[ambiente del servizio App][ASEintro], è già in una rete virtuale e non richiede l'uso della funzionalità Integrazione rete virtuale per raggiungere risorse nella stessa rete virtuale.

Integrazione rete virtuale consente all'app Web di accedere alle risorse presenti nella propria rete virtuale. Al contrario, l'accesso privato all'app Web dalla rete virtuale non viene concesso. Per accesso privato s'intende la possibilità di rendere l'app accessibile soltanto da una rete privata, ad esempio all'interno di una rete virtuale di Azure. L'accesso privato al sito è disponibile solo con un ambiente del servizio app configurato con un servizio di bilanciamento del carico interno (ILB). Per informazioni dettagliate in proposito, vedere [Uso di un servizio di bilanciamento del carico interno con un ambiente del servizio app][ILBASE]. 

Integrazione rete virtuale viene spesso usata per consentire l'accesso dalle app ai database e ai servizi Web in esecuzione nella rete virtuale. Con Integrazione rete virtuale non è necessario esporre un endpoint pubblico per le applicazioni nella macchina virtuale ma è possibile usare gli indirizzi instradabili non Internet privati. 

La funzionalità Integrazione rete virtuale:

* richiede un piano tariffario Standard, Premium o PremiumV2 
* funziona con reti virtuali classiche o di Resource Manager 
* supporta TCP e UDP
* per app Web, dispositivi mobili, API e app per le funzioni
* consente a un'app di connettersi a 1 sola rete virtuale alla volta
* consente di integrare fino a cinque reti virtuali in un piano di servizio app 
* consente l'uso della stessa rete virtuale da parte di più app in un piano di servizio app
* richiede un gateway di rete virtuale configurato con la VPN da punto a sito
* supporta un contratto di servizio pari al 99,9% grazie al contratto di servizio sul gateway

Alcune operazioni non sono supportate da Integrazione rete virtuale:

* montaggio di un'unità
* integrazione di AD 
* NetBios
* accesso al sito privato
* accesso alle risorse in ExpressRoute 
* accesso alle risorse negli endpoint servizio 

### <a name="getting-started"></a>Introduzione
Prima di procedere con la connessione della propria app Web a una rete virtuale, è opportuno prendere in considerazione i seguenti aspetti:

* In una rete virtuale di destinazione deve essere abilitata la VPN da punto a sito con un gateway basato su route per poter eseguire la connessione a un'app. 
* La rete virtuale deve essere inclusa nella stessa sottoscrizione del piano di servizio app (ASP).
* Le app che si integrano con una rete virtuale usano il DNS specificato per quella rete virtuale.

## <a name="enabling-vnet-integration"></a>Abilitazione della funzionalità Integrazione rete virtuale

È disponibile in anteprima una nuova versione della funzionalità Integrazione rete virtuale. Non dipende dalla VPN da punto a sito e supporta anche l'accesso alle risorse in ExpressRoute o negli endpoint servizio. Per altre informazioni sulla nuova funzionalità in anteprima, procedere alla fine di questo documento. 

### <a name="set-up-a-gateway-in-your-vnet"></a>Configurare un gateway nella rete virtuale ###

Se è già stato configurato un gateway con indirizzi da punto a sito, è possibile passare alla configurazione di Integrazione rete virtuale con l'app.  
Per creare un gateway:

1. [Creare una subnet del gateway][creategatewaysubnet] nella rete virtuale.  

1. [Creare il gateway VPN][creategateway]. Selezionare un tipo di VPN basato su route.

1. [Impostare gli indirizzi da punto a sito][setp2saddresses]. Se il gateway non si trova nello SKU di base, IKEV2 deve essere disabilitato nella configurazione da punto a sito e SSTP deve essere selezionato. Lo spazio degli indirizzi deve essere in uno dei blocchi di indirizzi seguenti:

* 10.0.0.0/8: un intervallo di indirizzi IP da 10.0.0.0 a 10.255.255.255
* 172.16.0.0/12: un intervallo di indirizzi IP da 172.16.0.0 a 172.31.255.255 
* 192.168.0.0/16: un intervallo di indirizzi IP da 192.168.0.0 a 192.168.255.255

Se si sta semplicemente creando il gateway per l'uso con Integrazione rete virtuale del servizio app, non è necessario caricare un certificato. La creazione del gateway può richiedere 30 minuti. Non sarà possibile integrare l'app con la rete virtuale fino al completamento del provisioning del gateway. 

### <a name="configure-vnet-integration-with-your-app"></a>Configurare Integrazione rete virtuale con l'app ###

Per abilitare Integrazione rete virtuale nell'app: 

1. Passare all'app nel portale di Azure, aprire le impostazioni e selezionare Rete > Integrazione rete virtuale. Ridimensionare ASP in uno SKU Standard o superiore prima di poter configurare Integrazione rete virtuale. 
 ![Interfaccia utente di Integrazione rete virtuale][1]

1. Selezionare **Add VNet** (Aggiungi rete virtuale). 
 ![Aggiungere Integrazione rete virtuale][2]

1. Selezionare la rete virtuale. 
  ![Selezionare la rete virtuale][8]
  
L'app verrà riavviata dopo quest'ultimo passaggio.  

## <a name="how-the-system-works"></a>Funzionamento del sistema
La funzionalità Integrazione rete virtuale si basa sulla tecnologia VPN da punto a sito. Le app nel Servizio app di Azure sono ospitate in un sistema multi-tenant che preclude il provisioning di un'app direttamente in una rete virtuale. La tecnologia da punto a sito limita l'accesso alla rete alla sola macchina virtuale che ospita l'app. Le app possono esclusivamente inviare traffico in Internet tramite Connessioni ibride o Integrazione rete virtuale. 

![Funzionamento di Integrazione rete virtuale][3]

## <a name="managing-the-vnet-integrations"></a>Gestione delle integrazioni di reti virtuali
La possibilità di connettersi e disconnettersi da una rete virtuale è a livello di app. Le operazioni che possono influenzare Integrazione rete virtuale tra più app sono a livello del piano di Servizio app. Dall'UID dell'app è possibile ottenere dettagli sulla rete virtuale. Le stesse informazioni vengono visualizzate anche a livello di ASP. 

 ![Dettagli della rete virtuale][4]

Dalla pagina Stato funzionalità di rete è possibile vedere se l'app è connessa alla rete virtuale. Se il gateway della rete virtuale è inattivo per qualsiasi motivo, lo stato viene visualizzato come non connesso. 

Le informazioni ora disponibili nell'interfaccia utente di integrazione rete virtuale a livello di app sono le stesse fornite a livello di ASP. Nel dettaglio:

* Nome rete virtuale: rimanda all'interfaccia utente della rete virtuale
* Località: indica la località della rete virtuale. L'integrazione con una rete virtuale in un'altra località può causare problemi di latenza per l'app. 
* Stato certificato: indica se i certificati sono sincronizzati tra il piano di Servizio app e la rete virtuale.
* Stato dei gateway: se i gateway sono inattivi per qualsiasi motivo, l'app non può accedere alle risorse nella rete virtuale. 
* Spazio di indirizzi della rete virtuale: mostra lo spazio di indirizzi IP della rete virtuale. 
* Spazio di indirizzi da punto a sito: mostra lo spazio di indirizzi IP da punto a sito della rete virtuale. Quando si effettuano chiamate nella rete virtuale, l'indirizzo del mittente dell'app sarà uno di questi indirizzi. 
* Spazio di indirizzi da sito a sito: è possibile usare una connessione VPN da sito a sito per connettere la rete virtuale alle risorse locali o ad altre reti virtuali. Di seguito sono riportati gli intervalli IP definiti con la connessione VPN.
* Server DNS: mostra i server DNS configurati con la rete virtuale.
* Routing degli indirizzi IP alla rete virtuale: mostra i blocchi di indirizzi instradati usati per guidare traffico nella rete virtuale 

L'unica operazione possibile nella vista app di Integrazione rete virtuale è la disconnessione dell'app dalla rete virtuale a cui è attualmente connessa. Per disconnettere l'app da una rete virtuale, selezionare **Disconnetti**. L'app verrà riavviata quando ci si disconnette da una rete virtuale. La disconnessione non modifica la rete virtuale. La rete virtuale e la relativa configurazione, inclusi i gateway, rimane invariata. Per eliminare la rete virtuale è necessario eliminare prima le risorse al suo interno, inclusi i gateway. 

Per raggiungere l'interfaccia utente di Integrazione rete virtuale del piano di servizio app, aprire l'interfaccia utente ASP e selezionare **Rete**.  In Integrazione rete virtuale selezionare **Fare clic qui per configurare** per aprire l'interfaccia utente di Stato funzionalità di rete.

![Informazioni di Integrazione rete virtuale del piano di servizio app][5]

L'interfaccia utente di Integrazione rete virtuale del piano di servizio app illustra tutte le reti virtuali usate dalle app nel piano di servizio app. È possibile connettersi fino a 5 reti virtuali con un numero qualsiasi di app nel piano di servizio app. Ogni app può avere una sola integrazione configurata. Per visualizzare i dettagli su ogni rete virtuale, fare clic sulla rete virtuale in questione. Qui è possibile eseguire due azioni.

* **Sincronizza rete**. L'operazione Sincronizza rete verifica che i certificati e le informazioni di rete siano sincronizzati. Se si aggiunge o modifica il DNS della rete virtuale, è necessario eseguire un'operazione **Sincronizza rete**. Questa operazione riavvierà qualsiasi app con questa rete virtuale.
* **Aggiungi route**. L'aggiunta di route indirizzerà il traffico in uscita nella rete virtuale.

**Routing**: le route definite in una rete virtuale vengono usate per indirizzare il traffico dall'app alla propria rete virtuale. Se è necessario inviare altro traffico in uscita nella rete virtuale, è possibile aggiungere qui i blocchi di indirizzi.   

**Certificati**: Se Integrazione rete virtuale è abilitata, è necessario uno scambio di certificati per garantire la sicurezza della connessione. Con i certificati si ottengono la configurazione DNS, le route e altre informazioni simili che descrivono la rete.
Se vengono modificati i certificati o le informazioni di rete, è necessario fare clic su "Sincronizza rete". Quando si fa clic su "Sincronizza rete", si verifica una breve interruzione della connettività tra l'app e la rete virtuale. L'app non viene riavviata, ma la perdita di connettività potrebbe causare il funzionamento non corretto del sito. 

## <a name="accessing-on-premises-resources"></a>Accesso alle risorse locali
Le app possono accedere alle risorse locali grazie all'integrazione con le reti virtuali che dispongono di connessioni da sito a sito. Per accedere alle risorse locali, è necessario aggiornare le route di gateway VPN locali con i blocchi di indirizzi da punto a sito. Quando la connessione VPN da sito a sito viene configurata per la prima volta, gli script usati per la configurazione devono configurare le route in modo appropriato. Se gli indirizzi da punto a sito vengono aggiunti dopo aver creato la VPN da sito a sito, è necessario aggiornare le route manualmente. Le informazioni dettagliate su come eseguire questa operazione variano in base al gateway e non sono descritte in questo documento. Non è possibile configurare BGP con una connessione VPN da sito a sito.

> [!NOTE]
> La funzionalità Integrazione rete virtuale non consente l'integrazione di un'app con una rete virtuale dotata di un gateway ExpressRoute. Anche se il gateway ExpressRoute è configurato in [modalità di coesistenza][VPNERCoex], Integrazione rete virtuale non funziona. Se è necessario accedere alle risorse tramite una connessione ExpressRoute, è possibile usare un [ambiente del servizio app][ASE] eseguito nella rete virtuale. 
> 
> 

## <a name="peering"></a>Peering
È possibile usare Integrazione rete virtuale per accedere alle risorse nelle reti virtuali con peering alla rete virtuale a cui si è connessi. Per configurare il peering per interagire con l'app:

1. Aggiungere una connessione di peering nella rete virtuale a cui si connette l'app. Quando si aggiunge la connessione di peering, abilitare **Consenti accesso alla rete virtuale** e selezionare **Consenti traffico inoltrato** e **Consenti transito gateway**.
1. Aggiungere una connessione di peering nella rete virtuale sottoposta a peering alla rete virtuale a cui si è connessi. Quando si aggiunge la connessione di peering nella rete virtuale di destinazione, abilitare **Consenti accesso alla rete virtuale** e selezionare **Consenti traffico inoltrato** e **Usa gateway remoti**.
1. Passare a Piano di servizio app > Rete > interfaccia utente di Integrazione rete virtuale nel portale.  Selezionare la rete virtuale a cui si connette l'app. Nella sezione Routing aggiungere l'intervallo di indirizzi della rete virtuale sottoposta a peering alla rete virtuale a cui è connessa l'app.  


## <a name="pricing-details"></a>Dettagli prezzi
L'uso della funzionalità Integrazione rete virtuale prevede tre tipi di costi:

* Requisiti del piano tariffario ASP
* Costi di trasferimento dati
* Costi del gateway VPN

Il piano di servizio delle app deve essere di tipo Standard, Premium o PremiumV2. Per altre informazioni sui costi, vedere i [prezzi del servizio app][ASPricing]. 

È previsto un addebito per i dati in uscita, anche se la rete virtuale è nello stesso data center. Gli addebiti sono descritti nei [dettagli relativi ai prezzi dei trasferimenti dei dati][DataPricing]. 

È previsto un costo per il gateway di rete virtuale necessario per la connessione VPN da punto a sito. I dettagli sono disponibili nella pagina dei [prezzi del gateway VPN][VNETPricing].

## <a name="troubleshooting"></a>risoluzione dei problemi
La funzionalità è semplice da configurare, ma possono comunque verificarsi problemi durante l'uso. In caso di problemi di accesso all'endpoint desiderato, sono disponibili varie utilità che permettono di testare la connettività dalla console dell'app. Le console disponibili sono due: la console Kudu e la console nel portale di Azure. Per accedere alla console Kudu dalla propria app, selezionare Strumenti -> Kudu. Questa operazione equivale a visitare [nomesito].scm.azurewebsites.net. Dopo l'apertura, passare alla scheda Console di debug. Per accedere alla console ospitata nel portale di Azure dalla propria app, selezionare Strumenti -> Console. 

#### <a name="tools"></a>Strumenti
Gli strumenti **ping**, **nslookup** e **tracert** non funzionano dalla console a causa di vincoli di sicurezza. Per questo motivo sono stati aggiunti due strumenti separati. Per testare la funzionalità del DNS è stato aggiunto lo strumento nameresolver.exe. La sintassi è:

    nameresolver.exe hostname [optional: DNS Server]

È possibile usare **nameresolver** per controllare i nomi host da cui dipende l'app. In questo modo è possibile verificare se la configurazione del server DNS è corretta e se si ha accesso al server DNS.

Il secondo strumento permette di verificare la connettività TCP a una combinazione di host e porta. Questo strumento viene chiamato **tcpping** la cui sintassi è:

    tcpping.exe hostname [optional: port]

L'utilità **tcpping** indica se è possibile raggiungere una porta e un host specifici. Essa visualizza il completamento solo se: è presente un'applicazione in ascolto presso la combinazione di host e porta ed è presente un accesso di rete dall'app all'host e alla porta specificati.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Accesso di debug a risorse ospitate su una rete virtuale
L'app potrebbe non riuscire a raggiungere un host e una porta specifici per una serie di motivi. Nella maggior parte dei casi il motivo è uno dei seguenti:

* **L'ostacolo è rappresentato da un firewall**. Se è presente un firewall, si verifica il timeout TCP, che in questo caso è di 21 secondi. Usare lo strumento **tcpping** per testare la connettività. Il timeout TCP può essere dovuto anche ad altri motivi, ma è consigliabile iniziare dal firewall. 
* **Il DNS non è accessibile**. Il timeout DNS è di tre secondi per ogni server DNS. Se si dispone di due server DNS, il timeout è di 6 secondi. Usare nameresolver per verificare il funzionamento del DNS. Si noti che non è possibile usare nslookup, perché non usa il DNS con cui è configurata la rete virtuale.
* **Intervallo di indirizzi da punto a sito non valido**. L'intervallo IP da punto a sito deve essere compreso tra gli intervalli IP privati RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255). Se l'intervallo usa indirizzi IP esterni a tali intervalli, allora il processo non funzionerà. 

Se il problema non rientra in nessuno dei tre casi, cercare prima di tutto le cause più semplici. Ad esempio: 

* Il gateway viene visualizzato come attivo nel portale?
* I certificati vengono visualizzati come sincronizzati?
* La configurazione di rete è stata modificata senza poi eseguire una sincronizzazione della rete nell'ASP interessato? 

Se il gateway è inattivo, è necessario riattivarlo. Se i certificati non sono sincronizzati, passare alla visualizzazione ASP dell'integrazione di reti virtuali ed eseguire la sincronizzazione della rete. Se si ritiene che sia stata apportata una modifica alla configurazione della rete virtuale non sincronizzata con gli ASP, premere "Sincronizza rete".  Un'operazione "Sincronizza rete" riavvierà tutte le app nell'ASP integrate con la rete virtuale. 

Se i quesiti precedenti non permettono di risolvere il problema, è necessario approfondire l'analisi:

* Sono presenti altre app che usano l'integrazione rete virtuale per raggiungere risorse nella stessa rete virtuale? 
* È possibile accedere alla console dell'app e usare tcpping per raggiungere altre risorse nella rete virtuale? 

Se viene soddisfatta una delle condizioni precedenti, l'integrazione di reti virtuali funziona correttamente e il problema è altrove. In tal caso, scoprire perché non è possibile raggiungere una combinazione host:porta può risultare più complesso. Tra le cause possibili:

* È presente un firewall sull'host che impedisce l'accesso alla porta dell'applicazione dall'intervallo IP da punto a sito. Il passaggio tra subnet spesso richiede l'accesso pubblico.
* L'host di destinazione è inattivo.
* L'applicazione è inattiva.
* L'IP o il nome host è errato.
* L'applicazione è in ascolto su una porta diversa da quella prevista. È possibile associare l'ID di processo alla porta di ascolto mediante "netstat -aon" nell'host dell'endpoint. 
* I gruppi di sicurezza di rete sono configurati in modo da impedire l'accesso all'host e alla porta dell'applicazione dall'intervallo IP da punto a sito.

Tenere presente che non si conosce l'IP usato dall'app nell'intervallo IP da punto a sito, quindi è necessario consentire l'accesso all'intero intervallo. 

Di seguito è riportata la procedura di debug aggiuntiva:

* connettersi a un'altra macchina virtuale nella rete virtuale e provare a raggiungere l'host:porta della risorsa da quel punto. Per verificare l'accesso al protocollo TCP usare il comando di PowerShell **test-netconnection**. La sintassi è:

      test-netconnection hostname [optional: -Port]

* visualizzare un'applicazione in un'altra macchina virtuale e testare l'accesso a quell'host e a quella porta dalla console dell'app

#### <a name="on-premises-resources"></a>Risorse locali ####

Se l'app non riesce a raggiungere una risorsa locale, verificare se è possibile raggiungere la risorsa dalla propria rete virtuale. Usare il comando di PowerShell **test-netconnection** per eseguire controllare l'accesso al protocollo TCP. Se la macchina virtuale non riesce a raggiungere la risorsa locale, verificare che la connessione VPN da sito a sito funzioni correttamente. Se la connessione funziona, eseguire gli stessi controlli descritti in precedenza, nonché la configurazione e lo stato del gateway locale. 

Se la macchina virtuale ospitata nella rete virtuale può raggiungere il sistema locale ma l'app non ci riesce, la causa è probabilmente una delle seguenti:

* le route non sono configurate con gli intervalli di IP da punto a sito nel gateway locale
* i gruppi di sicurezza della rete bloccano l'accesso all'intervallo IP da punto a sito
* i firewall locali bloccano il traffico proveniente dall'intervallo IP da punto a sito


## <a name="powershell-automation"></a>Automazione di PowerShell

È possibile integrare Servizio app con una rete virtuale di Azure tramite PowerShell. Per uno script pronto per l'esecuzione, vedere [Connect an app in Azure App Service to an Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3) (Connettere un'app del Servizio app di Azure a una rete virtuale di Azure).

## <a name="hybrid-connections-and-app-service-environments"></a>Connessioni ibride e ambienti del servizio App
Sono disponibili tre funzioni che consentono l'accesso alle risorse ospitate su reti virtuali. Sono:

* Integrazione rete virtuale
* connessioni ibride
* Ambienti del servizio app

Per le connessioni ibride è necessario installare nella rete un agente di inoltro denominato Gestione connessione ibrida. Gestione connessione ibrida deve potersi connettere ad Azure e anche all'applicazione. Connessioni ibride non richiede un endpoint in ingresso accessibile da Internet per la rete remota, a differenza di una connessione VPN. Gestione connessione ibrida può essere eseguita solo su sistemi Windows e prevede un massimo di cinque istanze in esecuzione per garantire un'elevata disponibilità. Le connessioni ibride, tuttavia, supportano solo il TCP e ogni endpoint di connessione ibrida deve corrispondere a una combinazione host:porta specifica. 

La funzionalità dell'ambiente del servizio app consente di eseguire una singola istanza tenant del Servizio app di Azure nella propria rete virtuale. Se le app si trovano in un ambiente del servizio App, le App possono accedere alle risorse nella rete virtuale senza eseguire altri passaggi. Con un ambiente del servizio app le app vengono eseguite in ruoli di lavoro più potenti e possono aumentare fino a 100 istanze ASP. Gli ambienti del servizio app sono compatibili con tutte le funzionalità di rete, tra cui endpoint servizio ed ExpressRoute.  

Nonostante ci sia una sovrapposizione del caso d'uso, nessuna di queste funzionalità può sostituire le altre. In base all'uso che se ne intende fare è possibile stabilire quali funzionalità usare. Ad esempio: 

* Per uno sviluppatore che voglia eseguire un sito di Azure e possa accedere a un database nella workstation locale, Connessioni ibride è la scelta ideale. 
* Una grande organizzazione che voglia inserire un gran numero di proprietà Web nel cloud pubblico e gestirle nella propria rete, può usare Ambiente del servizio app. 
* In caso si disponga di più app che richiedono l'accesso alle risorse nella rete virtuale, l'Integrazione rete virtuale è la scelta adatta. 

Se la rete virtuale è già connessa alla rete locale, uno dei modi più semplici per usare le risorse locali è costituito dall'uso di Integrazione rete virtuale o di un ambiente del servizio app. Se la rete virtuale non è connessa alla rete locale, è molto più complicato configurare una VPN da sito a sito con la rete virtuale piuttosto che installare Gestione connessione ibrida. 

Oltre a differenze funzionali, vanno considerate le differenze di prezzo. La funzionalità Ambiente del servizio app è un'offerta di servizio Premium che garantisce il massimo delle possibilità di configurazione di rete e altre funzionalità. La funzionalità Integrazione rete virtuale può essere usata con ASP Standard o Premium ed è ideale per l'uso sicuro delle risorse in una rete virtuale dal servizio app multi-tenant. La funzionalità Connessioni ibride dipende attualmente da un account BizTalk che prevede diversi piani tariffari, da quello gratuito a quello meno economico, a seconda della quantità richiesta. Per l'uso su più reti, tuttavia, la scelta ideale è la funzionalità Connessioni ibride, che permette di accedere a risorse in oltre 100 reti separate. 

## <a name="new-vnet-integration"></a>Nuova Integrazione rete virtuale ##

È disponibile una nuova versione della funzionalità Integrazione rete virtuale che non dipende dalla tecnologia VPN da punto a sito. A differenza della funzionalità preesistente, la nuova funzionalità di anteprima è compatibile con ExpressRoute e gli endpoint servizio. 

La nuova funzionalità è disponibile solo nelle più recenti unità di scala del servizio app di Azure. Se è possibile passare a PremiumV2, si accede a un'unità di scala più recente del Servizio app. L'interfaccia utente di Integrazione rete virtuale nel portale indicherà se l'app può usare la nuova funzionalità Integrazione rete virtuale. 

La nuova versione è disponibile in anteprima e presenta le caratteristiche seguenti.

* Per usare la nuova funzionalità Integrazione rete virtuale non è richiesto alcun gateway
* È possibile accedere alle risorse attraverso le connessioni ExpressRoute senza alcuna configurazione aggiuntiva oltre all'integrazione con la rete virtuale connessa a ExpressRoute.
* L'app e la rete virtuale devono essere nella stessa area
* La nuova funzionalità richiede una subnet inutilizzata nella rete virtuale di Resource Manager.
* Il piano di servizio app deve essere un piano Standard, Premium o PremiumV2
* I carichi di lavoro di produzione non sono supportati nella nuova funzionalità in anteprima
* L'app deve essere in una distribuzione del Servizio app di Azure in grado di passare a Premium v2.
* La nuova funzionalità Integrazione rete virtuale non funziona per le app in un ambiente del servizio app.
* Non è possibile eliminare una rete virtuale con un'app integrata.  
* Le tabelle di route e il peering globale non sono ancora disponibili con la nuova funzionalità Integrazione rete virtuale.  
* Viene usato un indirizzo per ogni istanza del piano di servizio app. Poiché le dimensioni della subnet non possono essere modificate dopo l'assegnazione, usare una subnet che può coprire abbondantemente le dimensioni massime di scalabilità. Un /27 con 32 indirizzi è la dimensione consigliata per poter contenere un piano di servizio app ridimensionato a 20 istanze.  È possibile usare le risorse protette dell'endpoint servizio usando la nuova funzionalità Integrazione rete virtuale. A tale scopo, abilitare gli endpoint servizio nella subnet usata per Integrazione rete virtuale.

Per usare la nuova funzionalità:

1. Passare all'interfaccia utente di Rete nel portale. Se l'app è in grado di usare la nuova funzionalità, risulterà possibile usarla in anteprima.  

 ![Selezionare la nuova funzionalità Integrazione rete virtuale in anteprima][6]

1. Selezionare **Aggiungi rete virtuale (anteprima)**.  

1. Selezionare la rete virtuale di Resource Manager da integrare e creare una nuova subnet o sceglierne una vuota esistente. Il completamento dell'integrazione richiede meno di un minuto. Durante l'integrazione, l'app viene riavviata.  Al termine dell'integrazione verranno visualizzati i dettagli della rete virtuale da integrare e un banner in alto indicante che la funzionalità è in anteprima.

 ![Selezionare la rete virtuale e la subnet][7]

Per abilitare l'app per l'uso del server DNS con cui è stata configurata la rete virtuale, creare un'impostazione Applicazione per l'app in cui il nome è WEBSITE_DNS_SERVER e il valore è l'indirizzo IP del server.  In presenza di un server DNS secondario, creare un'altra impostazione Applicazione in cui il nome è WEBSITE_DNS_ALT_SERVER e il valore è l'indirizzo IP del server. 

Per disconnettere l'app dalla rete virtuale, selezionare **Disconnetti**. L'app Web verrà riavviata. 

La nuova funzionalità Integrazione rete virtuale consente di usare gli endpoint servizio.  Per usarli con l'app, usare la nuova funzionalità Integrazione rete virtuale per connettersi a una rete virtuale selezionata e configurare gli endpoint servizio nella subnet usata per l'integrazione. 

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
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: http://portal.azure.com/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: http://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: http://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#gatewaysubnet
[creategateway]: http://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: http://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
